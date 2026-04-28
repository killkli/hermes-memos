---
title: NTU 魏晉南北朝影片 OmniVoice 自主產線 v4
created: 2026-04-28
updated: 2026-04-28
type: concept
tags: [ntu, omnivoice, video-production, tts, ep001, ep002]
sources: [2026-04-28 OmniVoice full autonomous production session]
---

## 概述

本頁記錄 2026-04-28 的 **OmniVoice 自主產線完整運行**，以及後續發現的**規格缺口與修正**。

**目標 Repo**: `~/.hermes/ntu-wei-jin-history/`
**OmniVoice workspace**: `~/.hermes/omnivoice-migration/`

---

## 本輪完成的事情

### 1. OmniVoice 環境驗證
- OmniVoice repo/venv 已確認可用（torch + torchaudio 正常）
- `teacher_A` / `teacher_B` profiles 通過驗證
- Ep001 / Ep002 各自建立 `tts_manifest.csv` 與標準目錄骨架

### 2. 圖片生成（系統原生 `image_generate`）
- 每 EP 每 CH 生成場景圖，統一裁成 **16:9 1920×1080**
- 使用系統原生工具（非 MiniMax）

### 3. OmniVoice TTS 批次生成
- **Ep001 CH01** 已完成全部音頻
- **Ep002 CH01** 已完成全部音頻
- 每 EP 每 CH 的音頻寫入正式目錄

### 4. 組裝與字幕
- MP4 + SRT 已組裝產出至 `06_final/`

---

## 發現的規格缺口

### ⚠️ 問題：每 EP 只產出 1 個 CH，而非 5 個 CH

本輪跑的藍圖 `BLUEPRINTS_Ep001_Ep002.md` 把每個 EP 規劃為：
- **每 EP = 1 個 CH**（每 CH 5 scenes）

但專案主流程 `PIPELINE.md` 定義的是：
- **每 EP = 5 個 CH**
- 每 CH = 5 scenes
- **每 EP = 25 scenes × 2 characters = 50 軌 TTS**

### 本輪產出 = CH01 試做版（命名隔離）
- 本輪成品命名為 `EpXXX_CH01_final/` 而非污染正式 `06_final/` 結構
- 差異：Ep001 和 Ep002 各只有 CH01，不是完整 5 CH

### 已識別缺口
| 項目 | Ep001 | Ep002 |
|------|-------|-------|
| CH01 | ✅ 已有（試做版）| ✅ 已有（試做版）|
| CH02 | ❌ 缺腳本/圖/音頻 | ❌ 缺腳本/圖/音頻 |
| CH03 | ❌ 缺腳本/圖/音頻 | ❌ 缺腳本/圖/音頻 |
| CH04 | ❌ 缺腳本/圖/音頻 | ❌ 缺腳本/圖/音頻 |
| CH05 | ❌ 缺腳本/圖/音頻 | ❌ 缺腳本/圖/音頻 |

---

## CH02-CH05 補完計畫（已建立）

**Ep001 的 5-CH 結構**（來源：教材）：
1. CH01 這四百年重新定義了中國（魏晉南北朝的時空座標）
2. CH02 從三國到五胡：非典型的亂世敘事
3. CH03 內亞視角：什麼是「胡」？
4. CH04 孝文帝改革：被低估的東亞再整合
5. CH05 結論：多元視角的必要

**Ep002 的 5-CH 結構**（黃巾之亂）：
1. CH01 黃巾之亂：定義一個時代
2. CH02 歷史解釋與群眾動員
3. CH03 宗教符號與五德終始說
4. CH04 打破「鋤與犁」的神話
5. CH05 農業國家的發明：批判耕戰二元框架

**腳本產生方式**：子代理批次生成 → 獨立 evaluator 審查

---

## Evaluator 審查發現的問題

### 問題：Ep001/03 與 Ep002/04 太方法論、重複

| 問題章節 | 問題描述 |
|---------|---------|
| Ep001/03 | 歷史學方法論章節過重，與 Ep002/04 內容重疊 |
| Ep002/04 | 農民起義敘事框架重複出現 |

**處理方式**：抽讀實際 JSON 內容，修掉 FAIL 章節，重新驗證。

---

## 技術細節

- **圖片生成工具**：系統原生 `image_generate`（非 MiniMax）
- **圖片比例**：統一裁成 16:9 1920×1080
- **TTS 引擎**：OmniVoice（teacher_A + teacher_B 雙角色）
- **組裝**：批量 MP4 + SRT，寫入 `06_final/`
- **圖片 4:3 → 16:9 裁切**：pillow (`Image.open().crop((0, 60, 1440, 780))`)

---

## 關聯頁面

- [OmniVoice TTS 本地遷移](/hermes-memos/concepts/omnivoice-tts-migration/) — OmniVoice 兩階段架構
- [TTS 語音處理新規則](/hermes-memos/concepts/tts-voice-processing-rules/) — TTS 語音處理新規則
- [[educational-video-production-pipeline]] — 教育影片總管線
- [[ntu-history-episode-video-pipeline]] — NTU 單集影片製作技能

