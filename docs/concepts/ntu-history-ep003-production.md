---
title: NTU 魏晉南北朝 Ep003 製作
created: 2026-04-29
updated: 2026-04-29
type: concept
tags: [media-production, ntu-history, video, script, omnivoice, style-bible]
sources:
  - sources/ntu-history-ep002-ep003-sessions-2026-04-27-28.md
---

# NTU 魏晉南北朝 Ep003 製作

## 概述

Ep003（「天下分裂與西晉統一」）於 2026-04-28 正式啟動製作，是第一個從頭到尾使用 OmniVoice 標準產線的集數。

## Ep003 核心問題（教材分析）

從教材文本中提取的五大歷史主題：

1. **統一與分裂** — 三國歸晉的歷史邏輯
2. **崩潰** — 西晉短期滅亡的結構性原因
3. **鄉村危機** — 基層社會的動盪
4. **地方豪族** — 門閥士族的興起
5. **族群融合** — 五胡與漢族的互動

## 腳本生成

- **格式**：5 章 JSON（CH01–CH05），每章含 `image_prompt` + `narration` 欄位
- **旁白限制**：每段 ≤30 字元，適配 TTS 與影片節奏
- **禁用標點**：全形「？」「！」「——」「……」一律替換為句號或逗號
- **腳本路徑**：`~/.hermes/ntu-wei-jin-history/textbook/output/Ep003/03_scripts/`

## Style Bible 整合

Ep003 正式導入「Chinese History Teaching Video Image Style Bible」：

- **全局風格提示詞**：每個 `image_prompt` 必須以 `global_style_prompt` 開頭
- **畫風定位**：「手繪動漫教育插畫」風格，2D 彩繪
- **文化錨定**：明確指定中國歷史場景，避免 AI 偏向「通用古文明」（如羅馬/埃及）

### Style Bible 核心規則

```
1. 所有 prompt 開頭加全域風格前綴
2. 場景描述必須包含具體中國歷史元素（服飾、建築、器物）
3. 禁止使用模糊的 "ancient civilization" 描述
4. 角色外觀必須繼承集數設定（教師/學生服裝）
```

## 製作規格

| 項目 | 規格 |
|------|------|
| TTS 引擎 | OmniVoice（正式標準） |
| 角色音色 | teacher_a_ref.wav（女）+ teacher_b_ref.wav（男） |
| 音頻採樣率 | 48kHz mono |
| 影片解析度 | 1920×1080 |
| 每集結構 | 5 CH × 5 scenes = 25 scenes × 2 chars = 50 軌 TTS |
| 批次 TTS | tmux 背景執行（run_omnivoice_tmux.sh） |
| 中文處理 | OpenCC t2s（繁→簡）for OmniVoice 穩定性 |

## 製作狀態

| 階段 | 狀態 |
|------|------|
| 教材分析 | ✅ 完成 |
| 核心問題 | ✅ 完成（核心問題.md） |
| 腳本生成 | ✅ 完成（CH01–CH05） |
| 影像生成 | ⏳ 待執行（需用戶選 MiniMax 或 image_generate） |
| TTS 生成 | ⏳ 待執行 |
| 影片組裝 | ⏳ 待執行 |

## NTU 專案架構重整（2026-04-27）

Ep003 製作前進行了重大架構調整：

### Gradio Server 統一
- **舊模式**：直接呼叫 `VoxCPMDemo` Python class（direct call）
- **新模式**：統一走 Gradio Server（localhost:8808）
- **原因**：避免環境依賴衝突、統一 API 介面
- **後續**：OmniVoice 階段改為直接 Python API，不再依賴 Gradio Server

### 角色音色生成
- `teacher_a_ref.wav`：女性溫柔教師，使用平穩旁白台詞生成
- `teacher_b_ref.wav`：男性穩重教師
- 後處理：ffmpeg 80Hz 高通 + 12kHz 低通 + 降噪 + loudnorm
- 格式：48kHz stereo PCM

### 目錄標準化
```
textbook/output/EpXXX/
├── 03_scripts/
├── 04_images/
├── 05_audio/
├── 06_final/
characters/            ← 共用角色（專案根目錄）
shared/characters/     ← teacher_a/b_ref.wav
```

## 相關頁面

- [NTU Ep002 製作與音頻修復](/hermes-memos/concepts/ntu-history-ep002-production/) — 前一集（音頻修復）
- [OmniVoice TTS 本地遷移](/hermes-memos/concepts/omnivoice-tts-migration/) — TTS 架構遷移
- [TTS 語音處理新規則](/hermes-memos/concepts/tts-voice-processing-rules/) — 音頻處理規則
- [NTU OmniVoice 自主產線 v4](/hermes-memos/concepts/ntu-omnivoice-full-production-run/) — 自主產線
- [[educational-video-production-pipeline]] — 教育影片總管線
