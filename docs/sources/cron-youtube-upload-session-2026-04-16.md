# Raw Source: Cron YouTube Upload Integration Session

> Date: 2026-04-16
> Session: Telegram DM with John Chen
> Jobs modified: e6a6121db141 (台灣每日搞笑RAP), 39a889ca7a7a (台灣每日抒情歌)
> Skills involved: minimax-music, minimax-image, youtube-upload

## Context

John Chen had two daily cron jobs creating AI-generated Taiwanese music videos:
1. 台灣每日搞笑RAP (e6a6121db141) - comedic daily rap based on Taiwan news
2. 台灣每日抒情歌 (39a889ca7a7a) - warm daily ballad based on positive Taiwan stories

Both jobs already had `skills: ['minimax-music', 'minimax-image', 'web']` and produced MP4 videos. The goal was to add automatic YouTube upload to @TWMUSIC-k5z after video generation.

## The Problem

The original prompt for RAP job included:
```python
import sys
sys.path.insert(0, os.path.expanduser('~/.hermes/skills/media/youtube-upload/scripts'))
from youtube_upload import get_authenticated_service, resumable_upload
```

And the 抒情歌 job lacked `youtube-upload` in its skills list entirely.

## Root Cause Analysis

Three distinct environments exist in Hermes:
1. **Agent** (normal chat): Tool calls work, skill tools registered in Hermes registry
2. **Cron** (isolated Python venv): Bare Python session, NO tool registry, skills field meaningless
3. **execute_code** (sandbox): Same as cron - isolated Python, no tool access

The error was assuming `skills: ['youtube-upload']` in a cron job meant the skill would be available. It doesn't - skills only matter for agent tool routing.

## Solution Applied

1. Installed google-auth packages into cron venv:
   `~/.hermes/cron/venv/bin/pip install google-auth google-auth-oauthlib google-api-python-client`

2. Rewrote both cron job prompts to use self-contained YouTube upload code (complete OAuth + API logic embedded in the Python code block, reading from `channels.json`)

3. Added `youtube-upload` to 抒情歌 job's skills list

## Key Insight

> The `skills` field in a cron job is purely decorative for agent-mode documentation. It has NO effect on what the cron Python interpreter can access. Always use self-contained code blocks for API integrations in cron jobs.

## Files Modified

- `~/.hermes/cron/jobs.json` - both job prompts rewritten
- `~/.hermes/cron/venv/` - google-api packages installed

## Skills Created

- `cron-youtube-upload-integration` - documents the complete pattern
- `youtube-upload` SKILL.md updated - added "Cron cannot use skill import" warning
