---
title: Cron Job Architecture
created: 2026-04-16
updated: 2026-04-22
type: concept
tags: [cron, architecture, automation, hermes]
sources: [session:20260416_085803, raw/articles/cron-job-debug-session-2026-04-18.md, raw/articles/taiwan-daily-music-cron-2026-04-22.md]
---

# Cron Job Architecture

## Overview

How the Hermes cron system is structured — job registration, venv isolation, skill loading, and the distinction between skill imports vs self-contained code.

## System Structure

```
~/.hermes/cron/
├── jobs.db              # SQLite job registry
├── output/              # Generated files from cron jobs
└── (per-job log files)
```

Jobs are registered via `cronjob` tool (action='create'). Each job has:
- `job_id` — unique identifier
- `prompt` — self-contained task instruction
- `skills` — ordered list of skills to load before running
- `schedule` — cron expression or interval
- `model` — optional per-job model override

## Cron ≠ Agent

The most critical architectural distinction:

| | Agent | Cron |
|--|-------|------|
| Skill imports | ✅ Work | ❌ Don't work |
| Tool registry | ✅ Full access | ❌ Not available |
| Hermes env vars | ✅ Available | ❌ Sandbox |
| OAuth token path | `~/.hermes/...` | Uses cron venv's home |

### Why Skills Don't Work in Cron

When a cron job runs, it executes in a fresh Python process. The `sys.path` does NOT include `~/.hermes/skills/`. The Hermes tool registry is not initialized. Therefore:

```python
# ❌ WRONG — these all fail in cron
from hermes_tools import terminal, read_file
from my_skill import some_function
import hermes_skills

# ✅ CORRECT — self-contained code
import subprocess, json, os
# All logic inline, no external imports from hermes/
```

## Skill Loading in Cron

The `skills` parameter on cron creation DOES work — it loads skill contents into the prompt context before execution. But it does NOT make the skill's Python code importable, and **it silently fails if the skill doesn't exist**.

```python
# ⚠️ CRITICAL: Non-existent skills are SILENT failures
# If "web" is listed in skills but doesn't exist, you get:
#   ⚠️ Skill(s) not found and skipped: web
# The job continues but the expected skill content is missing.

# Always verify skills exist before adding to cron:
# skills=["youtube-upload"] ✅ exists
# skills=["web"] ❌ does NOT exist — don't add it
```

## File Paths in Cron

Cron venv has a different `$HOME`. Always use:

```python
# ❌ WRONG
path = "~/.hermes/youtube/channels.json"
data = open(path).read()  # Opens relative to cwd, not ~

# ✅ CORRECT
path = os.path.expanduser("~/.hermes/youtube/channels.json")
data = open(path).read()
```

## Delivery

Cron jobs can deliver results to:
- `"origin"` — back to the chat where the job was created
- `"local"` — save to `~/.hermes/cron/output/` only, no delivery
- `"telegram"` — send to home channel
- `"platform:chat_id:thread_id"` — explicit targeting

## Iteration Limit Lesson (2026-04-18)

The full 11-step music pipeline (news → jury vote → lyrics → cover → music → video → Telegram → YouTube) can exceed the default iteration limit (50 messages).

**Symptoms:** Job stalls at message 38/50, partial output produced but incomplete.

**Fix:** Verify/adjust cron runner `max_iterations` config. The 11-step pipeline needs headroom — set to 80+ or as needed.

## Telegram .env Reading (2026-04-22)

Cron's `~/.vcenvs` virtual environment credentials are **masked** — bot token and chat_id return as `***`. Do NOT rely on `~/.vcenvs` for sensitive credentials in cron.

```python
# ❌ WRONG — masked in cron venv
import os
token = os.environ.get("TELEGRAM_BOT_TOKEN")  # None or ***

# ✅ CORRECT — read from actual .env
import subprocess
result = subprocess.run(['bash', '-c', 'source /home/devilchen/.hermes/.env && echo $TELEGRAM_BOT_TOKEN'],
                       capture_output=True, text=True)
token = result.stdout.strip()
```

## YouTube Import Path in Cron (2026-04-22)

`sys.path.insert` does NOT work in bare Python/execute_code environments. YouTube upload skill's `youtube_upload.py` must be imported via **absolute path**:

```python
# ❌ WRONG
import sys
sys.path.insert(0, os.path.expanduser("~/.hermes/skills/media/youtube-upload/scripts"))
from youtube_upload import upload_video  # ModuleNotFoundError

# ✅ CORRECT
import importlib.util
spec = importlib.util.spec_from_file_location(
    "youtube_upload",
    os.path.expanduser("~/.hermes/skills/media/youtube-upload/scripts/youtube_upload.py")
)
youtube_upload = importlib.util.module_from_spec(spec)
spec.loader.exec_module(youtube_upload)
```

Alternatively, use `subprocess` to call the script directly with all arguments passed via command line.

## Related Concepts

- [Agent Environments](/hermes-memos/concepts/agent-environments/) — Agent vs cron vs execute_code environments
- [Skill vs Codeblock](/hermes-memos/concepts/skill-vs-codeblock/) — Self-contained code for cron environments
- [YouTube Upload Patterns](/hermes-memos/concepts/youtube-upload-patterns/) — YouTube upload in cron (self-contained OAuth pattern)
- [Taiwan Daily Music Pipeline](/hermes-memos/concepts/taiwan-daily-music-pipeline/) — Real cron job using these patterns
- [Multi-Agent Stock Analysis](/hermes-memos/concepts/multi-agent-stock-analysis-system/) — How to run the stock analysis system as a cron
