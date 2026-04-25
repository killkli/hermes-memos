---
title: LinuxKit Bootable USB
created: 2026-04-22
updated: 2026-04-22
type: concept
tags: [hermes, architecture, automation, pitfall]
sources: [raw/articles/linuxkit-usb-build-session-2026-04-18.md]
---

# LinuxKit Bootable USB

## Overview

Building a bootable USB drive that runs Hermes Agent using LinuxKit. Project path: `~/hermes-usb-linuxkit/`.

> Goal: Boot any x86_64 machine from USB and have Hermes Agent running via Docker.

## Architecture

```
USB Drive (ISO boot)
└── LinuxKit OS
    ├── Docker DinD (Docker-in-Docker)
    └── nousresearch/hermes-agent container
        └── -v /mnt/data:/opt/data (persistent data)
```

## Project Structure

```
~/hermes-usb-linuxkit/
├── project/
│   └── linuxkit.yml          # LinuxKit manifest
├── output/                   # Built images
├── build.sh                  # Build script
├── flash.sh                  # USB flash script
├── run-hermes.sh             # Runtime script
└── README.md
```

## linuxkit.yml Core Config

```yaml
kernel:
  image: linuxkit/kernel:6.12.59
  cmdline: "console=tty0 console=ttyS0"

init:
  - linuxkit/initramfs:v0.12

services:
  - name: docker
    image: docker:dind
    capabilities:
      - CAP_SYS_ADMIN
    binds:
      - /var/run/docker.sock:/var/run/docker.sock
  - name: hermes
    image: nousresearch/hermes-agent
    volumes:
      - /mnt/data:/opt/data
    capabilities:
      - CAP_SYS_ADMIN
```

## Key Lessons (2026-04-18)

### USB Boot Does NOT Need `format` or `mount` kernel modules

These packages are needed for advanced LinuxKit use cases (custom partitioning, filesystem creation) but are **not needed for simple USB ISO boot**.

**Removed from linuxkit.yml:**
```yaml
# ❌ REMOVE these
- linuxkit/format
- linuxkit/mount
```

### Use `iso-efi` Output Format, Not `raw-efi`

`raw-efi` requires `linuxkit/mkimage-raw-efi` which depends on `linuxkit/format` — a problematic dependency chain.

**Fix:** Use `iso-efi` format instead — produces a bootable ISO that can be dd'd to USB or written with tools like `balenaEtcher`.

```bash
# Build
linuxkit build -format iso-efi project/linuxkit.yml

# Flash to USB (on Linux/macOS)
sudo dd if=output/hermes-usb-efi.iso of=/dev/sdX bs=4M status=progress
```

### Docker Hub Rate Limiting

Building requires pulling base images from Docker Hub. Unauthenticated pulls are rate-limited.

**Error:**
```
You have reached your unauthenticated pull rate limit. https://www.docker.com/increase-rate-limits
```

**Workarounds:**
1. Docker login before build (requires interactive TTY — may not work in automated environments)
2. Use a proxy/mirror (e.g., Mirror庄)
3. Wait for rate limit cooldown (unauthenticated: 100 pulls/6hrs)
4. Pre-pull images on a machine with auth, then save/load as tar

## Failure Modes

| Error | Cause | Fix |
|-------|-------|-----|
| `no such file or directory: output/build/hermes-usb-efi.img` | Missing output directory | Create `output/build/` before building |
| Docker Hub `TOOMANYREQUESTS` | Unauthenticated rate limit | Docker login or wait |
| `raw-efi` format fails | Missing `linuxkit/format` | Switch to `iso-efi` format |
| Hermes container won't start | Volume mount path wrong | Verify `-v /mnt/data:/opt/data` exists |

## Status

| Item | Status |
|------|--------|
| LinuxKit project structure | ✅ Done |
| iso-efi build | 🔄 In progress (Docker Hub rate limited) |
| USB flash script | ✅ Done |
| Actual hardware test | ❌ Pending |

## Related Concepts

- [Cron Job Architecture](/hermes-memos/concepts/cron-job-architecture/) — Cron system design and Hermes execution contexts
- [Agent Environments](/hermes-memos/concepts/agent-environments/) — Container vs bare-metal execution environments
- [Reflection Session 2026-04-16](/hermes-memos/concepts/reflection-session-2026-04-16/) — Open items: LinuxKit hardware test pending
