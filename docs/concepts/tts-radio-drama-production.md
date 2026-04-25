---
title: TTS 廣播劇製作工作流
created: 2026-04-13
updated: 2026-04-22
type: concept
tags: []
sources: []
---


## 概述

從網站小說文本 → 廣播劇腳本 → TTS 音頻 → 後製輸出 的完整流程。

**典型案例：** The Wandering Inn 正傳第一章（RW1-00）

---

## 核心流程

```
網站抓取小說
    ↓
腳本改寫（廣播劇格式）
    ↓
確認腳本格式相容 pipeline
    ↓
建立角色音色（ref_wav）
    ↓
批次 TTS 生成（background，避免 timeout）
    ↓
合併 + 雙-pass loudnorm + 壓縮 + MP3
```

---

## 腳本格式規範

### ✅ 正確格式（gradio-tts-script-pipeline 相容）

```
Narrator: 旁白文字內容
Erin: 角色台詞
```

### ❌ 不相容格式

```
(NARRATOR): ...     ← 圓括號被當作角色名的一部分
[SCENE: ...]        ← 方括號會被解析為角色名
(SOUND: ...)        ← 同上
旁白：...           ← 中文冒號不行，pipeline 只認 ASCII `:`
```

**規則：**
- 只能用 `角色: 台詞` 格式（ASCII 冒號）
- 圓括號 `()` → 會被當作角色名的一部分，導致解析失敗
- 方括號 `[]` → 會被當作角色名，旁白就變成 `[`
- 場景/音效 → 寫在 Narrator 行內，不要用標記

---

## 角色音色建立

每個角色需要一個**中性、平靜**的自我介紹 ref_wav，確保音色穩定。

```python
from gradio_client import Client, handle_file
import shutil, os, opencc

client = Client("http://localhost:8808")
converter = opencc.OpenCC('t2s')

characters = [
    ("narrator_ref.wav",
     "Female, radio drama narrator, warm and clear voice, steady pacing",
     "I am the narrator. I tell stories with a warm and steady voice."),
    ("erin_ref.wav",
     "Female, young woman, teenage to young adult, American accent, slightly tired but determined",
     "I'm Erin. I'm a teenage girl who ended up in a strange fantasy world."),
]

for filename, instruction, text in characters:
    result = client.predict(
        text=converter.convert(text),      # 簡體（中文內容時）
        control_instruction=converter.convert(instruction),
        ref_wav=None,
        use_prompt_text=False,
        prompt_text_value="",
        cfg_value=2,
        do_normalize=False,
        denoise=False,
        dit_steps=10,
        api_name="/generate",
    )
    shutil.copy(result, os.path.join(CHAR_DIR, filename))
```

---

## TTS 生成腳本

每批 25-30 句（避免 timeout），支援中斷後續傳：

```python
# 進度檔 progress.json
{"done": 0, "total": 156, "last_index": -1, "complete": False}

# 執行時自動檢查進度，跳過已生成的
for i in range(start_from, len(LINES)):
    if os.path.exists(f"c{i:03d}.wav"):
        continue  # 跳過已存在
    # ... 生成
    save_progress(done, EXPECTED, i)
```

---

## 後製管線（Podcast 標準）

### 完整流程

```
1. concat merge → merged_48k_stereo.wav
2. 雙-pass loudnorm → -16 LUFS
3. acompressor → 動態壓縮
4. libmp3lame → VBR 192k MP3
```

### 關鍵參數

| 參數 | 數值 | 備註 |
|------|------|------|
| Target Loudness | -16 LUFS | Podcast 標準 |
| Compressor Threshold | 0.1 (線性) | ⚠️ 不是 dB！0.1 = -20dB |
| Compressor Ratio | 3:1 | |
| Compressor Attack | 10 ms | |
| Compressor Release | 100 ms | |
| Compressor Knee | 6 dB | |
| Compressor Makeup | 1 | ⚠️ 最小值為 1，不能為 0 |

### 常見錯誤

| 錯誤訊息 | 原因 | 修正 |
|----------|------|------|
| `Option not found: 'output'` | `acompressor` 無 `output` 參數 | 改用 `makeup` |
| `Value -20.0 for parameter 'threshold' out of range` | threshold 需為線性值（0-1） | 設為 `0.1` |
| `Value 0.0 for parameter 'makeup' out of range` | makeup 最小值為 1 | 設為 `1` |

---

## 專案目錄結構

```
tts-project/
├── characters/              # 角色音色 ref_wav
│   ├── narrator_ref.wav
│   └── erin_ref.wav
├── The_Wandering_Inn/
│   └── Volume_1/
│       └── Chapter_1.00/
│           ├── 1.00_The_Wandering_Inn_raw.txt
│           ├── 1.00_The_Wandering_Inn_SCRIPT.txt
│           ├── audio_wavs/              # c000~c155.wav
│           ├── merged_48k_stereo.wav    # 合併後
│           └── WI9_EP01_The_Wandering_Inn_Ch1.mp3
└── Archive/                  # 歷史檔案
```

---

## 重要發現

### 英文內容不需要 OpenCC

英文 TTS 直接送英文文字即可，不需要 `opencc t2s` 轉換。`t2s` 轉換只用於中文內容。

### 圓括號格式不相容

`(NARRATOR):` 這類格式在 `gradio-tts-script-pipeline` 中無法解析。腳本必須使用 `Narrator:` 格式。

### "Dustrag" 段落是很好的廣播劇素材

Erin 反覆自言自語 "dustrag" 的段落，在廣播劇中形成獨特的節奏感和心理描寫效果，適合用較慢的語速和明顯的停頓來處理。

### loudnorm 需要雙-pass

單-pass loudnorm 無法準確正規化。必須先分析（Pass 1）取得 `input_i`, `input_tp`, `input_lra`，再正規化（Pass 2）。

---

## 相關資源

- Skill: `gradio-tts-podcast-production`
- Skill: `gradio-tts-script-pipeline`
- Skill: `gradio-tts-character-designer`
