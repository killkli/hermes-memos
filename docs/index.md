# Wiki Index

> Content catalog. Every wiki page listed under its type with a one-line summary.
> Read this first to find relevant pages for any query.
> Last updated: 2026-04-25 | Total pages: 41

## Entities

- [[john-chen]] — Primary Hermes user via Telegram; enforces engineering-grade investment standards, values authentic Taiwanese lyrics

## Concepts

### Core Systems (Hermes Agent)

- [LLM Wiki Pattern](/hermes-memos/concepts/llm-wiki-pattern/) — Persistent compounding wiki vs RAG; 3-layer architecture; ingest/query/lint operations (source: karpathy gist)
- [Harness Pattern](/hermes-memos/concepts/harness-pattern/) — Generator + Evaluator autonomous development; TDD session protocol; evaluator scoring rules; max 2 retry cycles (source: celesteanders/harness repo)
- [Dual-Core Agent System](/hermes-memos/concepts/dual-core-system/) — LLM Wiki (knowledge input) + Harness (execution output) — how they reinforce each other; session startup protocol; mutual reinforcement
- [Agent Environments](/hermes-memos/concepts/agent-environments/) — Three execution environments: Agent, Cron, execute_code — each with different tool access
- [Skill vs Codeblock](/hermes-memos/concepts/skill-vs-codeblock/) — When to use skill imports vs self-contained Python code block
- [Cron Job Architecture](/hermes-memos/concepts/cron-job-architecture/) — Cron system design, iteration limits, Telegram .env reading, YouTube import paths

### Skills & Integration

- [OAuth Integration](/hermes-memos/concepts/oauth-integration/) — OAuth 2.0 patterns for non-interactive (cron) environments
- [YouTube Upload Patterns](/hermes-memos/concepts/youtube-upload-patterns/) — YouTube Data API v3 integration patterns (agent vs cron mode)

### Domain Pipelines

- [Taiwan Daily Music Pipeline](/hermes-memos/concepts/taiwan-daily-music-pipeline/) — Daily 08:00 cron generating rap + ballad, auto-uploading to YouTube
- [Taiwan Weekly Edu Pipeline](/hermes-memos/concepts/taiwan-weekly-edu-pipeline/) — Taiwan weekly edu-video pipeline: 9 domains, news→image→TTS→video→YouTube; W16更新：SRT參數順序bug、quota耗盡處理原則、三領域空洞化觀察（2026-04-23）
- [Taiwan Stock Morning Report](/hermes-memos/concepts/taiwan-stock-morning-report/) — Taiwan stock morning report; 7 industries concurrent collection; HTTP 432/529 error handling; cron automation

### Research & Analysis

- [Multi-Agent Stock Analysis](/hermes-memos/concepts/multi-agent-stock-analysis-system/) — 7-agent pipeline for Taiwan stock analysis (5388/6245/3363/6533)
- [Engineering Investment Research](/hermes-memos/concepts/engineering-investment-research-report/) — Mandatory framework: 3-scenario, stop-loss precision, causality chains, no narrative-only
- [Multi-Agent Review Methodology](/hermes-memos/concepts/multi-agent-review-methodology/) — Multi-agent review system; Generator+Critic+Researcher framework; delegate_task issues; data-first / confidence contagion principles
- [Japan-India Labor Analysis](/hermes-memos/concepts/japan-india-labor-analysis/) — 在日印度勞動力結構分析案例；6種偏誤識別；數據鴻溝與置信度原則；多代理審查方法論

### History & Sociology

- [Historical Sociology Methodology](/hermes-memos/concepts/historical-sociology-methodology/) — 歷史社會學研究方法論；A/B/C級史料分級；幽靈引用識別與處理；Barth理論應用層次；分析性比較vs歷史性類比；墓誌偏差與循環性問題；個案代表性處理
- [Six Towns Rebellion Research](/hermes-memos/concepts/northern-dynasties-six-towns-research/) — 南北朝六鎮之亂研究地圖；五研究者發現地圖；Conditional Pass結論；書籍四層模型；墓誌方法論極限

### 魏晉南北朝史（甘懷真，NTU OCW）

