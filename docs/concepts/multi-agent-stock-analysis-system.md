---
title: Multi-Agent Stock Analysis System v3.0
created: 2026-04-12
updated: 2026-04-16
type: concept
tags: [hermes, automation, architecture, lesson]
sources: [session:20260412_062324, session:20260412_113528]
---

# Multi-Agent Stock Analysis System v3.0

## Overview

A 7-agent pipeline for engineering-grade investment research on Taiwan stocks. Deployed for 中磊 (5388), 立端 (6245), 上詮 (3363), and 晶心科 (6533).

## Architecture

```
Data Coordinator → Phase 2 (parallel)
                    ├── Technical Analyst
                    ├── Fundamental Analyst
                    ├── Chip Analyst (institutional)
                    └── Macro Analyst
               → Bear Advocate (devils advocate)
               → Chief Analyst (synthesis + final report)
```

**7 Agents total:**
1. Data Coordinator — collects real-time price, fundamentals, technicals, chip data
2. Technical Analyst — moving averages, volume, RSI, MACD
3. Fundamental Analyst — EPS, margins, revenue growth
4. Chip Analyst — foreign/institutional flow, margin balances
5. Macro Analyst — industry trends, AI/5G tailwinds
6. Bear Advocate — challenges bull case, identifies invalidation conditions
7. Chief Analyst — synthesizes all views into final report

## Report Structure (6 Layers)

1. **Facts** — observed data only
2. **Causality** — 3-layer chain (Industry → Company → Competitive)
3. **Bull/Bear** — bull and bear cases with explicit invalidation conditions
4. **Validation** — time dimensions [Past/Present/Future], verifiable indicators
5. **Technical** — technical chart analysis
6. **Strategy** — 3-scenario planning (Bull/Base/Bear), stop-loss, risk-reward

## 3-Scenario Planning Rule

- Bull + Base + Bear probabilities must sum to 100%
- Risk-reward ratio must be > 1 for any "Buy" recommendation
- Stop-loss must be precise to the dollar

## Key Findings by Stock

### 中磊 (Sercomm, 5388) — NOT YET ANALYZED
- Q1 2026 revenue surged 86% YoY (Wi-Fi 7 / DOCSIS 4.0 upgrade cycle)
- Wait for completion of 6245 error recovery first

### 立端 (Lanner Electronics, 6245) ✅
- Score: 60/100 | Rating: ★★★☆☆ Hold
- Bull: 88 / Base: 78 / Bear: 60
- Risk-reward: 0.38:1 ❌ NOT favorable
- Key risk: EPS -51% YoY, domestic funds 8-day zero position

### 晶心科 (Andes Technology, 6533) ✅
- Score: 45-52/100 | Rating: Neutral/Conservative
- March 2026 revenue +39.51% YoY
- RISC-V tailwinds but -12.73% operating margin
- ZeroDivisionError bug in chief_analyst.py (price/volume data anomaly)
- High liquidity risk (~394 shares/day)

### 上詮 (Coscot, 3363) — planned but not executed

## Critical Lessons

### ⚠️ Stock Code vs Company Name Mapping
> The system MUST verify stock code against company name BEFORE analysis.

| Stock Code | Correct Company | Historical Error |
|------------|----------------|-----------------|
| 5388 | 中磊 (Sercomm) | — |
| 6245 | 立端 (Lanner Electronics) | Was confused with 中磊 |
| 3363 | 上詮 (Coscot) | — |
| 6533 | 晶心科 (Andes Technology) | — |

**Anti-pattern:** Data coordinator returned company name without code verification. Chief analyst blindly used the wrong name.

**Fix:** Use `find-taiwan-stock-code` skill to resolve name → code, then verify via Yahoo Finance URL before any data collection.

### ⚠️ Bear Advocate Phase Failure
The Bear Advocate agent failed in both stock sessions (6245, 6533) — returned directory listing instead of analysis. The Chief Analyst had to synthesize without dedicated devil's advocate critique, lowering report quality.

### ⚠️ chief_analyst.py ZeroDivisionError
`upside_pct` calculation failed when `current_price` was parsed as 0 or null from raw JSON. Workaround: manually invoke Chief Analyst via `delegate_task` for high-level reasoning synthesis.

## Related Concepts

- [Agent Environments](/hermes-memos/concepts/agent-environments/) — Agent vs cron vs execute_code differences affect how the system runs
- [Skill vs Codeblock](/hermes-memos/concepts/skill-vs-codeblock/) — Analysis scripts vs skill imports in cron contexts
- [Cron Job Architecture](/hermes-memos/concepts/cron-job-architecture/) — Scheduling the multi-agent system as a cron job
- [Taiwan Daily Music Pipeline](/hermes-memos/concepts/taiwan-daily-music-pipeline/) — Practical application of these patterns in daily music production
- [Engineering Investment Research](/hermes-memos/concepts/engineering-investment-research-report/) — The mandatory framework embedded in every agent's system prompt
