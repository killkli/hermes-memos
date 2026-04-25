# Wiki Schema

## Domain
Hermes Agent 系統工程、自動化工作流程、AI 工具整合、Agent 開發與維護。

## Conventions

- File names: lowercase, hyphens, no spaces (e.g., `cron-job-architecture.md`)
- Every wiki page starts with YAML frontmatter (required fields below)
- Wikilinks format: double-bracket notation with bare page name, no directory prefix
  - Example: `[Cron Job Architecture](/hermes-memos/concepts/cron-job-architecture/)` → links to concepts/cron-job-architecture.md
  - Example: `[[john-chen]]` → links to entities/john-chen.md
  - Note: do not include "concepts/" or "entities/" prefixes in actual wikilinks
- Use double-bracket links between pages (minimum 2 outbound links per page)
- When updating a page, always bump the `updated` date
- Every new page must be added to `index.md` under the correct section
- Every action must be appended to `log.md`
- Use `>` blockquotes for key insights or lessons
- Use `| Error | Cause | Fix |` tables for troubleshooting knowledge

## Frontmatter (required on every page)

```yaml
---
title: Page Title
created: YYYY-MM-DD
updated: YYYY-MM-DD
type: entity | concept | comparison | query | summary
tags: [tag1, tag2]
sources: [raw/articles/source-name.md]
---
```

## Tag Taxonomy

**Architecture & Systems:**
- `hermes` — Hermes Agent framework, CLI, gateway
- `cron` — Cron job system, scheduler
- `architecture` — System design, file layouts
- `automation` — Automated workflows, pipelines

**Skills & Tools:**
- `skill` — Hermes skill system, skill development
- `tool` — Individual tools, tool integration
- `integration` — API integrations, third-party services
- `oauth` — OAuth 2.0 authentication flows

**Platforms & Environments:**
- `agent` — Agent vs cron vs execute_code environments
- `sandbox` — Isolated execution environments
- `venv` — Python virtual environments

**Domains:**
- `youtube` — YouTube API, video upload
- `music` — AI music generation (MiniMax)
- `taiwan` — Taiwan-specific content pipelines

**Meta:**
- `lesson` — Key lessons, anti-patterns
- `pattern` — Recurring successful patterns
- `pitfall` — Known failure modes
- `workflow` — End-to-end workflow descriptions

## Page Thresholds

- **Create a page** when a concept/entity appears in 2+ sessions OR is central to one significant lesson
- **Add to existing page** when new information elaborates on an established topic
- **DON'T create a page** for trivial one-off facts or passing mentions
- **Split a page** when it exceeds ~200 lines — break into sub-topics
- **Archive a page** when fully superseded — move to `_archive/`, remove from index

## Update Policy

When new information conflicts with existing content:
1. Check dates — newer observations generally supersede older ones
2. If genuinely contradictory, note both positions with dates and context
3. Mark the contradiction in frontmatter: `contradictions: [other-page]`
4. Flag for user review

## Core Concepts (pre-defined)

These pages should always exist or be easily creatable:
- `agent-environments` — Agent vs cron vs execute_code environment differences
- `skill-development` — How to create and maintain skills
- `cron-job-architecture` — Cron system design
- `oauth-integration` — OAuth patterns in non-interactive environments
