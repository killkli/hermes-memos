---
title: NTU 魏晉南北朝 Ep002/Ep003 + BOYO 專案 Sessions（2026-04-27 ~ 04-29）
created: 2026-04-29
updated: 2026-04-29
type: source
tags: [session-log, ntu-history, omnivoice, english-teaching]
---

# Sessions 原始摘要（2026-04-27 ~ 04-29）

## Session 1: Ep002 音頻修復（2026-04-27 上午）

- **Session ID**: `20260427_053216_6129e265`
- **主題**: NTU 魏晉南北朝 Ep002 最終組裝、音頻修復、清理
- **關鍵事件**:
  - Ep002 初版上傳（5 章合併 7:27 影片）
  - 發現音頻低沉問題：中間 MP4 被降頻至 24kHz
  - 根因：ffmpeg 組裝未指定 `-ar 48000`
  - 重建：從原始 WAV 重新提取 48kHz → 重組 5 章
  - 舊影片刪除，新版上傳（ID: `A02MHEO5qg4`）
  - 清理 ~330MB 中間檔案
- **教訓**: 48kHz 是硬性要求，不可依賴 ffmpeg 預設

## Session 2: NTU 專案架構重整（2026-04-27 下午）

- **Session ID**: `20260427_121224_dc0a98bf`
- **主題**: 架構統一、Gradio Server 強制、角色音色生成
- **關鍵事件**:
  - 修正 4 個核心技能（gradio-tts 系列全部統一）
  - 統一 TTS 走 Gradio Server (localhost:8808)，禁止 direct call
  - 建立 Ep001 專用目錄結構
  - 生成 teacher_a_ref.wav / teacher_b_ref.wav
  - 音色後處理：ffmpeg 80Hz 高通 + 12kHz 低通 + 降噪 + loudnorm
- **決策**: TTS 禁用 direct call，統一 Server 模式

## Session 3: OmniVoice 遷移規劃（2026-04-27 晚上）

- **Session ID**: `20260427_175003_264eed7b`
- **主題**: VOX → OmniVoice 完整遷移規劃
- **關鍵事件**:
  - 完整研究 OmniVoice repo
  - 確認 API：`model.generate()` + `ref_audio` / `instruct` 參數
  - 環境驗證：macOS 15.3, M4, Python 3.9.6
  - 分析現有腳本（ch01_v4_tts.py）
  - 決定保留手動分句邏輯
- **產出**: 遷移計畫草案

## Session 4: Ep003 製作啟動（2026-04-28 晚上）

- **Session ID**: `20260428_204649_ef686529`
- **主題**: Ep003 全流程啟動、Style Bible 導入
- **關鍵事件**:
  - 分析 Ep003 教材，生成核心問題文件
  - 5 章 JSON 腳本完成
  - 導入 Style Bible（手繪動漫教育插畫、中國文化錨定）
  - OmniVoice 確認為正式標準
  - 48kHz 強制、教學語速 0.75
- **待辦**: 影像生成、TTS 批次、影片組裝

## Session 5: BOYO 英文文法影片專案（2026-04-28 ~ 04-29）

- **Session ID**: `20260428_222629_b7b82690`
- **主題**: BOYO English Grammar I 教材改編為教學影片
- **關鍵事件**:
  - 專案初始化（`~/.hermes/english-teaching-video/`）
  - I-1 單元拆分為 4 堂課
  - Lesson 1/2 大綱完成（Generator+Evaluator 審查）
  - 採用「校園故事 + 闖關遊戲」敘事框架
  - 確認 OmniVoice 為 TTS 引擎
  - 腳本規範：每行 ≤50 字元、禁用全形標點
- **待辦**: JSON 腳本生成、影像工具選擇

## Session 6: BOYO 教材拆分（2026-04-29）

- **Session ID**: `20260429_144906_1808923e`
- **主題**: 三本 BOYO 教材按章節拆分
- **關鍵事件**:
  - 將三本教材按內部章節結構拆分為獨立檔案
  - 便於每章節對應獨立影片腳本
