---
title: "LLM Wiki Pattern"
created: 2026-04-13
updated: 2026-04-13
type: source
tags: [knowledge-management, llm, rag, wiki, karpathy]
sources: [self]
---

# LLM Wiki Pattern

Source: Andrej Karpathy's Gist — https://gist.githubusercontent.com/karpathy/442a6bf555914893e9891c11519de94f/raw/ac46de1ad27f92b28ac95459c782c07f6b8c964a/llm-wiki.md

## Core Idea

Traditional RAG re-discovers knowledge from scratch on every query. The LLM Wiki pattern instead has the LLM **incrementally build and maintain a persistent wiki** — a structured, interlinked collection of markdown files.

> "Instead of just retrieving from raw documents at query time, the LLM incrementally builds and maintains a persistent wiki... When you add a new source, the LLM doesn't just index it for later retrieval. It reads it, extracts the key information, and integrates it into the existing wiki — updating entity pages, revising topic summaries, noting where new data contradicts old claims."

## Key Distinction

| | RAG | LLM Wiki |
|--|-----|---------|
| Knowledge retrieval | Re-discovers from scratch each query | Already compiled, cross-referenced |
| New source | Index for later retrieval | Integrates into existing pages |
| Contradictions | May rediscover conflicting info | Already flagged and noted |
| Synthesis | Re-done every query | Already synthesized |
| Compounding | No | Yes — grows richer with every source |

## Architecture

```
Raw Sources (immutable) → Wiki (LLM-owned markdown) → Schema (AGENTS.md)
```

**3 Layers:**
1. **Raw Sources** — Immutable collection of source documents. LLM reads but never modifies. Source of truth.
2. **The Wiki** — LLM-generated markdown files. Includes summaries, entity pages, concept pages, comparisons, synthesis. LLM owns entirely.
3. **The Schema** — Configuration file (AGENTS.md) defining wiki structure, conventions, workflows. Co-evolved over time.

## Operations

### Ingest
1. LLM reads the source
2. Discusses key takeaways with user
3. Writes summary page in wiki
4. Updates the index
5. Updates relevant entity and concept pages
6. Appends entry to the log

A single source may touch **10-15 wiki pages**.

### Query
1. LLM searches wiki for relevant pages
2. Reads them and synthesizes answer with citations
3. Good answers can be filed back into the wiki as new pages

### Lint
Periodically have the LLM health-check:
- Contradictions between pages
- Stale claims superseded by newer sources
- Orphan pages with no inbound links
- Missing cross-references
- Data gaps fillable with web search

## Indexing

**index.md** — Content-oriented catalog. Lists every page with one-line summary, organized by category. LLM reads this first when answering queries.

**log.md** — Chronological append-only record:
```
## [2026-04-02] ingest | Article Title
```

## Use Cases

- Personal (goals, health, psychology)
- Research (deep topic exploration over weeks/months)
- Reading a book (build pages for characters, themes)
- Business/Team (internal wiki fed by Slack, meetings)
- Competitive analysis, due diligence, trip planning
