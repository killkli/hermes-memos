---
title: Skill vs Code Block
created: 2026-04-16
updated: 2026-04-16
type: concept
tags: [skill, agent, cron, pattern]
sources: [raw/articles/cron-youtube-upload-session-2026-04-16.md]
---

# Skill vs Code Block

## Definition

在 Hermes Agent 環境中，「使用一個功能」有兩種根本不同的方式：

1. **Skill Tool**：透過 Hermes tool registry 呼叫已註冊的 tool（例：`youtube_upload` tool）
2. **Code Block**：在 prompt 裡寫 Python 程式碼，交由對應環境（Agent/Cron/execute_code）自己執行

## Decision Tree

```
你要做什麼？
│
├─ 在「正常對話」中操作？
│   └─ 是 → 可以用 Skill Tool
│       └─ 這個 skill 有對應的 tool 嗎？
│           ├─ 有 → 使用 skill tool（最乾淨）
│           └─ 沒有，但 skill 有 scripts/ → sys.path.insert + import 可以
│
├─ 在「Cron job」中操作？
│   └─ 是 → 必須用 Self-contained Code Block
│       └─ 邏輯是否已經在 cron prompt 的 code block 裡？
│           ├─ 是 → 完美，直接執行
│           └─ 否 → 把邏輯完整內嵌進去
│
└─ 在「execute_code」中操作？
    └─ 是 → 必須用 Self-contained Code Block
        └─ 環境變數需要用 subprocess 繞道讀取
```

## Skill Tool Pattern

**何時使用**：在 agent 正常對話中，且該功能有對應的 Hermes tool。

```python
# Agent 對話中：直接呼叫 tool
# 由 Hermes tool registry 路由到對應 handler
result = youtube_upload(tool_arg)
```

優點：
- 宣告式，乾淨
- AI model 知道可用哪些工具
- 邏輯維護在 skill 端，prompt 不會膨脹

缺點：
- 只適用於 agent 環境
- 需要 skill 有對應的 tool registered

## Self-Contained Code Block Pattern

**何時使用**：Cron job、execute_code sandbox、或任何需要完整 SDK 邏輯但 skill tool 不可用的場景。

```python
# 在 cron prompt 或 execute_code 的 Python code block 中
import json, datetime, os
import google.auth
from google.auth.transport.requests import Request
from google.oauth2.credentials import Credentials
from googleapiclient.discovery import build
from googleapiclient.http import MediaFileUpload
from googleapiclient.errors import HttpError

# 完整的邏輯都寫在這裡，不依賴任何外部 import
CHANNELS_FILE = os.path.expanduser("~/.hermes/youtube/channels.json")

def get_youtube_service(channel_id):
    ...

def upload_video(youtube, file_path, ...):
    ...
```

優點：
- 完全控制，不依賴環境特性
- 任何環境都能執行
- OAuth token 可直接讀取 `channels.json`

缺點：
- Prompt 會變長（內嵌完整 SDK 邏輯）
- 需要重複邏輯時維護成本高
- 必須自己處理錯誤

## The Skill Import Trap

```python
# ❌ 這樣寫在 cron prompt 的 code block 裡是錯的
import sys
sys.path.insert(0, os.path.expanduser('~/.hermes/skills/media/youtube-upload/scripts'))
from youtube_upload import get_authenticated_service, resumable_upload

youtube = get_authenticated_service(channel_id='@TWMUSIC-k5z')
```

**為什麼錯**：
1. `sys.path.insert` 只影響當前 Python process，但 cron 的 Python 不是從 agent 環境繼承
2. 即使 path 正確，`get_authenticated_service` 依賴的 OAuth flow 需要瀏覽器互動，cron 環境做不到
3. Cron 環境根本沒有 Hermes tool registry，`skill import` 沒有任何額外意義

## Environment vs Approach Matrix

| 環境 | Skill Tool (`youtube_upload` tool) | Code Block (內嵌邏輯) |
|------|-----------------------------------|----------------------|
| Agent 對話 | ✅ 可用 | ✅ 可用 |
| Cron job | ❌ skill tool 不存在 | ✅ 唯一正確做法 |
| execute_code | ❌ skill tool 不存在 | ✅ 唯一正確做法 |

## Key Rule

> **When in doubt, use self-contained code.** If the code is going into a cron job prompt or execute_code block, always embed the complete logic. Never assume a skill tool is available outside of agent conversation.

## Related Concepts

- [Agent Environments](/hermes-memos/concepts/agent-environments/) — Three environments and their tool access differences
- [OAuth Integration](/hermes-memos/concepts/oauth-integration/) — OAuth patterns for non-interactive environments
- [YouTube Upload Patterns](/hermes-memos/concepts/youtube-upload-patterns/) — Concrete YouTube integration patterns
- [Multi-Agent Stock Analysis](/hermes-memos/concepts/multi-agent-stock-analysis-system/) — How these environment patterns affect the stock analysis pipeline
- [Multi-Agent Stock Analysis](/hermes-memos/concepts/multi-agent-stock-analysis-system/)
- [Taiwan Daily Music Pipeline](/hermes-memos/concepts/taiwan-daily-music-pipeline/)
- [Reflection Session 2026-04-16](/hermes-memos/concepts/reflection-session-2026-04-16/) — Synthesis of cross-session patterns and outstanding items
