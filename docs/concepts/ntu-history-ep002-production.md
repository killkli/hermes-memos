---
title: NTU 魏晉南北朝 Ep002 製作與音頻修復
created: 2026-04-29
updated: 2026-04-29
type: concept
tags: [media-production, ntu-history, video, audio-fix, youtube]
sources:
  - sources/ntu-history-ep002-ep003-sessions-2026-04-27-28.md
---

# NTU 魏晉南北朝 Ep002 製作與音頻修復

## 概述

Ep002（「天下分裂與西晉統一」）經歷了兩次上傳：初版因音頻降頻問題導致聲音低沉難辨，完全重建後修復。

## 關鍵事件時序

| 日期 | 事件 |
|------|------|
| 2026-04-25 | 初版 Ep002 製作完成（5 章 → 合併 → 上傳） |
| 2026-04-27 | 發現音頻問題：24kHz 降頻導致聲音「低沉含糊」 |
| 2026-04-27 | 根因分析：中間 MP4 組裝時未指定 `-ar 48000`，ffmpeg 預設降頻 |
| 2026-04-27 | 重建：從原始 WAV 重新提取 48kHz 音頻，重組 5 章 |
| 2026-04-27 | 舊影片刪除，新版上傳（ID: `A02MHEO5qg4`） |
| 2026-04-27 | 清理 ~330MB 中間檔案（PNG/WAV/舊 MP4） |

## 根因分析：24kHz 降頻 Bug

```
Source WAV → [ffmpeg image+audio → MP4] → concat → final.mp4
                    ↑
              未指定 -ar 48000
              ffmpeg 預設 24kHz（或更低）
```

- **症狀**：人聲極低沉、像變聲器效果、幾乎無法辨識
- **根因**：`ffmpeg -loop 1 -i image.png -i audio.wav -c:v libx264 ...` 組裝時未指定採樣率
- **修復**：所有組裝指令強制加 `-ar 48000 -ac 1`

## 修復方案

```bash
# 修復後的組裝指令模板
ffmpeg -loop 1 -i "${img}" -i "${audio}" \
  -c:v libx264 -tune stillimage -pix_fmt yuv420p \
  -c:a aac -ar 48000 -ac 1 -b:a 128k \
  -t "${duration}" -shortest "${out}"
```

## YouTube 資訊

| 版本 | Video ID | 狀態 |
|------|----------|------|
| 初版（24kHz bug） | `5PtSgvAupu0` | 已刪除 |
| 重建版（48kHz 修復） | `A02MHEO5qg4` | 線上 |

## 教訓與規則

1. **48kHz 硬性要求**：所有 ffmpeg 組裝必須加 `-ar 48000`，不可依賴預設值
2. **雙層字幕策略**：`mov_text` 封裝進 MP4（備用） + YouTube Caption API（主要）
3. **場景時長由音頻決定**：duration = audio_length + 1s lead，防止靜態沉默
4. **標題卡恢復**：Ep002 意外省略了 Pillow 生成的金色標題卡，Ep003 起恢復
5. **產後清理**：最終 MP4 驗證後刪除中間 PNG/WAV，每 EP 從 ~180MB 降至 ~20MB

## 相關頁面

- [TTS 語音處理新規則](/hermes-memos/concepts/tts-voice-processing-rules/) — 48kHz 硬性要求規則
- [OmniVoice TTS 本地遷移](/hermes-memos/concepts/omnivoice-tts-migration/) — TTS 架構演進
- [NTU OmniVoice 自主產線 v4](/hermes-memos/concepts/ntu-omnivoice-full-production-run/) — NTU 自主產線
- [NTU Ep003 製作啟動](/hermes-memos/concepts/ntu-history-ep003-production/) — 下一集製作
