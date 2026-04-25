---
title: YouTube Upload Patterns
created: 2026-04-16
updated: 2026-04-22
type: concept
tags: [youtube, oauth, integration, automation, cron]
sources: [raw/articles/cron-youtube-upload-session-2026-04-16.md, raw/articles/taiwan-daily-music-cron-2026-04-22.md]
---

# YouTube Upload Patterns

## Definition

使用 YouTube Data API v3 進行影片上傳的兩種主要情境模式：
1. **Agent 模式**：在正常對話中，使用 `youtube-upload` skill tool
2. **Cron 模式**：在自動化環境中，直接內嵌 API 邏輯

## The Fundamental Problem

YouTube Data API v3 需要 OAuth 2.0 授權。標準 OAuth flow 需要瀏覽器互動，但自動化環境（cron、execute_code）沒有瀏覽器。

**解決方案**：預先授權 + token 持久化。

## OAuth Token Architecture

```
~/.hermes/youtube/
├── client_secrets.json      # Google Cloud OAuth 2.0 憑證（所有頻道共用）
├── channels.json             # 多頻道 token 統一儲存（推薦方式）
└── token_@CHANNEL_ID.json   # 各頻道獨立 token（舊版 fallback）
```

### channels.json 結構

```json
{
  "@TWMUSIC-k5z": {
    "name": "@TWMUSIC-k5z",
    "token": {
      "token": "ya29.xxx",
      "refresh_token": "1//xxx",
      "token_uri": "https://oauth2.googleapis.com/token",
      "client_id": "xxx.apps.googleusercontent.com",
      "client_secret": "GOCSPX-xxx",
      "scopes": ["https://www.googleapis.com/auth/youtube.upload", "..."],
      "expiry": "2026-04-17T08:00:00+08:00"
    }
  }
}
```

## Agent Mode: Using the Skill Tool

當在正常 agent 對話中需要 YouTube 上傳：

```python
# Hermes 自動路由到 youtube_upload tool
# 邏輯在 ~/.hermes/skills/media/youtube-upload/scripts/youtube_upload.py
```

**所需步驟**：
1. 確認 `~/.hermes/youtube/channels.json` 存在且 token 有效
2. 確認 cron venv 已安裝：`pip install google-auth google-auth-oauthlib google-api-python-client`
3. 呼叫 `youtube_upload` tool（agent 環境自動路由）

## Cron Mode: Self-Contained Code

在 cron job 或 execute_code 環境中，**完整內嵌 OAuth + 上傳邏輯**：

```python
import json, datetime, os
import google.auth
from google.auth.transport.requests import Request
from google.oauth2.credentials import Credentials
from googleapiclient.discovery import build
from googleapiclient.http import MediaFileUpload
from googleapiclient.errors import HttpError

CHANNEL_ID = "@TWMUSIC-k5z"
CHANNELS_FILE = os.path.expanduser("~/.hermes/youtube/channels.json")

def get_youtube_service(channel_id):
    """從 channels.json 讀取 token，回傳 YouTube service"""
    creds = None
    if os.path.exists(CHANNELS_FILE):
        with open(CHANNELS_FILE) as f:
            channels = json.load(f)
        if channel_id in channels:
            d = channels[channel_id]["token"]
            creds = Credentials(
                token=d.get("token"),
                refresh_token=d.get("refresh_token"),
                token_uri=d.get("token_uri"),
                client_id=d.get("client_id"),
                client_secret=d.get("client_secret"),
                scopes=d.get("scopes")
            )
            if d.get("expiry"):
                creds.expiry = datetime.datetime.fromisoformat(d["expiry"])

    if not creds or not creds.valid:
        if creds and creds.expired and creds.refresh_token:
            creds.refresh(Request())
        else:
            print("[YouTube] 無有效 token，跳過上傳")
            return None
    return build("youtube", "v3", credentials=creds)

def upload_video(youtube, file_path, title, description, tags,
                 category_id="10", privacy="public"):
    """續傳上傳影片到 YouTube"""
    body = {
        "snippet": {
            "title": title,
            "description": description,
            "tags": tags,
            "categoryId": str(category_id)
        },
        "status": {
            "privacyStatus": privacy,
            "selfDeclaredMadeForKids": False
        }
    }
    media = MediaFileUpload(file_path, chunksize=10*1024*1024, resumable=True)
    request = youtube.videos().insert(part="snippet,status", body=body, media_body=media)
    response = None
    while response is None:
        try:
            status, response = request.next_chunk()
            if status:
                print(f"  YouTube 上傳中: {int(status.progress()*100)}%", flush=True)
        except HttpError as e:
            if e.resp.status in [500, 502, 503, 504]:
                continue
            raise
    return response

# 主程式
youtube = get_youtube_service(CHANNEL_ID)
if youtube:
    resp = upload_video(youtube, video_path, title="...", description="...", tags=[...])
    print(f"上傳成功！ID: {resp['id']}")
```

