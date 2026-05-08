---
title: BOYO 英文文法教學影片專案
created: 2026-04-29
updated: 2026-05-08
type: concept
tags: [media-production, english-teaching, curriculum-design, video, script]
sources:
  - sources/boyo-english-grammar-sessions-2026-04-28-29.md
  - sources/sessions-2026-05-02-to-2026-05-08.md
---

# BOYO 英文文法教學影片專案

## 概述

2026-04-28 啟動的新專案，將《Boyo English Grammar》三冊教材改編為系列教學影片。目標受眾為國中學生，強調趣味性與互動性。

## 專案資訊

| 項目 | 值 |
|------|-----|
| 專案路徑 | `~/.hermes/english-teaching-video/project/` |
| 教材來源 | `~/.hermes/cache/documents/Boyo_English_Grammar_I_Clean.md` |
| 教材冊數 | 三本（Boyo English Grammar I / II / III） |
| TTS 引擎 | OmniVoice（正式路線） |
| 總管線技能 | `educational-video-production-pipeline` |
| 腳本技能 | `teaching-script-writer` + `english-grammar-teaching-script-style` |
| YouTube Playlist | `PL0xKEhydN5PR5eu5VbaWU4e5X7lsRo7iV` |

## 教材拆分策略

### Boyo English Grammar I（第一冊）

Unit I-1（Be 動詞現在式）拆分為 4 堂課：

| 課次 | 主題 | 核心內容 |
|------|------|----------|
| Lesson 1 | 名詞與冠詞 | 可數/不可數、a/an/the |
| Lesson 2 | 人稱代名詞與所有格 | 主格/受格/所有格切換（I/me/my） |
| Lesson 3 | Be 動詞現在式 | am/are/is 身分卡配對、肯定/否定/疑問 |
| Lesson 4 | 指示詞 | this/that/these/those 遠近指法 + 總複習 |

每單元內容均拆分為可管理的短片（每課 5-8 分鐘），避免學習負荷過重。

## 教學設計原則

### 敘事框架
- **「校園故事 + 闖關遊戲」混合模式**
- 固定角色：Teacher（老師）、Lisa（學生 A）、Wendy（學生 B）
- 系列連貫的世界觀設定

### 腳本規範
- JSON 格式（含 `visual_style`、`scenes`、`narration_A`、`narration_B`）
- 每條旁白 ≤50 字元（OmniVoice TTS 穩定性）
- 禁用全形標點（？、！、——、……）→ 替換為句號或逗號
- `image_prompt` 必須繼承全域 `visual_style`

### 教學元素
- 開場問候制（greeting-first openings）
- 中英文例句平衡
- 自然課堂對話風格（非僵硬講義體）
- 練習環節：口頭快問快答、錯誤偵探

## 製作流程

```
教材分析 → 課程拆分 → 大綱生成（Generator+Evaluator）→ JSON 腳本 → 影像生成 → TTS → 影片組裝
```

### 技術棧
- **腳本**：`teaching-script-writer`（JSON 格式）
- **影像**：MiniMax 或系統原生 `image_generate`（待用戶決定）
- **TTS**：OmniVoice（teacher profile）
- **影片組裝**：ffmpeg（需注意 concat demuxer 音頻流 bug — 無聲clip需加 `anullsrc`）

## Lessons 1-14 完成紀錄（2026-05-08 更新）

> Lessons 1-14 全部完成並上傳 YouTube，Playlist: `PL0xKEhydN5PR5eu5VbaWU4e5X7lsRo7iV`

### 技術解決方案

| 問題 | 解法 |
|------|------|
| 4:3 → 16:9 比例不符 | PIL center-crop 裁切 |
| 音頻流丟失 | `anullsrc` 防止無聲 clip 丟失音軌 |
| 雙渲染模式 | MiniMax vs Native 兩種模式並存 |

### YouTube 上傳

- 批次上傳腳本自動化
- Title 格式標準化
- Playlist 孤兒清理（移除未歸屬影片）

## Series III 視覺重設計

> 2026-05-05 ~ 05-07 確立「Warm Academy / 暖白學院」風格方向。詳見 [BOYO Series III 視覺重設計](/hermes-memos/concepts/boyo-series-iii-visual-redesign/)。

- 暖米白底 + 主題藍 + 暖橙點綴
- 封面 Title-first 佈局
- 課堂場景結構化頁面（左規則/右對話/底字幕區）
- 開場圖卡 + 共用開場音樂（硬性規定）

Series III 進度：III-1 ✅、III-2 ✅、III-3 ✅、III-4 待做

## 製作狀態

| 階段 | 狀態 |
|------|------|
| 專案初始化 | ✅ 完成 |
| 教材複製 | ✅ 完成 |
| I-1 課程拆分 | ✅ 完成（4 課大綱） |
| Lesson 1-4 JSON 腳本 | ✅ 完成 |
| Lessons 1-14 上傳 YouTube | ✅ 完成 |
| Series III Warm Academy 設計 | ✅ 定版 |
| Series III-1 ~ III-3 | ✅ 完成 |
| Series III-4 | ⏳ 待做 |

## 與其他專案的關係

- 獨立於 NTU 南北朝歷史影片專案
- 與國中教育會考題庫專案互補（同一目標受眾：國中生）
- TTS 架構共用 OmniVoice 產線
- 影片生成使用 [AI 影片生成實戰 — Seedance 2.0](/hermes-memos/concepts/ai-video-generation-seedance/)

## 相關頁面

- [TTS 語音處理新規則](/hermes-memos/concepts/tts-voice-processing-rules/) — TTS 技術規範
- [OmniVoice TTS 本地遷移](/hermes-memos/concepts/omnivoice-tts-migration/) — OmniVoice 架構
- [NTU OmniVoice 自主產線 v4](/hermes-memos/concepts/ntu-omnivoice-full-production-run/) — NTU 產線（可參考）
- [Exam Question Bank QA System](/hermes-memos/concepts/exam-question-bank-qa-system/) — 國中會考題庫（同受眾）
- [BOYO Series III 視覺重設計](/hermes-memos/concepts/boyo-series-iii-visual-redesign/) — Series III 視覺設計系統
- [AI 影片生成實戰 — Seedance 2.0](/hermes-memos/concepts/ai-video-generation-seedance/) — Seedance 影片生成
