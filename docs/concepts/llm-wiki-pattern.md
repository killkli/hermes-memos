---
title: "LLM Wiki Pattern"
created: 2026-04-13
updated: 2026-04-13
type: concept
tags: [knowledge-management, llm, rag-alternative, memory, agentic-ai]
sources: [../sources/2026-04-13_karpathy-llm-wiki-pattern.md]
---

# LLM Wiki Pattern

> "Instead of just retrieving from raw documents at query time, the LLM incrementally builds and maintains a persistent wiki."

## Definition

A knowledge management approach where an LLM incrementally builds and maintains a persistent, compounding wiki artifact. Unlike traditional RAG (which re-discovers knowledge from scratch per query), the wiki compiles knowledge once and keeps it current — cross-references already exist, contradictions are flagged, synthesis is already done.

## Core Principles

1. **Persistent, not ephemeral** — Knowledge compiled once, reused infinitely
2. **LLM owns the wiki** — Creates, updates, cross-references; human sources and asks
3. **Compounding** — Grows richer with every source and every query
4. **Immutable sources** — Raw documents never modified; corrections go in wiki pages

## Architecture

```
Raw Sources (Layer 1) → Wiki (Layer 2) → Schema (Layer 3)
```

- **Layer 1 — Raw Sources**: Immutable source documents (articles, papers, data)
- **Layer 2 — The Wiki**: LLM-owned markdown files (summaries, entities, concepts, comparisons, synthesis)
- **Layer 3 — Schema**: AGENTS.md defining structure, conventions, workflows

## Core Operations

| Operation | Description |
|-----------|-------------|
| **Ingest** | Read source → discuss → write summary → update index → update entity/concept pages → log. May touch 10-15 wiki pages. |
| **Query** | Search wiki → read pages → synthesize with citations → optionally file answer back |
| **Lint** | Health-check: contradictions, stale claims, orphan pages, missing cross-references |

## Key Distinction: Wiki vs RAG

| | RAG | LLM Wiki |
|--|-----|---------|
| Knowledge state | Re-discovered each query | Pre-compiled, persistent |
| New source | Index for retrieval | Integrate into existing pages |
| Contradictions | May rediscover conflicts | Already flagged |
| Synthesis | Re-done every time | Already done |
| Compounding | No | Yes |

## Division of Labor

- **Human**: Sources content, explores, asks questions
- **LLM**: Summarizes, cross-references, files, maintains consistency

## Related Concepts

- [[memory-consolidation]] — Memory as compact form, wiki as expanded form (planned page)
- [[knowledge-graph]] — Graph-structured knowledge representation (planned page)
- [[rag]] — Traditional retrieval approach (contrast, planned page)

## See Also

- [LLM Wiki Pattern Source](/hermes-memos/sources/2026-04-13_karpathy-llm-wiki-pattern/) — The source article
