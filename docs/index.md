# Wiki Index

> Content catalog. Every wiki page listed under its type with a one-line summary.
> Read this first to find relevant pages for any query.
> Last updated: 2026-04-22 | Total pages: 12

## Entities
<!-- People, places, organizations, products -->

## Concepts

- [[concepts/llm-wiki-pattern]] — Persistent compounding wiki vs RAG; 3-layer architecture; ingest/query/lint operations (source: karpathy gist)
- [[concepts/harness-pattern]] — Generator + Evaluator autonomous development; TDD session protocol; evaluator scoring rules; max 2 retry cycles (source: celesteanders/harness repo)
- [[concepts/dual-core-system]] — LLM Wiki (knowledge input) + Harness (execution output) — how they reinforce each other; session startup protocol; mutual reinforcement
- [[concepts/japan-india-labor-analysis]] — 在日印度勞動力結構分析案例；6種偏誤識別；數據鴻溝與置信度原則；多代理審查方法論
- [[concepts/multi-agent-review-methodology]] — 多代理人審查系統方法論；Generator+Critic+Researcher框架；delegate_task問題記錄；數據優先/置信度傳染原則
- [[concepts/tts-radio-drama-production]] — TTS廣播劇完整流程：腳本格式、ref_wav音色建立、批次生成、後製管線（loudnorm+acompressor）；英文內容不用OpenCC；圓括號格式不相容問題
- [[concepts/historical-sociology-methodology]] — 歷史社會學研究方法論；A/B/C級史料分級；幽靈引用識別與處理；Barth理論應用層次；分析性比較vs歷史性類比；墓誌偏差與循環性問題；個案代表性處理
- [[concepts/taiwan-weekly-edu-pipeline]] — 台灣每週教育影片製作管線；9領域每週新聞→圖片→語音→影片→YouTube；目錄結構；FFmpeg字幕限制；YouTube上傳scope問題
- [[concepts/taiwan-stock-morning-report]] — 台股產業晨報系統；7大產業並行蒐集；日期判定邏輯；HTTP 432/529錯誤處理；存檔路徑注意；cron自動化

## Comparisons
<!-- Side-by-side analyses -->

## Synthesis

- [[concepts/dual-core-system]] — Dual-core architecture: wiki for knowledge compounding, harness for execution rigor. Unified by memory and structured artifacts.

## Sources

- [[sources/2026-04-13_karpathy-llm-wiki-pattern]] — Karpathy's LLM Wiki pattern article
- [[sources/2026-04-13_celesteanders-harness]] — Celeste Anderson's harness repo (Generator + Evaluator pattern)
- [[sources/2026-04-14_japan-india-labor-statistics]] — 在日印度勞動力原始資料彙編；法務省/厚勞省統計；特定技能國籍分佈；多代理人審查發現（2026-04-14）
- [[sources/2026-04-22_six-towns-rebellion-research]] — 六鎮之亂動員機制研究；隱形部落理論；五研究者協作流程；Conditional Pass審查結論（2026-04-22）

## 產業分析

| 主題 | 日期 | 摘要 |
|------|------|------|
| 台股晨報知識消化 | 2026-04-13 | 三賢者批判分析：半導體AI、金融、傳產航運、網通、生技、綠能、零售七大產業 |
| 霍爾木茲海峽危機 | 2026-04-13 | 美伊停火協議後的五賢者批判分析：基本面、國際政治、強制外交、技術面、台灣產業影響 |
| 台股晨報知識消化 | 2026-04-13 | 三賢者批判：鴻海25個月增長年增45%為成長周期減速點；台灣能源進口結構性脆弱；2500億美元對美投資為經濟主權置換 |
| 在日印度勞動力結構審查 | 2026-04-14 | 多代理人審查系統：識別6種偏誤；確認印度人國籍別統計鴻溝；總量估算MEDIUM、產業/簽證分佈均LOW置信度；方法論反思 |
