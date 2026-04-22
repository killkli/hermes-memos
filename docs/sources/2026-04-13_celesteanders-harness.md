---
title: "Celeste Anderson's Harness Repo"
created: 2026-04-13
updated: 2026-04-13
type: source
tags: [harness, generator-evaluator, autonomous-ai, agentic-ai, claude-code]
sources: [self]
---

# Harness Repo — celesteanders/harness

Source: https://github.com/celesteanders/harness

## What It Is

A minimal harness for autonomous AI-driven software development with Claude Code. Implements the generator + evaluator pattern from Anthropic, OpenAI, and community research.

## Core Workflow

```
Feedback --> Triage --> Clarify --> Plan --> Execute (TDD) --> Evaluate --> Done
```

## Key Architecture

### Generator + Evaluator Pattern

> "Agents grade their own work too generously. A skeptical evaluator in a fresh context provides honest feedback."

The generator implements; the evaluator judges. Never the same agent doing both.

### Session Protocol (Execute Phase)

1. Orient — read plan, progress notes, git history
2. Verify baseline — run tests to confirm existing code works
3. Implement — TDD for backend (failing test first), direct for frontend
4. Verify — run tests again
5. Evaluate — spawn evaluator subagent (HARD GATE)
6. Update state — mark complete only after PASS, commit, update progress

## Key Design Decisions

| Decision | Rationale |
|----------|-----------|
| Separate evaluator session | Agents rate own work too generously |
| JSON plans, not Markdown | Structured state survives context resets; machine-parseable |
| Git as safety net | Recovery is always one `git reset` away |
| Acceptance criteria as contract | Every task has testable ACs evaluator checks mechanically |
| Max 2 retry cycles | Prevents infinite loops; forces human escalation |

## Structure

```
.harness/
├── plans/           Per-ticket plans (JSON)
├── eval_feedback/   Evaluator verdict JSONs
├── progress.md      Session notes
├── runner.py        Headless orchestrator (batch)
└── evaluator.py     Skeptical QA agent
```

## Evaluator Scoring Rules

- **OVERALL = FAIL** if any of: verification, acceptance_criteria, test_coverage, no_placeholders is FAIL
- `tdd_compliance = WARN` does NOT fail overall
- When in doubt → FAIL (false positives preferred)

## Research Sources

| Document | Source |
|----------|--------|
| Harness Design (Mar 2026) | Anthropic |
| Effective Harnesses (Nov 2025) | Anthropic |
| Harness Engineering for Codex (Feb 2026) | OpenAI |
| Ralph Wiggum Technique (Jul 2025) | Geoffrey Huntley |
