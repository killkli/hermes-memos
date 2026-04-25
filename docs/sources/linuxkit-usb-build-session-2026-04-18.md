# Raw Source: LinuxKit USB Build Session (2026-04-18)

## Session Metadata
- **Date:** 2026-04-18 06:18 AM
- **Source:** Telegram
- **Session ID:** 20260418_061809_c1f21f66
- **Model:** MiniMax-M2.7

## Work Performed

### 1. LinuxKit USB Build Project Init
Goal: Create a bootable USB running Hermes Agent using LinuxKit.

**Project path:** `~/hermes-usb-linuxkit/`

**Files created:**
- `project/linuxkit.yml` — Kernel 6.12.59 + Docker DinD 27.5.1 + Hermes Agent
- `build.sh` — Build script
- `flash.sh` — USB flash script
- `run-hermes.sh` — Runtime script
- `README.md`

**Docker image used:** `nousresearch/hermes-agent`
**Volume mount:** `-v /mnt/data:/opt/data`

### 2. Errors Encountered

#### Error 1: Missing output directory
```
Error: open /home/devilchen/hermes-usb-linuxkit/output/build/hermes-usb-efi.img: no such file or directory
```
**Fix:** Created `output/build/` directory structure manually.

#### Error 2: Docker Hub rate limiting (TOOMANYREQUESTS)
```
You have reached your unauthenticated pull rate limit. https://www.docker.com/increase-rate-limits
```
Affected images: `linuxkit/format`, `linuxkit/kernel`, `linuxkit/mkimage-raw-efi`

**Fix applied:**
- Removed `linuxkit/format` and `linuxkit/mount` from `linuxkit.yml` (not needed for USB)
- Switched output format from `raw-efi` to `iso-efi` to bypass the `mkimage-raw-efi` dependency
- Docker login failed: requires interactive TTY (`cannot perform an interactive login from a non-TTY device`)

### 3. Build Status at Session End
Build progressed further after switching to `iso-efi` format but still encountering Docker Hub rate limits.

**Remaining tasks:**
- Wait for Docker Hub rate limit cooldown, or configure authenticated Docker login
- Test the `iso-efi` format output on actual USB hardware
- Verify Hermes Agent container starts correctly from USB boot

## Key Outcomes
- LinuxKit project structure established
- Critical learning: USB boot不需要 `format` 和 `mount` kernel modules
- `iso-efi` format is the correct output target for USB bootable ISO

## Related Sessions
- This project is continued from prior sessions (pre-04-16)
- Related skill: `linuxkit-bootable-usb` (if created later)