- [魏晉南北朝歷史學方法論](/hermes-memos/concepts/wei-jin-historical-methodology/) — 歷史學方法論：論從史出、沒有黃河文明、胡華非二元對立、內亞視角（Ep01）
- [沒有單一漢族理論](/hermes-memos/concepts/no-single-han-identity/) — 沒有單一漢族理論：孝文帝改革是內亞性延續、關隴集團無純粹漢族血統（Ep01/09/10）
- [專業農民的發明](/hermes-memos/concepts/invention-of-professional-farmer/) — 專業農民的發明：編戶齊民是國家權力建構結果非自然狀態（Ep01/05/12）
- [東亞大移民](/hermes-memos/concepts/east-asian-great-migration/) — 東亞大移民：雙向多向族群移動、游牧vs漢族非種族對立（Ep03/04/06）
- [五胡亂華新解](/hermes-memos/concepts/five-hu-rebellion-reframed/) — 五胡亂華新解：拒絕負面框架、五胡主體性、內亞性與中國性共存（Ep06）
- [胡族國家體制與佛教王權](/hermes-memos/concepts/non-chinese-state-systems/) — 胡族國家體制與佛教王權：混合體制、軍將體制、佛教王權（Ep07）
- [晉室南渡與僑吳姓](/hermes-memos/concepts/jin-southward-migration/) — 晉室南渡與僑吳姓：政治博弈、北方世家與南方土著整合困境（Ep08）
- [北魏孝文帝改革的多元解釋](/hermes-memos/concepts/northern-wei-xiaowen-reforms/) — 北魏孝文帝改革的多元解釋：傳統漢化解釋vs內亞性替代理論（Ep09）
- [後三國時代與關隴集團](/hermes-memos/concepts/post-three-kingdoms-guanlong/) — 後三國時代與關隴集團：西魏→北周→隋→唐政權繼承、關隴軍事貴族網絡（Ep10）
- [隋唐帝國誕生與科舉演變](/hermes-memos/concepts/sui-tang-imperial-birth/) — 隋唐帝國誕生與科舉演變：科舉真正成熟在宋代、隋煬帝大運河戰略意義（Ep11/15）
- [桃花源與基層社會](/hermes-memos/concepts/peach-blossom-spring-grassroots/) — 桃花源與基層社會：歷史記憶與文學想像交匯、山越非編戶人群（Ep12）
- [儒教國家與佛教](/hermes-memos/concepts/confucian-state-buddhism/) — 儒教國家與佛教：皇帝即如來、門閥vs皇權、儒道佛融合（Ep13）
- [東亞世界的形成](/hermes-memos/concepts/east-asian-world-formation/) — 東亞世界的形成：批判中國中心天下觀、多中心互動網絡、日本/朝鮮/越南主體性（Ep14）

### Media Production

- [TTS Radio Drama Production](/hermes-memos/concepts/tts-radio-drama-production/) — TTS廣播劇完整流程：腳本格式、ref_wav音色建立、批次生成、後製管線（loudnorm+acompressor）；英文內容不用OpenCC；圓括號格式不相容問題

### Infrastructure

- [LinuxKit Bootable USB](/hermes-memos/concepts/linuxkit-bootable-usb/) — Building a bootable USB with Hermes Agent via LinuxKit (iso-efi format, Docker Hub rate limits)

### Meta / Reflection

- [News Intelligence Methodology](/hermes-memos/concepts/news-intelligence-methodology/) — 新聞情報方法論；9領域分類框架；Teachable Topic挑選標準；知識點結構；Script撰寫格式；圖片Prompt原則；W16更新：三領域空洞化觀察、來源品質分級（2026-04-23）
- [Reflection Session 2026-04-16](/hermes-memos/concepts/reflection-session-2026-04-16/) — Cross-session synthesis: patterns, outstanding items, open questions (updated 2026-04-22)

### Finance & Economics

- [Bitcoin M2 Monetary Theory](/hermes-memos/concepts/bitcoin-m2-monetary-theory/) — 比特幣 M2 貨幣理論批判；奧派敘事 vs 經濟學實證；鮑莫爾成本病；機構集中化風險；批判思維模式

## Comparisons
<!-- Side-by-side analyses -->

## Synthesis

- [Dual-Core Agent System](/hermes-memos/concepts/dual-core-system/) — Dual-core architecture: wiki for knowledge compounding, harness for execution rigor. Unified by memory and structured artifacts.

## 教育研究

### 題庫補正

- [Exam Question Bank QA System](/hermes-memos/concepts/exam-question-bank-qa-system/) — 國中教育會考題庫三代理人 QA 系統；題組文章補正流程；可用率85%；JSON `group_id`/`passage` 結構；最終部署至 GitHub Pages（2026-04-24）

