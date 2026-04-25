# Wiki Log

> Chronological record of all wiki actions. Append-only.
> Format: `## [YYYY-MM-DD] action | subject`
> Actions: ingest, update, query, lint, create, archive, delete
> When this file exceeds 500 entries, rotate: rename to log-YYYY.md, start fresh.

## [2026-04-25] ingest | 臺大開放式課程 魏晉南北朝史（甘懷真）15集逐字稿

- Source: ~/.hermes/ntu-wei-jin-history/transcripts/ (Ep001~Ep015 raw txt)
- Created: sources/ntu-wei-jin-history-kan-huaijen-2026-04-25.md (source page)
- Created: concepts/wei-jin-historical-methodology.md (歷史學方法論，Ep01)
- Created: concepts/no-single-han-identity.md (沒有單一漢族，Ep01/09/10)
- Created: concepts/invention-of-professional-farmer.md (專業農民發明，Ep01/05/12)
- Created: concepts/east-asian-great-migration.md (東亞大移民，Ep03/04/06)
- Created: concepts/five-hu-rebellion-reframed.md (五胡亂華新解，Ep06)
- Created: concepts/non-chinese-state-systems.md (胡族國家體制，Ep07)
- Created: concepts/jin-southward-migration.md (晉室南渡，Ep08)
- Created: concepts/northern-wei-xiaowen-reforms.md (孝文帝改革，Ep09)
- Created: concepts/post-three-kingdoms-guanlong.md (後三國與關隴，Ep10)
- Created: concepts/sui-tang-imperial-birth.md (隋唐帝國與科舉，Ep11/15)
- Created: concepts/peach-blossom-spring-grassroots.md (桃花源與基層，Ep12)
- Created: concepts/confucian-state-buddhism.md (儒教與佛教，Ep13)
- Created: concepts/east-asian-world-formation.md (東亞世界形成，Ep14)
- Updated: index.md (新增14個entry，page count 27→41)
- Core framework: 內亞視角、去黃河文明化、胡族主體性、多中心東亞世界
- Key corrections incorporated: 科舉真正成熟在宋代(Ep11)、五胡主體性(Ep06)、編戶齊民(Ep05)

## [2026-04-25] ingest | 教育會考數學科題庫建立 + LaTeX 驗證
- Source: session (math-exam-guardian repo + KaTeX validation)
- Created: sources/exam-question-bank-math-2026-04-25.md
- Updated: concepts/exam-question-bank-qa-system.md (新增數學科差異：KaTeX pipeline、常見錯誤類型、為何不用regex、部署架構)
- Updated: index.md (新增 source entry, page count 25→27)
- Skill created: latex-render-pipeline (data-science，含 check_latex.js)
- Key findings: 9 KaTeX errors fixed; \\  vs \\, in \left(); \x08 control char; double backslash; build_type=legacy for GitHub Pages without workflow token

## [2026-04-23] ingest | 台灣每週新聞 W16 + W16實戰教訓
- Source: ~/.hermes/taiwan-weekly-edu/2026-04-W16/01_collected_news.json (72則中央社新聞, 2026-04-19~04-23)
- Created: sources/2026-04-23_taiwan-news-2026-W16-analysis.md
- Updated: concepts/taiwan-weekly-edu-pipeline.md (W16實戰教訓：SRT參數順序bug、quota耗盡原則、三領域空洞化、色音色區分、來源品質)
- Updated: concepts/news-intelligence-methodology.md (W16觀察：三領域空洞化、來源品質分級)
- Updated: index.md (新增W16 source entry, 更新pipeline/方法論描述)
- Skill created: tw-weekly-edu-w16-lessons (媒體技能，含完整W16實戰知識)

## [2026-04-13] ingest | LLM Wiki Pattern (karpathy)
- Source: karpathy's gist on LLM Wiki pattern
- Created: sources/2026-04-13_karpathy-llm-wiki-pattern.md
- Key concept: Persistent compounding wiki vs RAG re-retrieval; 3-layer architecture; ingest/query/lint operations

## [2026-04-13] ingest | Harness Pattern (celesteanders/harness)
- Source: celesteanders/harness GitHub repo
- Created: concepts/harness-pattern.md
- Key concept: Generator + Evaluator pattern; workflow Intake→Triage→Clarify→Plan→Execute→Evaluate→Done; session protocol; max 2 retry cycles; JSON plan rules; evaluator scoring

