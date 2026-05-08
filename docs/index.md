# Wiki Index

> Content catalog. Every wiki page listed under its type with a one-line summary.
> Read this first to find relevant pages for any query.
> Last updated: 2026-05-08 | Total pages: 89

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
- [[taiwan-industry-daily-2026-05-02]] — 台股晨報知識消化 2026-05-02；科技主線續強、金融有政策工具、零售僅見零星題材；屬訊號稀疏的高位輪動格局
- [[taiwan-industry-daily-2026-05-01]] — 台股晨報知識消化 2026-05-01；情緒回暖但結構分化；AI/資料中心/綠電延續，傳產與零售偏題材交易
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
- [OmniVoice TTS 本地遷移](/hermes-memos/concepts/omnivoice-tts-migration/) — OmniVoice TTS 本地遷移（2026-04-28）；VoxCPM → OmniVoice 兩階段架構；失敗音色處理原則
- [NTU OmniVoice 自主產線 v4](/hermes-memos/concepts/ntu-omnivoice-full-production-run/) — NTU OmniVoice 自主產線 v4（2026-04-28）；Ep001/02 CH01試做版發現規格缺口（每EP應有5CH）
- [TTS 語音處理新規則](/hermes-memos/concepts/tts-voice-processing-rules/) — TTS語音處理新規則（2026-04-28）；ref_denoise禁用、48kHz硬性要求、圓括號語氣描述處理、ffmpeg concat音頻流丟失
- [Threads 登入與內容瀏覽](/hermes-memos/concepts/threads-login-browsing/) — Threads登入與內容瀏覽（2026-04-28）；搜尋頁死鎖陷阱、雲嘉彰美食/溫馨新聞過濾偏好
- [NTU Ep002 製作與音頻修復](/hermes-memos/concepts/ntu-history-ep002-production/) — NTU Ep002 製作與音頻修復（2026-04-27~05-08）；24kHz降頻bug根因確認、48kHz硬性要求、五章腳本完成、Ep001 v4重建上傳
- [NTU Ep003 製作啟動](/hermes-memos/concepts/ntu-history-ep003-production/) — NTU Ep003 製作啟動（2026-04-28）；首個OmniVoice全流程集數、Style Bible導入、5章腳本完成、架構重整與Gradio Server統一
- [BOYO 英文文法教學影片專案](/hermes-memos/concepts/boyo-english-grammar-video-project/) — BOYO英文文法教學影片專案（2026-04-28啟動）；三冊教材改編、Lessons 1-14完成上傳YouTube、雙渲染模式、Warm Academy Series III 視覺重設計
- [AI 影片生成實戰 — Seedance 2.0](/hermes-memos/concepts/ai-video-generation-seedance/) — Seedance 2.0 Fast 影片生成完整實戰；OpenRouter 影片 API、Token 計算公式、錨點機制、成本紀律、繁中→簡中 Prompt 技巧
- [搞笑短片創作技能](/hermes-memos/concepts/comedy-short-video-production/) — 邵氏武俠風搞笑短片泛化技能；Fast Intake Mode、9 種短片格式、Comedy Mechanism Palette、固定輸出格式
- [BOYO Series III 視覺重設計](/hermes-memos/concepts/boyo-series-iii-visual-redesign/) — BOYO Series III「Warm Academy / 暖白學院」視覺重設計；暖米白底+主題藍+暖橙、課堂場景結構化佈局、開場圖卡規範

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
- [NTU Ep002/Ep003 Sessions](/hermes-memos/sources/ntu-history-ep002-ep003-sessions-2026-04-27-28/) — NTU Ep002/Ep003 + OmniVoice遷移 sessions（04-27~04-29）；Ep002音頻修復、架構重整、OmniVoice規劃、Ep003啟動
- [BOYO 英文文法 Sessions](/hermes-memos/sources/boyo-english-grammar-sessions-2026-04-28-29/) — BOYO英文文法影片專案 sessions（04-28~04-29）；專案啟動、課程拆分、教材章節拆分
- [Sessions Digest 2026-05-02~08](/hermes-memos/sources/sessions-2026-05-02-to-2026-05-08/) — Batch session digest（05-02~05-08）；15個 sessions；Seedance影片生成、邵氏搞笑技能、BOYO Warm Academy、台股晨報

## 產業分析

