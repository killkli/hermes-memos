---
title: "Dual-Core Agent System"
created: 2026-04-13
updated: 2026-04-13
type: synthesis
tags: [agentic-ai, knowledge-management, autonomous-ai, llm-wiki, harness]
sources: [../concepts/llm-wiki-pattern.md, ../concepts/harness-pattern.md]
---

# Dual-Core Agent System

> LLM Wiki Pattern handles **knowledge compounding** (input side).
> Harness Pattern handles **execution rigor** (output side).
> Together they form a complete autonomous agent.

## The Two Halves

```
Knowledge Side (Input)          Execution Side (Output)
┌──────────────────────┐       ┌──────────────────────┐
│   LLM Wiki Pattern    │       │   Harness Pattern    │
│                      │       │                      │
│  Raw → Ingest → Wiki │       │  Plan → Execute →    │
│  Wiki → Query →      │       │  Evaluate → Commit    │
│  Knowledge compounds │       │  Quality gates every  │
│  over time           │       │  deliverable         │
└──────────────────────┘       └──────────────────────┘
         ↑                              ↓
         └─────────── Memory ───────────┘
```

## Why Both?

| Side | What it solves | Without it |
|------|---------------|------------|
| **Wiki** | Knowledge evaporates after session; RAG re-discovers from scratch each time | Same facts rediscovered, never compounded |
| **Harness** | Agents grade their own work too generously; bugs compound across sessions | Quality degrades, no honest feedback, context exhaustion |

## Session Startup Protocol

Every session starts by orienting through all three knowledge layers:

1. **Memory** — What must survive every session (preferences, environment facts)
2. **Harness Plans** — `.hermes/tasks/plans/*.json` — active work items
3. **Progress Notes** — `.hermes/tasks/progress.md` — what happened last time
4. **Wiki** — if topic is known, read relevant concept/source pages for context

## Knowledge Filing Flow

```
New knowledge encountered
    │
    ├─► User corrections/preferences → memory (compact, durable)
    │
    ├─► Environment facts → memory (compact, durable)
    │
    ├─► Workflow discoveries → memory (compact) + offer wiki entry
    │
    └─► Significant concepts/sources → INGEST TO WIKI
            │
            ├─► Save raw source to sources/
            ├─► Create/update concept or entity page
            ├─► Update index.md
            ├─► Append to log.md
            └─► Create cross-references to existing pages
```

## Execution Flow (Harness)

```
Feedback received
    │
    ├─► Intake & Triage (categorize: bug/feature/improvement/chore)
    ├─► Clarify (ask concrete questions if needed)
    ├─► Plan (create JSON plan with acceptance criteria)
    │
    └─► For each task:
            │
            ├─► Orient (read plan, progress, git history)
            ├─► Verify baseline (run tests — confirm existing code works)
            ├─► Implement (TDD for backend, direct for frontend)
            ├─► Verify (run tests again)
            ├─► Evaluate (spawn skeptical evaluator subagent — HARD GATE)
            │       │
            │       ├─► PASS → mark complete, commit, update progress
            │       └─► FAIL → fix → re-evaluate (max 2 cycles → human escalation)
            └─► Next task...
```

## Mutual Reinforcement

When the wiki and harness work together:

- **Wiki supplies context** to the executor: "Previously we learned that..."
- **Harness discoveries feed the wiki**: "This workflow approach worked well, let's document it"
- **Memory provides instant recall**: session-start facts without reading files
- **Progress notes provide continuity**: what broke last time, what to watch for

## The Two Types of Artifacts

| Artifact | Format | Rule |
|----------|--------|------|
| Harness plans | JSON | Never remove/reorder; only flip status |
| Wiki pages | Markdown | Never modify raw sources; updates go in wiki pages |
| Progress notes | Markdown | Append-only; never delete old entries |
| Memory | Key-value | Compact; significant facts expand into wiki |

## Key Principle

> **"Structured artifacts survive context resets. Unstructured conversation does not."**

Everything important must be written to disk in a machine-parseable format.