## [2026-04-13] ingest | Harness Repo (celesteanders/harness)
- Source: celesteanders/harness GitHub repo
- Created: sources/2026-04-13_celesteanders-harness.md
- Key concept: Generator + Evaluator pattern; workflow; session protocol; evaluator scoring; design decisions
## [2026-04-13] synthesis | Dual-Core System
- Created: concepts/dual-core-system.md
- Synthesis of LLM Wiki + Harness into unified agent operating model
- Session startup protocol, knowledge filing flow, execution flow

## [2026-04-14] ingest | 在日印度勞動力統計原始資料
- Source: 法務省在留外國人統計、厚勞省外國人雇用状況調査、入管庁特定技能統計
- Created: sources/2026-04-14_japan-india-labor-statistics.md
- Key data: 2023年印度人46,262人；2024年估54,000人；印度人特定技能<2%未進前10

## [2026-04-14] create | 在日印度勞動力結構分析概念頁
- Created: concepts/japan-india-labor-analysis.md
- Key concept: 6種偏誤識別；數據鴻溝；置信度原則；多代理審查框架
- Related: concepts/multi-agent-review-methodology.md

## [2026-04-14] create | 多代理人審查方法論反思
- Created: concepts/multi-agent-review-methodology.md
- Key concept: Generator+Critic+Researcher框架；delegate_task問題記錄；數據優先原則；置信度傳染原則
- Related: concepts/japan-india-labor-analysis.md

## [2026-04-14] create | multi-agent-labor-statistics-review skill
- Created: ~/.hermes/skills/research/multi-agent-labor-statistics-review/
- Framework: 數據驗證代理 + 偏誤審查代理 + 政策評估代理
- Triggers: 外國人雇用統計報告審查；在台/在日/在韓外國勞工結構分析

## [2026-04-16] ingest | The Wandering Inn 正傳第一章廣播劇製作

### 來源
- https://wanderinginn.com/2017/03/03/rw1-00/

### 關鍵問題發現
1. **圓括號格式不相容**：`gradio-tts-script-pipeline` regex `([^\\s【】(（)..!！?？:：]+)` 會把 `(NARRATOR)` 當角色名，正確格式為 `Narrator: ...`
2. **acompressor threshold**：新版 ffmpeg 需線性值（0.1 = -20dB），設 `-20` 會 out of range
3. **acompressor makeup**：最小值為 1，設 0 會 out of range
4. **英文不需要 OpenCC**：中文需 `t2s` 轉換，英文直接送英文即可

### 產出
- 腳本：156句（Narrator 125 + Erin 31），25分鐘
- 音色：`narrator_ref.wav`（553KB）、`erin_ref.wav`（538KB）
- 腳本：`~/.hermes/scripts/tts_wi9_ep01.py`、`merge_wi9_ep01.py`
- 最終 MP3：24MB，`WI9_EP01_The_Wandering_Inn_Ch1.mp3`
- Wiki：`concepts/tts-radio-drama-production.md`

### 更新 Skills
- `gradio-tts-podcast-production`：流程三更新為完整後製管線
- `gradio-tts-script-pipeline`：Step 5/6 更新為後製管線

### 相關案例
- WI9_02（4集 EP01-04，17角色），2026-04-15

## [2026-04-22] ingest | 台灣每週教育影片製作管線 + 台股晨報系統

### 台灣每週教育影片（taiwan-weekly-edu）
- cron 每週一 07:00 執行，9領域（國際、社會、政治、影視、音樂、文化、教育、財經、科技）
- 完整流程：新聞→題材挑選→腳本撰寫→圖片生成→語音生成→影片組裝→字幕→YouTube上傳
- **FFmpeg subtitles filter 限制**：subtitles/drawtext filter 皆不存在，MP4 不支援 SRT mux，只能產出 sidecar .srt 檔
- **YouTube scope 錯誤**：`youtube.force-ssl-auth` 無效，正確為 `youtube.force-ssl`
- **目錄結構注意**：圖片在 domain 子目錄，音頻為平面目錄
- **圖片出現延遲**：圖片出現後1秒再開始語音

### 台股產業晨報系統
- cron 每週一至五 07:00 執行，7大產業並行蒐集
- 日期判定：對照 taiwan_holidays_2026.md，週末發週末特刊
- **HTTP 432**：Web search 大面積故障，標注 ⚠️ 資料有限
- **HTTP 529**：伺服器高負載，重試或以近期趨勢補充
- **存檔路徑**：`/root/` vs `/Users/johnchen/`，正確為 `~/.nanobot/`

### 本次新增 Wiki 頁面
- `concepts/taiwan-weekly-edu-pipeline.md`
- `concepts/taiwan-stock-morning-report.md`
- `concepts/historical-sociology-methodology.md`
- `sources/2026-04-22_six-towns-rebellion-research.md`