## Brand Account 注意事項

如果目標頻道是 **Brand Account**（如 `@TWMUSIC-k5z`），標準 `InstalledAppFlow` 無法正確選擇頻道，會預設使用主要 Google 帳號的個人頻道。

**解決**：使用 Device Code Flow：

```bash
# 一次性預先授權
python3 ~/.hermes/youtube/authorize.py --channel-id @TWMUSIC-k5z --device-flow
```

Device Code Flow 會顯示驗證碼，讓你在瀏覽器裡完整選擇要授權的帳號與頻道。

## Cron venv 依賴管理

Cron 環境使用獨立 venv，**每次新增需要外部 pip packages 的工作前**必須安裝：

```bash
~/.hermes/cron/venv/bin/pip install google-auth google-auth-oauthlib google-api-python-client
```

**驗證方式**：
```bash
~/.hermes/cron/venv/bin/pip list | grep google
```

## MiniMax `output_format=url` Broken (2026-04-22)

MiniMax API 的 `output_format=url` 會返回 OSS 簽名 URL，但 URL 中的 `Signature=***` 被 Masked，導致 URL 無法使用。

**Solution:** 使用 `output_format=hex` + `bytes.fromhex()` 轉換：

```python
# ❌ WRONG — URL has masked signature
resp = requests.get(url, params={"output_format": "url"})
audio_url = resp.json()["data"]["audio_url"]  # 403 / invalid

# ✅ CORRECT — use hex format
resp = requests.get(url, params={"output_format": "hex"})
audio_hex = resp.json()["data"]["audio_binary"]
audio_bytes = bytes.fromhex(audio_hex)
with open("output.mp3", "wb") as f:
    f.write(audio_bytes)
```

## Common Failure Modes

| 徵兆 | 原因 | 解決 |
|------|------|------|
| `ModuleNotFoundError: google.auth` | cron venv 未安裝依賴 | `pip install` 到 cron venv |
| `FileNotFoundError: ~/.hermes/youtube/...` | JSON 內 `~` 未 shell 展開 | 用 `os.path.expanduser()` |
| 上傳到錯誤頻道 | Brand Account 未用 `--device-flow` | 重新授權 |
| `Token valid: False` | Token 過期且無法刷新 | 重新 OAuth 授權 |
|| `import sys; sys.path.insert` 無效 | Cron 不是 agent 環境 | 改用 self-contained code block |
|| `output_format=url` → 403 | OSS signature 被 masked | 用 `output_format=hex` + `bytes.fromhex()` |
|| `~/.vcenvs` credentials masked | Cron venv 隔離 | 從 `/home/devilchen/.hermes/.env` 讀取 |

## Related Concepts

- [Agent Environments](/hermes-memos/concepts/agent-environments/) — Why cron ≠ agent and tool access differs
- [Skill vs Codeblock](/hermes-memos/concepts/skill-vs-codeblock/) — When to use skill tools vs embedded code
- [OAuth Integration](/hermes-memos/concepts/oauth-integration/) — OAuth patterns for non-interactive environments
