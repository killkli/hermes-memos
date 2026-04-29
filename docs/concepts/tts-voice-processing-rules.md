---
title: TTS 語音處理新規則（2026-04-28 更新）
created: 2026-04-28
updated: 2026-04-29
type: concept
tags: [tts, voxcpm, omnivoice, ffmpeg, audio-processing]
sources:
  - sources/ntu-history-ep002-ep003-sessions-2026-04-27-28.md
---

## 概述

本頁彙整 2026-04-28 從 VoxCPM → OmniVoice 遷移過程中發現的 TTS 語音處理新規則。這些是實戰中確認的陷阱與最佳實踐。

---

## Gradio ref_denoise = True 效果差（禁用）

### 問題
用戶發現 Gradio TTS 服務中 `ref_denoise=True` 的降噪效果非常差，不應作為預設。

### 正確做法
**角色參考聲音的降噪應使用 ffmpeg 後處理，而非依賴 TTS 模型內的 ref_denoise 參數。**

標準降噪 pipeline（ffmpeg）：
```bash
ffmpeg -i ref_wav_raw.wav -af "highpass=f=80,lowpass=f=12000,afftdn=nf=-25" -ar 48000 -ac 2 ref_wav_clean.wav
```

後續還要套用 loudnorm 標準化音量。

---

## 48kHz 采樣率硬性要求

### 為什麼重要
在影片組裝時，如果 clip 音頻不是 48kHz，ffmpeg concat demuxer 會自動 downsampling，導致：
- 音色變得低沉
- 語速聽起來變慢
- 嘴唇同步失調

### 硬性規則
**所有用於影片組裝的音頻 clip，必須是 48kHz mono/stereo PCM WAV。**

在組裝前加入檢查：
```bash
ffprobe -v error -show_entries stream=sample_rate -of default=noprint_wrappers=1:nokey=1 audio.wav
# 必須返回 48000
```

---

## 圓括號語氣描述的正確處理

### 問題
腳本文字中包含圓括號語氣描述（如「`(平靜地)`」）會干擾 TTS 模型的發音。

### 正確做法
圓括號語氣描述**不應出現在 production 腳本中**。這不是 work-around，是 production 標準。

**text_preprocess 五階段管線中的第一步**：Stage Direction 移除。

---

## ffmpeg concat 音頻流丟失問題

### 問題描述
當使用 ffmpeg concat demuxer 拼接 MP4 時，如果任何一個 clip 不帶音頻流，concat 可能會丟失音頻。

### 根因
concat demuxer 要求所有 clip 有一緋的音頻流配置。如果某個 clip 沒有 audio stream，會導致其他 clip 的音頻也丟失。

### 解決方案
- 在拼接前，確保所有 clip 都有一緋的音頻流
- 若某 clip 缺少音頻，用 `anullsrc` 注入一個空的 audio stream：
  ```bash
  ffmpeg -f lavfi -i anullsrc=channel_layout=stereo:sample_rate=48000 -i video.mp4 -c:v copy -c:a aac -shortest output_with_audio.mp4
  ```
- 或者在 concat 前統一音頻：所有 clip 都先抽出音頻再合併

---

## OmniVoice 兩階段架構的 text_preprocess

詳見 [OmniVoice TTS 本地遷移](/hermes-memos/concepts/omnivoice-tts-migration/)，核心五階段：
1. Stage Direction 移除
2. 正規化
3. NV 標記 OFF
4. Phoneme Fix
5. OpenCC t2s

---

## 關聯頁面

- [OmniVoice TTS 本地遷移](/hermes-memos/concepts/omnivoice-tts-migration/) — OmniVoice 兩階段架構
- [NTU OmniVoice 自主產線 v4](/hermes-memos/concepts/ntu-omnivoice-full-production-run/) — OmniVoice 自主產線 v4 經驗
- [NTU Ep002 製作與音頻修復](/hermes-memos/concepts/ntu-history-ep002-production/) — Ep002 音頻修復（48kHz 降頻 bug）
- [NTU Ep003 製作啟動](/hermes-memos/concepts/ntu-history-ep003-production/) — Ep003 首個 OmniVoice 全流程
- [BOYO 英文文法教學影片專案](/hermes-memos/concepts/boyo-english-grammar-video-project/) — BOYO 教學影片（共用 TTS 規範）
- [[ffmpeg-concat-audio-stream-bug]] — FFmpeg concat demuxer 音頻流丟失根因分析

