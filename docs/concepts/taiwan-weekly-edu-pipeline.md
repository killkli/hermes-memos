---
title: 台灣每週教育影片製作管線
created: 2026-04-13
updated: 2026-04-22
type: concept
tags: []
sources: []
---


**創建日期**：2026-04-22
**更新日期**：2026-04-23（整合W16實戰經驗）
**相關技能**：tw-news-collector, teachable-topic-picker, teaching-script-writer, minimax-image-generator, gradio-tts-dialogue-generator, video-compositor, youtube-upload
**實戰教訓來源**：`[Taiwan Weekly News W16](/hermes-memos/sources/2026-04-23_taiwan-news-2026-W16-analysis/)`

---

## 完整工作流程

```
每週新聞蒐集（tw-news-collector）
    ↓
題材挑選（teachable-topic-picker）→ 每個領域選一個適合5-8分鐘教學的題材
    ↓
腳本撰寫（teaching-script-writer）→ 每幕含：英文 image_prompt + 雙人對談口說內容
    ↓
圖片生成（minimax-image-generator）→ 16:9 教學圖片，每場景一張
    ↓
語音生成（gradio-tts-dialogue-generator）→ A/B 兩人對話，生成 OGG 音頻
    ↓
影片組裝（video-compositor）→ 圖片 + 語音 → MP4
    ↓
字幕生成 → SRT 繁體字幕
    ↓
YouTube 上傳（youtube-upload）→ 字幕 + 縮圖上傳
```

---

## 每週執行規格（2026-04-W15 為例）

| 項目 | 內容 |
|------|------|
| **時間** | 每週一 07:00 cron 自動執行 |
| **領域數量** | 9個（國際、社會、政治、影視、音樂、文化、教育、財經、科技） |
| **每領域集數** | 每領域 1 集（每集 5-8 分鐘） |
| **每集場景數** | 約 8-12 個場景（取決於腳本長度） |
| **圖片規格** | 16:9, 英文 image_prompt |
| **音頻格式** | OGG（繁體中文 TTS） |
| **字幕格式** | SRT（sidecar 檔，不燒錄） |
| **輸出格式** | MP4 + SRT |

---

## 目錄結構

```
~/.hermes/taiwan-weekly-edu/{week}/          # 例如 2026-04-W15
├── 03_scripts/
│   ├── 社會.json      # dict 格式，含 "scenes" 陣列
│   ├── 國際.json
│   └── ...
├── 04_images/
│   ├── 社會/          # 圖片在 domain 子目錄下
│   │   └── 社會_議題名稱_01.png
│   │   └── 社會_議題名稱_02.png
│   ├── 國際/
│   │   └── 國際_美伊戰爭背後的大國博弈_中共角色與台灣戰_01.png
│   └── ...
├── 05_audio/          # ⚠️ 平面目錄，不按 domain 分子目錄
│   ├── 社會_1_A.ogg   # 格式：{domain}_{scene_id}_{spk}.ogg
│   ├── 社會_1_B.ogg
│   ├── 國際_1_A.ogg
│   └── ...
├── 06_final_videos/
│   ├── 社會_議題名稱.mp4
│   └── 社會_議題名稱.srt
└── 影像生成提示詞.md
```

**⚠️ 重要確認方法**：
```bash
ls ~/.hermes/taiwan-weekly-edu/{week}/05_audio/ | head -5   # 看實際檔名
ls ~/.hermes/taiwan-weekly-edu/{week}/04_images/            # 看 domain 子目錄
cat ~/.hermes/taiwan-weekly-edu/{week}/03_scripts/社會.json | python3 -c "import sys,json; d=json.load(sys.stdin); print(d['scenes'][0])"
```

---

## 圖片生成（MiniMax image-01）

### 每場景的 image_prompt 格式

腳本中的 image_prompt 為**英文**，用於 MiniMax 圖生成：

```
A professional educational infographic showing [specific concept], 
16:9 aspect ratio, clean design with Taiwan context, 
style: modern documentary
```

### 領域子目錄結構

圖片存放在 `{domain}/` 子目錄下，而非平面目錄：
```
04_images/社會/社會_議題名稱_01.png
04_images/國際/國際_美伊戰爭_01.png
```

---

## 語音生成（Gradio TTS Dialogue）

### 格式要求

```python
# gradio-tts-dialogue-generator 輸入格式
{
    "scenes": [
        {
            "scene_id": "01",
            "narration_A": "口說內容（中文）",  # 給 A 角色的台詞
            "narration_B": "口說內容（中文）",  # 給 B 角色的台詞
            "image_prompt": "英文 image prompt"
        },
        ...
    ]
}
```

### ⚠️ 圖片出現延遲原則

**圖片出現後 1 秒再開始語音**，避免語音與圖片同時出現造成觀看體驗不佳。此調整也改善了字幕與語音的同步狀況。

---

## 影片組裝（FFmpeg video-compositor）

### ⚠️ FFmpeg subtitles filter 限制（2026-04 實測）

| 嘗試方式 | 結果 | 解決方案 |
|----------|------|----------|
| `subtitles` filter | **不存在**（ffmpeg build 未含 libass） | 產出獨立 .srt 檔 |
| `drawtext` filter | **不存在** | 同上 |
| SRT mux into MP4 | **exit code 234**（MP4 容器不支援 SRT 字幕流） | 產出 .srt sidecar 檔 |
| MKV mux SRT | 可行 | 但多數播放器預設不解析內嵌字幕 |

**目前解決方案**：產出獨立的 `.srt` 字幕檔，播放時手動載入（VLC、MX Player）

### 場景命名與路徑注意

