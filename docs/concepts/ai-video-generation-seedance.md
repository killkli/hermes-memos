---
title: Seedance 2.0 Fast 影片生成實戰知識
created: 2026-05-08
updated: 2026-05-08
type: concept
tags: [video, ai-generation, seedance, openrouter]
sources:
  - sources/sessions-2026-05-02-to-2026-05-08.md
---

# Seedance 2.0 Fast 影片生成實戰知識

## 概述

2026-05-04 ~ 05-08 期間，透過多個 sessions 建立了完整的 OpenRouter 影片 API + Seedance 2.0 Fast 模型實戰技能。涵蓋 Generator+Evaluator 審查流程、Token 計算、錨點機制、Prompt 技巧與成本紀律。

## OpenRouter 影片 API 技能架構

### Generator + Evaluator 審查模式

- **Generator**：生成影片 prompt、設定參數
- **Evaluator**：審查 prompt 品質、檢查參數合規性
- 採用 [Harness Pattern](/hermes-memos/concepts/harness-pattern/) 的雙代理人架構

### Seedance 2.0 Fast 模型特性

| 功能 | 說明 |
|------|------|
| text-to-video | 純文字描述生成影片 |
| image-to-video | 靜態圖片 → 動態影片 |
| first+last frame control | 首幀+末幀控制，精確掌控起止畫面 |
| 音訊生成 | `generate_audio: true` 強制開啟 |

## Token 計算公式

```
tokens = (height × width × duration × 24) / 1024
```

- 24 代表影格率 (fps)
- 以 1024 為除數得出 Token 數
- 此公式直接影響每次生成的成本

## 參數邏輯

> **關鍵規則**：`size` 與 `aspect_ratio` 二選一，**必須用 `size`**。

- `size` 格式：`"{width}x{height}"`，例如 `"480x854"`
- `aspect_ratio` 不可與 `size` 同時使用
- 使用 `size` 可精確控制解析度，有利於成本管理

## 錨點機制（Anchor Blocks）

錨點是跨段 prompt 一致性的核心技術。每段 prompt 必須逐字複製相同的錨點區塊。

### 三類錨點

1. **角色錨點**：固定角色外觀描述（服裝、髮型、體型、膚色等）
2. **風格錨點**：統一視覺風格（色調、光影、構圖基調）
3. **時間/光線錨點**：場景時間段、光源方向、天氣狀態

> 錨點必須在每段 prompt 開頭逐字重複，不可省略或改寫。任何偏差都會導致角色/風格跳變。

## 成本紀律

| 策略 | 說明 |
|------|------|
| 480×854 代替 720p | 約省 70% Token 消耗 |
| 低解析度草稿驗證 | 先用低解析度生成確認構圖與動態，再升級 |
| duration 控制 | 儘量用短片段拼接而非一次長片段 |

## Prompt 技巧

1. **繁中 → OpenCC 簡中**：Seedance 對簡中理解較佳，用 OpenCC `t2s` 轉換
2. **`generate_audio: true`**：強制開啟音訊生成（預設可能關閉）
3. **對白取代字幕描述**：直接寫對白文字，不要描述「畫面上出現字幕」
4. **角色數量壓到 2-4 人**：過多角色會導致畫面混亂、動作不穩定

## 實戰案例

### 鹽酥雞 RAP（V1-V4 四輪迭代）
- 2026-05-04 session
- 透過 Generator+Evaluator 反覆優化 prompt
- 四輪迭代逐步提升畫面品質與動態流暢度

### 大嘴鳥動物園（V1-V2）
- 2026-05-07 session
- 測試動物角色的動態表現
- 第一版與第二版的對比改進

### 柯基冷眼（4段15秒）
- 2026-05-08 session
- 4 段 15 秒短片段拼接
- 測試角色一致性在多段場景中的維持

### 幾何摺紙教學
- 2026-05-05 sessions
- 結合 [BOYO Series III 視覺重設計](/hermes-memos/concepts/boyo-series-iii-visual-redesign/) 的 Warm Academy 風格
- 教學型影片的 Seedance 實作

## 疑難排解

| Error | Cause | Fix |
|-------|-------|-----|
| 畫面角色跳變 | 錨點未逐字複製到每段 prompt | 確保 Anchor Block 完整複製 |
| Token 超額 | 使用 720p 等高解析度 | 改用 480×854，省 70% Token |
| 真人過濾觸發 | 生成的 first frame 當 reference image | 避免使用生成圖作為 reference；改用純 text-to-video |
| 音訊缺失 | 未設定 generate_audio | 強制 `generate_audio: true` |
| 簡中 Prompt 品質差 | 直接用繁中 | 用 OpenCC t2s 轉換 |

## 相關頁面

- [[seedance]] (planned) — Seedance AI video generation overview
- [BOYO 英文文法教學影片專案](/hermes-memos/concepts/boyo-english-grammar-video-project/) — BOYO 專案影片產線
- [搞笑短片創作技能](/hermes-memos/concepts/comedy-short-video-production/) — 搞笑短片技能
- [Harness Pattern](/hermes-memos/concepts/harness-pattern/) — Generator + Evaluator 審查架構
