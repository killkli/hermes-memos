---
title: OAuth Integration in Non-Interactive Environments
created: 2026-04-16
updated: 2026-04-16
type: concept
tags: [oauth, automation, cron, integration, security]
sources: [raw/articles/cron-youtube-upload-session-2026-04-16.md]
---

# OAuth Integration in Non-Interactive Environments

## Definition

在沒有瀏覽器、無法進行人類互動的自動化環境（Cron、execute_code、server-side）中，如何使用需要 OAuth 2.0 授權的 API（如 YouTube Data API、Google Drive 等）。

## The Core Challenge

OAuth 2.0 標準 flow 都需要某種形式的人類互動：

| Flow | 人機互動需求 | 適用環境 |
|------|-------------|---------|
| Authorization Code（標準） | 需要瀏覽器登入 1 次 | 有 GUI 的桌面/伺服器 |
| Device Code Flow | 需要在另一設備輸入驗證碼 | 自動化伺服器（推薦） |
| Service Account | 完全不需要互動 | Google Workspace 組織帳號 |
| Refresh Token | 需要先完成一次完整 flow | Cron/自動化（搭配 token 持久化） |

## The Solution: Pre-Authorization + Token Refresh

```
步驟 1（一次性）：在有瀏覽器的環境完成 OAuth flow
         ↓
步驟 2：將 token（含 refresh_token）存到磁碟
         ↓
步驟 3（Cron 執行時）：從磁碟讀取 token，
                       檢查是否過期，
                       如需要自動 refresh
         ↓
步驟 4：使用有效的 credentials 呼叫 API
```

## Token Persistence Pattern

```python
import json, datetime, os

TOKEN_FILE = os.path.expanduser("~/.hermes/youtube/channels.json")

# 讀取 token
with open(TOKEN_FILE) as f:
    data = json.load(f)

d = data["@TWMUSIC-k5z"]["token"]

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

# 檢查並自動 refresh
if not creds.valid:
    if creds.expired and creds.refresh_token:
        creds.refresh(Request())
        # 可選：把 refresh 後的 token 寫回磁碟
        with open(TOKEN_FILE, "w") as f:
            json.dump(data, f)
```

## Device Code Flow for Brand Accounts

**Brand Account（如 @TWMUSIC-k5z）無法用標準 Authorization Code Flow 正確選擇頻道**，因為瀏覽器 session 綁定主要 Google 帳號。

Device Code Flow 是解決方案：

```python
# ~/.hermes/youtube/authorize.py 內的實作邏輯（精簡版）

import requests, time, json, os
from google_auth_oauthlib.flow import Flow

# Step 1: 取得驗證碼
device_resp = requests.post(
    "https://oauth2.googleapis.com/device/code",
    data={"client_id": CLIENT_ID, "scope": " ".join(SCOPES)}
).json()

print(f"請拜訪: {device_resp['verification_url']}")
print(f"輸入驗證碼: {device_resp['user_code']}")

# Step 2: Polling 等待授權完成
while True:
    token_resp = requests.post(
        "https://oauth2.googleapis.com/token",
        data={
            "client_id": CLIENT_ID,
            "client_secret": CLIENT_SECRET,
            "code": device_resp["device_code"],
            "grant_type": "http://oauth.net/grant_type/device/1.0"
        }
    ).json()
    if "access_token" in token_resp:
        break
    time.sleep(device_resp.get("interval", 5))

# Step 3: 存 token
with open(CHANNELS_FILE) as f:
    channels = json.load(f)
channels["@TWMUSIC-k5z"] = {
    "name": "@TWMUSIC-k5z",
    "token": token_resp
}
with open(CHANNELS_FILE, "w") as f:
    json.dump(channels, f, indent=2)
```

## Cron 環境的 OAuth Checklist

```
□ OAuth 預先授權（使用 --device-flow）
□ Token 有效且有 refresh_token
□ cron venv 已安裝所需 pip packages（google-auth 等）
□ Python code 使用 os.path.expanduser() 展開路徑
□ OAuth token 檔案路徑正確（相對於 cron venv 的 home）
□ 實作自動 refresh 邏輯（cred.expired 時自動刷新）
```

## Security Considerations

1. **Token 檔案包含 refresh_token**：相當於長期有效憑證，檔案本身需要保護（`chmod 600`）
2. **client_secret 不要寫進 code**：從 `client_secrets.json` 讀取，不 hardcode
3. **最小化 scope**：只申請需要的 OAuth scopes，不要 `offline access` 除非必要
4. **定期更換**：refresh_token 有時效，注意 OAuth 應用程式的 token 政策

## Related Concepts

- [Agent Environments](/hermes-memos/concepts/agent-environments/) — Why non-interactive environments need different patterns
- [Skill vs Codeblock](/hermes-memos/concepts/skill-vs-codeblock/) — Self-contained code for non-interactive environments
- [YouTube Upload Patterns](/hermes-memos/concepts/youtube-upload-patterns/) — Concrete application of OAuth patterns
- [Taiwan Daily Music Pipeline](/hermes-memos/concepts/taiwan-daily-music-pipeline/) — Real cron job using OAuth token refresh pattern
- [Multi-Agent Stock Analysis](/hermes-memos/concepts/multi-agent-stock-analysis-system/) — Cron ≠ Agent lesson applied to stock analysis