1. **圖片命名**：包含完整話題（如 `國際_美伊戰爭背後的大國博弈_中共角色與台灣戰_01.png`），pipeline 會自動使用 `os.path.basename()` 提取
2. **scene_id 補零**：使用 `01` 而非 `1`（三位數格式 `001`）
3. **音頻中間檔命名**：WAV/MP4 中間檔需加前綴底線區分 temp 檔（如 `_merged.wav`）

---

## YouTube 上傳

### 頻道資訊

- **TaiwanTeller**：`@UCnm5F5bej83I7l3yDL6y_-A`

### ⚠️ 字幕上傳 scope 問題

```bash
# 錯誤的 scope（無效）
youtube.force-ssl-auth

# 正確的 scope（caption 上傳必備）
youtube.force-ssl
```

**解決方法**：更新 `~/.hermes/youtube/authorize.py` 和 `~/.hermes/skills/media/youtube-upload/scripts/youtube_upload.py` 的 SCOPES 之後，需重新執行授權：

```bash
python3 ~/.hermes/youtube/authorize.py --channel-id @UCnm5F5bej83I7l3yDL6y_-A --device-flow
```

### 上傳後字幕處理

字幕目前只能以 **sidecar SRT** 方式交付，燒錄功能待日後研究。

---

## SRT 字幕生成

每個 domain 依實際音頻時長，按語速斷句生成對應時間軸的繁體中文 SRT。可用 MKV mux 方式驗證 SRT 可正常封裝（MKV + SRT 流可成功 mux）。

---

## Cron 自動化

每週一 07:00（台北時間）自動執行，流程：
1. 確認日期與市場狀態
2. 判斷是否交易日（週末發週末特刊）
3. 並行蒐集 7 大產業新聞（分 3 批）
4. 整合報告
5. 嘗試存檔並發送至用戶

---

## 常見錯誤與解決方案

| 錯誤 | 原因 | 解決方案 |
|------|------|----------|
| `HTTP 432` | Web search/excerpt 失敗 | 資料有限，標注 ⚠️ |
| 路徑 `Permission denied` | `/root/` vs `/Users/johnchen/` 路徑混淆 | 使用 `~/.hermes/taiwan-weekly-edu/` |
| FFmpeg subtitles filter 不存在 | ffmpeg build 未含 libass | 產出 .srt sidecar 檔 |
| MP4 mux SRT 失敗 | MP4 容器不支援 SRT 字幕流 | 改用 MKV 或獨立 SRT 檔 |
| YouTube 字幕上傳 403 | scope 設為 `youtube.force-ssl-auth` | 改用 `youtube.force-ssl`，重新授權 |

---

## W16 實戰教訓（2026-04-23）

### 1. SRT 字幕 bug：參數順序錯誤

**問題**：`make_srt(narration_A, speaker, duration, srt_path)` 第二參數傳入的是 `speaker`（標籤文字）而非 `narration_B`（實際台詞），導致 B 角色字幕只有標籤沒有內容。

**修復**：正確順序 `make_srt(narration_A, narration_B, duration, srt_path)`，並用 ffprobe 取真實 OGG 音訊 duration，而非自己計算。

### 2. YouTube API quota 耗盡時的處理原則

> **字幕永遠不燒進 MP4**。保持現狀，等待 API quota 重置或手動上傳。

| 錯誤 | 原因 | 解法 |
|------|------|------|
| 403 `quotaExceeded` | YouTube 每日配額用盡 | 等午夜 PST 重置；或 YouTube Studio 手動上傳 SRT |
| 409 `conflict` | 已有同語言字幕 | 先刪除舊字幕再上傳 |
| 403 其他 | OAuth token 過期 | 刷新 token |

**FFmpeg mov_text 嵌入式字幕（不改編碼）**：`ffmpeg -i input.mp4 -f srt -i input.srt -c:v copy -c:a copy -c:s mov_text output.mp4` — 這是備援手段，不是標準流程。

### 3. 三個國中生代理人投票的設計

使用 delegate_task 並行產生三位虛構國中生的新聞投票：
- 阿偉（活潑型）：喜歡有趣、有爆點的新聞
- 小美（關心社會型）：關注公平、正義相關新聞
- 小杰（理性型）：喜歡有數據、有邏輯的新聞

**投票邏輯**：每領域由得票最多的新聞當選；平票時由阿偉當選（活潑型代理人的偏好優先）。

### 4. TTS 音色：新聞廣播 vs 一般角色的區分

| 用途 | 音色檔案 |
|------|----------|
| 新聞廣播（老師A，男聲） | `~/.hermes/taiwan-weekly-edu/characters/teacher_a_ref.wav` |
| 新聞廣播（助理B，女聲） | `~/.hermes/taiwan-weekly-edu/characters/teacher_b_ref.wav` |
| 一般廣播劇/有聲書 | `~/.hermes/tts-project/characters/`（不同目錄） |

**注意**：兩組音色用途不同，給錯音色會導致聲音年齡/風格不符。

### 5. 新聞來源品質差異

| 來源類型 | 問題 | 實例 |
|----------|------|------|
| 中央社（CNA） | ✅ 時間乾淨無舊聞 | W16 全部 04/19–04/23 |
| Aggregator 網站 | ❌ 舊聞重複推送、時間戳混亂 | W17 社會/政治有大量1-3個月前舊聞 |

**實務建議**：新聞蒐集以中央社為主，Aggregator 僅作為補充參考。

### 6. 影視/音樂/教育三領域的空洞化問題

- **影視**：本週全是娛樂緋聞，無電影產業政策或創作議題
- **音樂**：本週全是演唱會消息，無產業趨勢或創作深度報導
- **教育**：所有項目均與科技/文化重疊，無獨立教育政策

**影響**：這三個領域的新聞教學潛力較低，需要更主動地從其他領域的新聞中挖掘教材。