## Sources (raw/)

- [LLM Wiki Pattern Source](/hermes-memos/sources/2026-04-13_karpathy-llm-wiki-pattern/) — Karpathy's LLM Wiki pattern article
- [Harness Pattern Source](/hermes-memos/sources/2026-04-13_celesteanders-harness/) — Celeste Anderson's harness repo (Generator + Evaluator pattern)
- [Japan-India Labor Statistics](/hermes-memos/sources/2026-04-14_japan-india-labor-statistics/) — 在日印度勞動力原始資料彙編；法務省/厚勞省統計；特定技能國籍分佈；多代理人審查發現（2026-04-14）
- [Taiwan Weekly News W17](/hermes-memos/sources/2026-04-21_taiwan-news-2026-W17-analysis/) — 台灣每週新聞領域分析；2026-W17九領域內容觀察；舊聞問題；教學主題推薦（2026-04-22）
- [Taiwan Weekly News W16](/hermes-memos/sources/2026-04-23_taiwan-news-2026-W16-analysis/) — 台灣每週新聞領域分析；2026-W16九領域72則新聞；CNA來源純淨無舊聞；三領域空洞化觀察；關鍵主題矩陣（2026-04-23）
- [Six Towns Rebellion Research](/hermes-memos/sources/2026-04-22_six-towns-rebellion-research/) — 六鎮之亂動員機制研究；隱形部落理論；五研究者協作流程；Conditional Pass審查結論（2026-04-22）
- [魏晉南北朝史 甘懷真 Source](/hermes-memos/sources/ntu-wei-jin-history-kan-huaijen-2026-04-25/) — 臺大開放式課程 魏晉南北朝史（甘懷真）15集逐字稿攝入；13個概念頁群；內亞視角與去中國中心化框架（2026-04-25）
- [Exam Question Bank Sessions](/hermes-memos/sources/exam-question-bank-sessions-2026-04-24/) — 教育會考英語科題庫補正完整 session（04-24主工作 + 04-23初始化）；13年份102-114年；三代理人 QA 流程；題組文章漏收問題與補正
- [Exam Question Bank Math](/hermes-memos/sources/exam-question-bank-math-2026-04-25/) — 教育會考數學科題庫建立 session（04-25）；數學科專用網站 math-exam-guardian；KaTeX 渲染錯誤發現與修正；9個錯誤修正（\\  vs \\,、\\x08 控制字元、雙反斜杠）；latex-render-pipeline skill 建立
- [Cron Job Debug Session](/hermes-memos/sources/cron-job-debug-session-2026-04-18/) — Session: web skill removal, iteration limit discovery
- [LinuxKit USB Build Session](/hermes-memos/sources/linuxkit-usb-build-session-2026-04-18/) — Session: LinuxKit USB project init, iso-efi format fix
- [Taiwan Daily Music Cron](/hermes-memos/sources/taiwan-daily-music-cron-2026-04-22/) — Session: full pipeline success, MiniMax hex format, .env reading
- [YouTube BTC M2 Analysis](/hermes-memos/sources/youtube-btc-m2-analysis-2026-04-22/) — YouTube 影片：比特幣對抗 M2 貨幣貶值論述；區塊鏈日記 25 分鐘批判分析
- [Econ Analysis BTC M2 Critique](/hermes-memos/sources/econ-analysis-btc-m2-critique-2026-04-22/) — 經濟學批判：M2–通膨關係弱化、比特幣避險屬性消失、機構集中化矛盾、鮑莫爾成本病

## 產業分析

| 主題 | 日期 | 摘要 |
|------|------|------|
| 台股晨報知識消化 | 2026-04-13 | 三賢者批判分析：半導體AI、金融、傳產航運、網通、生技、綠能、零售七大產業 |
| 霍爾木茲海峽危機 | 2026-04-13 | 美伊停火協議後的五賢者批判分析：基本面、國際政治、強制外交、技術面、台灣產業影響 |
| 台股晨報知識消化 | 2026-04-13 | 三賢者批判：鴻海25個月增長年增45%為成長周期減速點；台灣能源進口結構性脆弱；2500億美元對美投資為經濟主權置換 |
| 在日印度勞動力結構審查 | 2026-04-14 | 多代理人審查系統：識別6種偏誤；確認印度人國籍別統計鴻溝；總量估算MEDIUM、產業/簽證分佈均LOW置信度；方法論反思 |
