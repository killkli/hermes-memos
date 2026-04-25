---
title: Agent Environments
created: 2026-04-16
updated: 2026-04-16
type: concept
tags: [agent, architecture, hermes, cron, sandbox]
sources: [raw/articles/cron-youtube-upload-session-2026-04-16.md]
---

# Agent Environments

## Definition

Hermes Agent 系統中存在**三種截然不同的執行環境**，每種環境的工具可用性、Python 執行模型、和整合方式都不同。混淆它們是大多數整合失敗的根本原因。

## The Three Environments

### 1. Agent Environment（正常對話）

AI model 在對話中透過 **tool call** 與系統互動。

**特徵：**
- AI model 生成 tool calls（JSON 格式）
- Hermes Agent 的 `model_tools.py` 解析並路由到對應 tool handler
- Tools 執行結果以 `tool_result` message 形式回到 model
- **Skills 可用**：skill tools（`youtube_upload` tool 等）已註冊在 Hermes tool registry，可被呼叫
- `sys.path.insert` + skill import **可以運作**，因為 skill scripts 在同一個 filesystem

```
User → Model → Tool Call → Hermes Tool Registry → Tool Handler → Result → Model → Response
```

### 2. Cron Environment（獨立 Python Session）

Cron job 由 scheduler 觸發，在**隔離的 Python venv** 中執行獨立的 Python session。

**特徵：**
- 沒有 AI model，沒有 tool call
- 沒有 Hermes tool registry
- `skills` 欄位對 cron 無意義（只用於 agent 的 tool routing）
- 使用獨立的 venv：`~/.hermes/cron/venv/`
- 主機的 `site-packages` 與 cron venv 完全隔離
- **Skill tool 完全不存在**，任何 `sys.path.insert` + import skill 都無法運作
- **只能靠自己**：所有邏輯必須完整內嵌在 cron prompt 的 Python code block 裡

```
Cron Scheduler → Python venv → Python code block (stdib + pip packages only)
```

### 3. execute_code Sandbox（隔離 Python Sandbox）

`execute_code` tool 在一個隔離的沙盒環境中執行 Python 程式碼。

**特徵：**
- 沒有 AI model，沒有 tool call
- 沒有 Hermes tool registry
- 使用特殊的 hermes_tools 內建模組（`read_file`, `terminal` 等）
- 環境變數隔離：無法直接讀到 shell 環境變數，需用 `subprocess` 繞道
- 與 cron 環境相同：**Skill tool 不存在**
- **只能靠自己**：需要 API key 時，用 `subprocess` + `source ~/.vcenvs && echo $VAR` 讀取

## Environment Comparison Table

| Dimension | Agent | Cron | execute_code |
|-----------|-------|------|-------------|
| AI Model | ✅ 有 | ❌ 無 | ❌ 無 |
| Tool Registry | ✅ Hermes tools | ❌ 不存在 | ❌ 不存在 |
| Skill Tools | ✅ 可用 | ❌ 無效 | ❌ 無效 |
| `sys.path.insert` + import | ✅ 可用 | ❌ 無效 | ❌ 無效 |
| OAuth 瀏覽器互動 | ✅ 可以 | ❌ 不行 | ❌ 不行 |
| 環境變數直接讀取 | ✅ 可以 | ❌ venv 隔離 | ❌ 需 subprocess |
| 可否串接外部 API | ✅ tools | ❌ 需內嵌 SDK | ❌ 需內嵌 SDK |
| 獨立 venv | ❌ 用主機環境 | ✅ `~/.hermes/cron/venv/` | ❌ 用 sandbox |

## Why the Distinction Matters

### The Cardinal Sin: Skill Import in Cron

```
❌ 錯誤認知：
"cron job 有 'skills': ['youtube-upload']，所以 youtube-upload 可以用"

✅ 實際情況：
"skills 欄位只對 agent 環境有意義。Cron 是 bare Python，skill tool 不存在"
```

### The Pattern That Works

在 Cron 或 execute_code 環境中，任何外部 API 串接都必須：
1. 把完整的 SDK 邏輯（OAuth + API call）**全部內嵌**到 Python code block
2. 不依賴 `sys.path.insert` 或任何 skill import
3. 確保 cron venv 已安裝所需 pip packages
4. OAuth token 必須事先預授權（環境無法開瀏覽器互動）

## Related Concepts

- [Skill vs Codeblock](/hermes-memos/concepts/skill-vs-codeblock/) — Self-contained code for non-interactive environments
- [OAuth Integration](/hermes-memos/concepts/oauth-integration/) — OAuth patterns for non-interactive environments
- [YouTube Upload Patterns](/hermes-memos/concepts/youtube-upload-patterns/) — YouTube API integration applied to cron
- [Cron Job Architecture](/hermes-memos/concepts/cron-job-architecture/) — Cron system design details
- [Multi-Agent Stock Analysis](/hermes-memos/concepts/multi-agent-stock-analysis-system/) — How environment differences affect the stock analysis pipeline