## [2026-04-22] ingest | 六鎮之亂動員機制研究（Conditional Pass）

### 研究概況
- 5位研究者協作（軍事人類學、社會網絡、語義象徵、物質文化、比較歷史）
- 核心論點：「隱形部落」非正式網絡是叛亂動員的社會基礎
- 結果：經兩輪 Generator+Evaluator 審查，Conditional Pass

### 審查發現的8個問題（已全部修正）
1. 唐長孺、內田吟風幽靈引用 → 已從書目移除
2. 《北史》卷6高歡傳引用錯誤 → 已移除
3. 爾朱榮墓誌（懷疑不存在）→ 完全移除
4. 高文献墓誌（身份存疑）→ 降級為存疑待考
5. 元乂墓誌（無法驗證）→ 降級為存疑待考
6. Barth情感維度歸屬爭議 → 已加說明區分
7. 個案代表性未說明 → 已加⚠️方法論說明
8. 比較歷史方法未說明 → 已加分析性比較vs歷史性類比說明

### 方法論創新
- A/B/C級史料分級制度
- 幽靈引用識別與處理
- 理論演繹 vs 理論類比
- Conditional Pass 審查結論

## [2026-04-22] create | hermes-memos 靜態網站建立
- Repo: github.com/killkli/hermes-memos (public)
- Framework: MkDocs + Material for MkDocs
- Deployed: https://killkli.github.io/hermes-memos/
- GitHub Actions workflow: `.github/workflows/deploy.yml` (ubuntu-latest, mkdocs-material)
- Auto-deploy on every push to main
- Synced wiki pages: 12 → 14 concepts + sources

## [2026-04-22] ingest | 南北朝六鎮之亂研究地圖
- Created: concepts/northern-dynasties-six-towns-research.md
- 五研究者發現地圖：A軍事人類學/B社會網絡/C語義象徵/D物質文化/E比較歷史
- 書籍四層模型（部落/軍鎮/編戶/官僚門第）
- Conditional Pass（8個問題全部解決）
- 爾朱榮墓誌不存在、高文献為高閭筆誤、元簡為造像記（非墓誌）
- J.C. Harrocks無法驗證（已從所有章節移除）

## [2026-04-22] ingest | 新聞情報方法論
- Created: concepts/news-intelligence-methodology.md
- 9領域分類框架；Teachable Topic挑選標準；知識點結構
- Script撰寫格式（narration_A/narration_B）；圖片Prompt原則（英文、場景化）
- 1秒法則；FFmpeg subtitles filter不支援MP4 mux

## [2026-04-22] ingest | 台灣每週新聞領域分析 W17
- Created: sources/2026-04-21_taiwan-news-2026-W17-analysis.md
- 來源：~/.hermes/taiwan-weekly-edu/2026-04-W17/01_collected_news.json
- 發現：舊聞問題嚴重（社會/政治領域摻雜1-3個月前舊聞）
- 領域重疊問題（影視/音樂/文化界線模糊）
- 本週教學推薦：超高齡社會、AI GDP預測、量子科技、K型經濟

## [2026-04-22] merge | Wiki integration from external source
- Merged external wiki (25 files) into current wiki
- Added 10 new concepts: agent-environments, cron-job-architecture, engineering-investment-research-report, linuxkit-bootable-usb, multi-agent-stock-analysis-system, oauth-integration, reflection-session-2026-04-16, skill-vs-codeblock, taiwan-daily-music-pipeline, youtube-upload-patterns
- Added SCHEMA.md (was completely missing — core structural gap)
- Added entities/john-chen.md (conflict resolved: existing empty file replaced with full content)
- Added queries/ directory (was missing structure)
- Added 4 raw/articles: cron-youtube-upload, cron-job-debug, linuxkit-usb-build, taiwan-daily-music-cron
- Updated index.md: rebuilt with merged content, 12 → 22 pages
- Updated log.md: appended integration record
- Existing unique content preserved: 12 concepts + sources/
- Lint pending: will run full cross-link verification

## [2026-04-22] ingest | 比特幣 M2 貨幣理論批判研究
- Ingested 2 raw sources + 1 concept page from BTC M2 research session
- Sources: youtube-btc-m2-analysis-2026-04-22 (區塊鏈日記), econ-analysis-btc-m2-critique-2026-04-22 (經濟學批判)
- Concept: bitcoin-m2-monetary-theory — 奧派敘事 vs 經濟學批判對照；鮑莫爾成本病；機構集中化矛盾；選美理論
- Key finding: BTC 2022 通膨高峰期暴跌 78%，比特幣避險屬性學術證據薄弱
- Added to Finance & Economics section in index
