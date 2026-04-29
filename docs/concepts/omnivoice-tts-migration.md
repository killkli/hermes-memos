---
title: OmniVoice TTS 本地遷移
created: 2026-04-28
updated: 2026-04-29
type: concept
tags: [tts, omnivoice, voxcpm, migration]
sources:
  - k2-fsa/OmniVoice GitHub
  - sources/ntu-history-ep002-ep003-sessions-2026-04-27-28.md
---

## 概述

用戶決定以 [k2-fsa/OmniVoice](https://github.com/k2-fsa/OmniVoice) 完全取代現有的 VoxCPM/Gradio TTS 方案。目標：徹底重建本地 TTS 生產管線，不再保留 Vox 作為備用。

**Repo**: `https://github.com/k2-fsa/OmniVoice`

---

## 兩階段語音架構

OmniVoice 生產流程分兩個角色分工：

| 階段 | 工具 | 功能 | 產出 |
|------|------|------|------|
| Voice Design | VoxCPM Voice Design | 設計並生成角色音色候選（豐富語調變化） | 候選 ref_audio |
| Voice Clone | OmniVoice Voice Clone | 穩定量產 TTS 音頻 | 正式 production audio |

**核心原理**：
- Voice Design 階段產生的音色多樣性高，選擇最佳 candidate 作為固定 ref_audio
- Voice Clone 階段以固定 ref_audio 為基礎，穩定生成大量語音

---

## 角色 Profiles 系統

角色音色設定儲存於：
```
~/.hermes/omnivoice-migration/profiles/teacher_profiles.json
```

Format:
```json
{
  "teacher_a": {
    "name": "老師A",
    "voice_description": "女性，溫柔的中學老師，聲音溫暖穩定，語速中等偏慢",
    "ref_audio": "/path/to/teacher_a_ref.wav",
    "speed": 0.8
  }
}
```

---

## text_preprocess 五階段管線

腳本文字在送入 TTS 前須經過 5 階段處理：

1. **Stage Direction 移除** — 刪除圓括號內的語氣描述（`(平靜地)`、`(微笑)` 等）
2. **正規化** — 數字、標點、特殊符號標準化
3. **NV 標記 OFF** — 停用 Nova 標記（`[2phonemes]` 等）
4. **Phoneme Fix** — 修正發音問題
5. **OpenCC t2s** — 繁體 → 簡體轉換（OmniVoice 模型輸入偏好簡體）

**注意**：腳本文字本身不應包含括號語氣描述——這是 production 標準，不是 workaround。

---

## 失敗音色處理原則

用戶對音色方向的把關非常嚴格：

- **當音色方向錯誤時，立即刪除候選 ref_audio 和對應生成的音頻檔**
- **不要保留失敗的音色檔案** — 會造成後期決策混亂
- Regeneration 需要重新從 Voice Design 階段開始

---

## 與 VoxCPM 的比較

| 面向 | VoxCPM (Gradio) | OmniVoice |
|------|-----------------|-----------|
| 音色穩定性 | 不穩定，跨批次音色飄移 | 穩定（固定 ref_audio + Clone） |
| 生產速度 | 較快（direct call） | 較慢但可控（兩階段） |
| ref_wav 處理 | Gradio ref_denoise=True（效果差） | 外部 ffmpeg 後處理降噪 |
| 部署方式 | Gradio Server (localhost:8808) | OmniVoice repo 直接部署 |
| 適合場景 | 測試、快速原型 | 正式生產 |

---

## 關鍵 Memory 更新（2026-04-28）

用戶偏好（已寫入 memory）：
- 固定 ref_wav 音色系統為正式影片唯一標準
- 無 ref_wav 的 direct TTS 只能作測試音，不能進 final video 組裝
- 失敗音色立即刪除，不保留

---

## 遷移執行進展（2026-04-27 ~ 04-29）

### 規劃階段（4/27 晚上）
- 完整研究 OmniVoice GitHub repo
- 確認 API：`model.generate(text=..., ref_audio=..., ref_text=...)` + `instruct` 參數
- 環境驗證：macOS 15.3, Apple M4, Python 3.9.6, `uv` package manager
- 決定保留手動分句邏輯（防止 speed-up/buzzing）

### 架構重整（4/27 下午）
- 統一所有 TTS 走 Gradio Server (localhost:8808)
- 禁止 direct call VoxCPMDemo class
- 生成 teacher_a/b_ref.wav（平穩旁白台詞 + ffmpeg 後處理）

### 生產標準化（4/28）
- Ep003 成為第一個從頭使用 OmniVoice 標準產線的集數
- 批次 TTS 走 tmux 背景（`run_omnivoice_tmux.sh`）
- 教學語速預設 0.75
- text_preprocess 加入 OpenCC t2s 強制轉換

### 尚未完成
- CH02-CH05 補完（NTU Ep001/002 目前只有 CH01）
- BOYO 英文文法專案正式 TTS 批次

---

## 關聯頁面

- [TTS 語音處理新規則](/hermes-memos/concepts/tts-voice-processing-rules/) — 語音處理新規則
- [NTU Ep002 製作與音頻修復](/hermes-memos/concepts/ntu-history-ep002-production/) — Ep002 音頻修復
- [NTU Ep003 製作啟動](/hermes-memos/concepts/ntu-history-ep003-production/) — Ep003 首個 OmniVoice 全流程集數
- [NTU OmniVoice 自主產線 v4](/hermes-memos/concepts/ntu-omnivoice-full-production-run/) — NTU 自主產線
- [BOYO 英文文法教學影片專案](/hermes-memos/concepts/boyo-english-grammar-video-project/) — BOYO 專案（共用 TTS 架構）

