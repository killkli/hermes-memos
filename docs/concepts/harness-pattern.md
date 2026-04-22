---
title: "Harness Pattern"
created: 2026-04-13
updated: 2026-04-13
type: concept
tags: [agentic-ai, harness, generator-evaluator, autonomous-ai, tdd, workflow]
sources: [../sources/2026-04-13_celesteanders-harness.md]
---

# Harness Pattern

> "Agents grade their own work too generously. A skeptical evaluator in a fresh context provides honest feedback."
> — Celeste Anderson / Anthropic Engineering

## Definition

A **Generator + Evaluator** multi-agent pattern for autonomous software development. Separates the agent doing the work from the agent judging it, creating an honest feedback loop.

## Core Workflow

```
Feedback --> Triage --> Clarify --> Plan --> Execute (TDD) --> Evaluate --> Done
```

## The Problem It Solves

Agents tend to confidently praise their own work even when quality is mediocre. Self-evaluation fails because:
- No objective distance from the implementation
- Cognitive bias toward confirming one's own decisions
- Subjective tasks have no binary check like tests provide

## Architecture

```
┌──────────────┐    ┌──────────────┐    ┌──────────────┐
│   Generator  │───>│  Evaluator   │───>│   Verdict    │
│   (Build)    │    │  (Skeptical) │    │  PASS | FAIL │
└──────────────┘    └──────────────┘    └──────────────┘
```

**Generator:** Implements features one at a time (TDD for backend)
**Evaluator:** Skeptical QA agent in fresh context; finds problems not praise

## Session Protocol

Every task follows this sequence:
1. **Orient** — Read plan, progress notes, git history
2. **Verify baseline** — Run tests to confirm existing code works
3. **Implement** — Backend: TDD (failing test first). Frontend: direct implement
4. **Verify** — Run verification command
5. **Evaluate** — Spawn evaluator subagent (HARD GATE)
6. **Update state** — Mark complete only after PASS, commit, append to progress

## Hard Gate Rules

- Task is NOT complete until evaluator returns `OVERALL: PASS`
- If FAIL: fix → re-evaluate with new evaluator subagent
- **Max 2 retry cycles** → if still failing, escalate to human
- False positives preferred over missed issues

## Evaluator Scoring

| Check | FAIL if... |
|-------|-----------|
| `verification` | verification command fails |
| `acceptance_criteria` | any AC not met |
| `test_coverage` | tests don't actually verify ACs |
| `no_placeholders` | TODO/FIXME/pass/NotImplementedError found |
| `tdd_compliance` | WARN only (doesn't fail overall) |

**OVERALL = FAIL if ANY of first four is FAIL**

## JSON Plan Rules

Plans in `.hermes/tasks/plans/{slug}.json`:
- Never remove or edit task descriptions or AC text
- Only flip status: `pending → in_progress → complete`
- Never reorder task IDs
- AC text is immutable — only flip `verified: true` after evaluator confirms

## Key Research Findings

| Finding | Source |
|---------|--------|
| Context windows are the constraint; structured artifacts are the solution | Anthropic |
| Separate generation from evaluation | Anthropic |
| One task per session | Anthropic |
| Verify before building (compounding bugs across sessions) | Anthropic |
| Humans steer, agents execute | OpenAI |
| Repository is single source of truth | OpenAI |
| LLM quality reflects operator skill — tune prompts | Ralph Wiggum |

## Related Concepts

- [LLM Wiki Pattern](/concepts/llm-wiki-pattern/) — Knowledge management (complementary)
- [Agentic Workflows](/concepts/agentic-workflows/) — General agent workflow patterns
