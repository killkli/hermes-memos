---
title: Taiwan Daily Music Production Pipeline
created: 2026-04-16
updated: 2026-04-22
type: concept
tags: [music, taiwan, automation, cron, youtube]
sources: [session:20260416_085803, session:20260416_095521, session:cron_e6a6121db141, raw/articles/cron-job-debug-session-2026-04-18.md, raw/articles/taiwan-daily-music-cron-2026-04-22.md]
---

# Taiwan Daily Music Production Pipeline

## Overview

Two automated daily cron jobs (08:00) generating Taiwanese-themed music content and uploading to YouTube.

## Pipeline Architecture

```
08:00 Cron Trigger
    ├── 台灣每日搞笑RAP (e6a6121db141)
    │   ├── MiniMax API → generate rap lyrics + audio
    │   └── YouTube upload (TWMUSIC-k5z channel)
    │
    └── 台灣每日抒情歌 (39a889ca7a7a)
        ├── MiniMax API → generate ballad + audio
        └── YouTube upload (TWMUSIC-k5z channel)
```

## Cron Job Configuration

- **Schedule:** Daily at 08:00
- **Model:** MiniMax API + ffmpeg
- **Output:** `~/.hermes/cron/output/`
- **YouTube channel:** @TWMUSIC-k5z
- **OAuth:** `~/.hermes/youtube/channels.json`

## Key Technical Patterns

### MiniMax Hex Format (Critical Fix — 2026-04-22)

MiniMax API 的 `output_format=url` 返回的 OSS URL，`Signature=***` 被 masked，導致下載失敗。正確做法是用 `output_format=hex`：

```python
# ❌ WRONG
resp = requests.get(url, params={"output_format": "url"})
audio_url = resp.json()["data"]["audio_url"]  # Signature=*** → 403

# ✅ CORRECT
resp = requests.get(url, params={"output_format": "hex"})
audio_bytes = bytes.fromhex(resp.json()["data"]["audio_binary"])
```

### YouTube Upload in Cron Context

⚠️ **Critical:** Skill imports (`from hermes_tools import ...`) do NOT work in bare Python cron environments. Must use absolute path via `importlib.util` or `subprocess`.

```python
# ✅ CORRECT — absolute import path
import importlib.util
spec = importlib.util.spec_from_file_location(
    "youtube_upload",
    os.path.expanduser("~/.hermes/skills/media/youtube-upload/scripts/youtube_upload.py")
)
youtube_upload = importlib.util.module_from_spec(spec)
spec.loader.exec_module(youtube_upload)
```

### Telegram .env Reading

`~/.vcenvs` credentials are masked in cron. Read from `/home/devilchen/.hermes/.env` via bash subprocess.

See: [YouTube Upload Patterns](/hermes-memos/concepts/youtube-upload-patterns/)

### Song Generation

- **Lyric style:** Taiwanese Hokkien/Mandarin mix, authentic cultural references (not superficial)
- **User preference:** Rejects 表演型 (performative) lyrics — demands genuine partnership, sincerity
- **MiniMax music-2.6 model** for audio generation

## Files & Paths

| File | Purpose |
|------|---------|
| `~/.hermes/scripts/` | Cron helper scripts |
| `~/.hermes/cron/output/` | Generated MP3/MP4 files |
| `~/.hermes/youtube/channels.json` | OAuth credentials |

## Missing Configuration

- `TELEGRAM_BOT_TOKEN` — for cron completion notifications
- `TELEGRAM_CHAT_ID` — delivery target for notifications

## Related Concepts

- [YouTube Upload Patterns](/hermes-memos/concepts/youtube-upload-patterns/) — YouTube API v3 integration in agent vs cron modes
- [Cron Job Architecture](/hermes-memos/concepts/cron-job-architecture/) — Cron system design
- [Agent Environments](/hermes-memos/concepts/agent-environments/) — Cron ≠ Agent execution context
