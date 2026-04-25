---
title: Engineering Investment Research Report v1.0
created: 2026-04-12
updated: 2026-04-12
type: concept
tags: [finance, pattern, workflow, lesson]
sources: [session:20260412_062324, session:20260412_113528]
---

# Engineering Investment Research Report v1.0

## Overview

Mandatory checklist for stock analysis reports. Enforced by user (John Chen) to prevent narrative-only arguments and ensure every conclusion is verifiable, quantifiable, and actionable.

## Core Requirements

### 1. Three-Layer Causality Chain
Every narrative argument must trace through exactly 3 layers:
```
Industry → Company → Competitive
```
No single-layer "because AI is trending" arguments.

### 2. Evidence Classification
Each claim must be explicitly labeled:
- **Observed** — raw data directly from source
- **Explained** — interpretation of observed data
- **Inferred** — conclusion drawn from explained data (requires evidence trail)

### 3. Time Dimensions (mandatory)
Every analysis point must specify temporal scope:
- **[Past]** — historical performance, trends
- **[Present]** — current state
- **[Future]** — projections with specific milestones

### 4. Verifiable Indicators
Every narrative requires at least one concrete, falsifiable indicator:
- Revenue growth % (observable)
- Margin % (observable)
- Institutional flow (observable)
- Technical levels (observable)

### 5. Invalidation Conditions
Every bull/bear point must have explicit conditions that would invalidate it:
```
Bull case: [condition that, if true, disproves the bull]
Bear case: [condition that, if true, disproves the bear]
```

### 6. 3-Scenario Planning
All recommendations require three scenarios with probability sums = 100%:

| Scenario | Probability | Target Price | Assumption |
|----------|------------|-------------|------------|
| Bull | X% | $XX | [specific] |
| Base | Y% | $YY | [specific] |
| Bear | Z% | $ZZ | [specific] |

### 7. Risk-Reward Rule
**Buy recommendation requires:** Risk-reward ratio > 1

```
Risk-Reward = (Base Target - Stop-Loss) / (Entry - Stop-Loss)
            OR
            = Upside / Downside
```

### 8. Stop-Loss Precision
Stop-loss must be **precise to the dollar** — no rounding, no "around $X".

## Anti-Patterns (explicitly banned)

❌ "AI trend will drive growth" (narrative-only, no indicator)
❌ "Management is confident" (no quantification)
❌ "Risk-reward is attractive" (no calculation shown)
❌ Generic sector commentary without company-specific linkage

## Relationship to Multi-Agent System

This report framework is embedded in [Multi-Agent Stock Analysis](/hermes-memos/concepts/multi-agent-stock-analysis-system/). The Chief Analyst agent is responsible for enforcing these rules. The Bear Advocate catches violations before synthesis.

## Related Concepts

- [Multi-Agent Stock Analysis](/hermes-memos/concepts/multi-agent-stock-analysis-system/)
- [Skill vs Codeblock](/hermes-memos/concepts/skill-vs-codeblock/) — skill prompts must embed these rules in system prompts