| 主題 | 日期 | 摘要 |
|------|------|------|
| 台股晨報知識消化 | 2026-05-02 | 科技主線續強、金融有政策工具、零售僅見零星題材；屬訊號稀疏的高位輪動格局 |
| 台股晨報知識消化 | 2026-05-01 | 情緒回暖但結構分化；AI/資料中心/綠電延續，傳產與零售偏題材交易 |
| 台股晨報知識消化 | 2026-04-30 | AI 主線延續但受惠集中；高利率高油價下資產品質分化；網通與綠電成半導體外溢受惠鏈 |
| 台股晨報知識消化 | 2026-04-29 | AI獲利了結vs供應鏈實質擴張背離；壽險CSM自律前兆；散裝vs貨櫃週期分化；覆蓋3/7產業（其餘API限速） |
| 台股晨報知識消化 | 2026-04-28 | 台股首見4萬點但結構虛胖；荷姆茲通行僅7艘；輝達市值5.26兆美元；NAND Q2漲價確認記憶體超級週期 |
| 台股晨報知識消化 | 2026-04-27 | 英特爾Q1 EPS大超預期暴漲23.6%；費半連漲18日創歷史；輝達5兆美元；超級央行周來臨 |
| 台股晨報知識消化 | 2026-04-26 | 美風電開發商法律戰略勝訴；Ford放棄特斯拉式初創改走效率路線；DeepSeek V4低价衝擊；廣達AI訂單能見度良好 |
| 台股晨報知識消化 | 2026-04-25 | DeepSeek V4開啟百萬上下文平民化時代；Fed鮑爾刑事調查終止華許接任預期升溫；VIX與S&P同向罕見警訊 |
| 台股晨報知識消化 | 2026-04-24 | 台股創高後爆天量洗盤1700點；費半逆勢上漲；台積電CPO報捷；散戶槓桿創25年新高；美光圍堵記憶體紅鏈 |
| 台股晨報知識消化 | 2026-04-23 | 聯發科連拉漲停；高含發量ETF暴漲；SK海力士Q1創紀錄；台積電亞利桑那先進封裝廠2029年量產 |
| 台股晨報知識消化 | 2026-04-22 | 外銷訂單911.2億美元創歷史新高；台股創收盤新高37605點；千金股盛況空前47檔；力積電Q2記憶體邏輯全線調漲 |
| 台股晨報知識消化 | 2026-04-21 | 日本7.4級地震暴露半導體供應集中風險；做多半導體成最擁擠交易；AT&T/T-Mobile合併申請重構電信業 |
| 台股晨報知識消化 | 2026-04-19 | 伊朗外長宣布荷姆茲對商船完全開放；油價暴跌10%；台積電法說利多發酵；離岸風電3-3期季底前拍板 |
| 台股晨報知識消化 | 2026-04-18 | 那指連12漲創歷史；輝達5,000億美元AI大單；SoftBank光學衛星通訊；平圴A-100利用率低水位 |
| 台股晨報知識消化 | 2026-04-17 | 台積電史上最正向法說；Q1 EPS 22.08元毛利率66.2%；輝達入股邁威爾強化矽光子；中信金Q1獲利王 |
| 台股晨報知識消化 | 2026-04-16 | CoWoS缺口2027年前難解；13家金控Q1大放異彩；AT&T/T-Mobile合併談判；軟體雲端取代半導體成資金新歡 |
| 台股晨報知識消化 | 2026-04-15 | 台股高檔震盪；記憶體景氣復甦；三AI伺服器供應鏈靚；金融存股以股息換資本利得 |
| 在日印度勞動力結構審查 | 2026-04-14 | 多代理人審查系統：識別6種偏誤；確認印度人國籍別統計鴻溝；總量估算MEDIUM、產業/簽證分佈均LOW置信度；方法論反思 |
| 台股晨報知識消化 | 2026-04-14 | 台股AI供應鏈輪動；CoWoS供需缺口加劇；記憶體景氣確認復甦；金控獲利年增近三成 |
| 霍爾木茲海峽危機 | 2026-04-13 | 美伊停火協議後的五賢者批判分析：基本面、國際政治、強制外交、技術面、台灣產業影響 |
| 台股晨報知識消化 | 2026-04-13 | 三賢者批判分析：半導體AI、金融、傳產航運、網通、生技、綠能、零售七大產業 |
| 台股晨報知識消化 | 2026-04-13 | 三賢者批判：鴻海25個月增長年增45%為成長周期減速點；台灣能源進口結構性脆弱；2500億美元對美投資為經濟主權置換 |
| 台股晨報知識消化 | 2026-04-13 | 台積電法說前市場觀望；三賢者批判分析；鴻海25個月增長年增45%為成長周期減速點 |
| 台股晨報知識消化 | 2026-04-12 | 台股創高後高檔整理；三賢者批判：半導體、金融、傳產航運、網通、生技、綠能、零售 |
| 台股晨報知識消化 | 2026-04-10 | 那指進入技術性牛市；輝達GB300量產延遲；Fed降息預期延後；生技製藥大併購潮 |
| 台股晨報知識消化 | 2026-04-07 | 美中貿易火仍在燒；半導體春燕來了？MSCI台灣比重凍漲；聯發科AI手機SoC翻身 |
| 台股晨報知識消化 | 2026-04-06 | 川普關稅2.0來襲；台灣半導體如何突圍；三賢者分析：半導體/AI、金融、傳產航運、網通、生技、綠能、零售 |
| 台股晨報知識消化 | 2026-04-02 | 台積電二度上調展望；CoWoS缺口加劇；AI伺服器春燕；金融存股族青睞 |
| 台股晨報知識消化 | 2026-04-01 | 美中科技戰升級；半導體供應鏈大洗牌；三賢者框架：半導體、金融、傳產航運、網通、生技、綠能、零售 |
| 台股晨報知識消化 | 2026-03-31 | 川普關稅引發台股恐慌；半導體法說在即；三賢者框架：半導體、金融、傳產航運、網通、生技、綠能、零售 |
| 台股晨報知識消化 | 2026-03-30 | 台股技術性回調；三賢者框架：半導體、金融、傳產航運、網通、生技、綠能、零售 |
| 台股晨報知識消化 | 2026-03-29 | 英特爾IDM 2.0曙光；三賢者框架：半導體、金融、傳產航運、網通、生技、綠能、零售 |
