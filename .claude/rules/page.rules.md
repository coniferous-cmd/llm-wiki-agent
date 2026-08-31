---
paths:
  - "*"
---
## Page Format

Every wiki page uses this YAML frontmatter:

```yaml
---
title: "Page Title"
type: source | entity | concept | synthesis
tags: []
sources: []       # list of source slugs that inform this page
last_updated: YYYY-MM-DD
---
```

Use `[[PageName]]` wikilinks to link to other wiki pages.


### Concept Page Formats

Concept pages (`wiki/concepts/*.md`) have two variants:

**Standard Concept** — lightweight summary of an idea, framework, or theory.

```markdown
---
title: "ConceptName"
type: concept
tags: []
sources: []
last_updated: YYYY-MM-DD
---

## Definition
Brief definition.

## Key Claims
- Claim 1
- Claim 2

## Connections
- [[RelatedConcept]] — relationship
```

**Teaching Concept** — structured chapter for Sigma 1v1 tutoring. Use when a concept is mature enough (covered by 2+ sources) to become a teachable unit. The ingest agent may create these incrementally as sources accumulate.

**reference**: @handbooks/template.md

---
## Naming Conventions

- Source slugs: `kebab-case` matching source filename
- Entity pages: `TitleCase.md` (e.g. `OpenAI.md`, `SamAltman.md`)
- Concept pages: `TitleCase.md` (e.g. `ReinforcementLearning.md`, `RAG.md`)
- Source pages: `kebab-case.md`

## Index Format

```markdown
# Wiki Index

## Overview
- [Overview](overview.md) — living synthesis

## Sources
- [Source Title](sources/slug.md) — one-line summary

## Entities
- [Entity Name](entities/EntityName.md) — one-line description

## Concepts
- [Concept Name](concepts/ConceptName.md) — one-line description

## Syntheses
- [Analysis Title](syntheses/slug.md) — what question it answers
```

## Log Format

Each entry starts with `## [YYYY-MM-DD] <operation> | <title>` so it's grep-parseable:

```
grep "^## \[" wiki/log.md | tail -10
```

Operations: `ingest`, `query`, `health`, `lint`, `graph`

---