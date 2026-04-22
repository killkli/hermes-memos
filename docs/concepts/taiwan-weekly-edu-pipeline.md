# 台灣每週教育影片製作管線

**創建日期**：2026-04-22
**更新日期**：2026-04-22（整合完整知識）
**相關技能**：tw-news-collector, teachable-topic-picker, teaching-script-writer, minimax-image-generator, gradio-tts-dialogue-generator, video-compositor, youtube-upload

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
