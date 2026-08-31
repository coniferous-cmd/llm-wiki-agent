---
paths:
  - "*"
---
## Ingest Workflow

Triggered by:

```sh
ingest <file> or /wiki-ingest
```

**Supported formats:** Markdown (`.md`) ingested directly. Non-markdown files (`.pdf`, `.docx`, `.pptx`, `.xlsx`, `.html`, `.txt`, `.csv`, `.json`, `.xml`, `.rst`, `.rtf`, `.epub`, `.ipynb`, `.yaml`, `.yml`, `.tsv`, `.wav`, `.mp3`) auto-converted to markdown via [markitdown](https://github.com/microsoft/markitdown) before ingestion. Use `--no-convert` to skip auto-conversion.

Steps (in order):

1. Read the source document fully using the Read tool (auto-convert if non-markdown)
2. Read `wiki/index.md` and `wiki/overview.md` for current wiki context
3. Write `wiki/sources/<slug>.md` — use the source page format below
4. Update `wiki/index.md` — add entry under Sources section
5. Update `wiki/overview.md` — revise synthesis if warranted
6. Update/create entity pages for key people, companies, projects mentioned
7. Update/create concept pages for key ideas and frameworks discussed
   - **If the source contains pedagogical material** (exercises, examples, common errors, diagnostic questions, hint ladders, misconceptions, practice tasks): create as **Teaching Concept** directly
   - **Else if user says** `"teachify <concept>"` or `"把 X 做成教学章节"`: create as **Teaching Concept** directly
   - **Else**: create or update as **Standard Concept** (lightweight summary)
   - **If concept already has 2+ sources** and the new source adds pedagogical material: prompt user — `"Concept X now has N sources. Upgrade to Teaching Concept?"`
8. Flag any contradictions with existing wiki content
9. Append to `wiki/log.md`: `## [YYYY-MM-DD] ingest | <Title>`
10. **Post-ingest validation** — check for broken `[[wikilinks]]`, verify all new pages are in `index.md`, print a change summary

---
## Teachify Workflow (Standard → Teaching Concept)

Triggered by:

```text
teachify <concept> or 把 X 做成教学章节
```

Converts a **Standard Concept** into a **Teaching Concept** by mining pedagogical material from its linked sources.

Steps:
1. Read the target `wiki/concepts/<ConceptName>.md`
2. Read all `wiki/sources/*.md` listed in its `sources:` frontmatter
3. Extract and synthesize:
   - **Core Thesis** — the single sentence that captures the essence
   - **Problem Definition** — what problem this concept solves
   - **Terminology** — defined terms across sources
   - **Mental Model** — structural understanding from source explanations
   - **Common Errors** — error patterns mentioned in sources
   - **Misconceptions** — contradictions or confusions found across sources
   - **Practice Tasks** — exercises, code samples, or scenarios from sources
4. Write the expanded `wiki/concepts/<ConceptName>.md` using the **Teaching Concept** format
5. Mark incomplete sections explicitly (e.g., `## Hint Escalation Ladder (TODO — not found in sources)`)
6. Update `wiki/index.md` — add `curriculum` tag if not present
7. Append to `wiki/log.md`: `## [YYYY-MM-DD] teachify | <ConceptName>`

**Note**: A Teaching Concept does not need to be complete in one pass. It can mature incrementally as more sources are ingested. Incomplete sections are fine — they signal where the knowledge base is thin.

---
### Source Page Format

```markdown
---
title: "Source Title"
type: source
tags: []
date: YYYY-MM-DD
source_file: raw/...
---

## Summary
2–4 sentence summary.

## Key Claims
- Claim 1
- Claim 2

## Key Quotes
> "Quote here" — context

## Connections
- [[EntityName]] — how they relate
- [[ConceptName]] — how it connects

## Contradictions
- Contradicts [[OtherPage]] on: ...
```

---
### Domain-Specific Templates

If the source falls into a specific domain (e.g., personal diary, meeting notes), the agent should use a specialized template instead of the default generic one above:

#### Diary / Journal Template

```markdown
---
title: "YYYY-MM-DD Diary"
type: source
tags: [diary]
date: YYYY-MM-DD
---
## Event Summary
...
## Key Decisions
...
## Energy & Mood
...
## Connections
...
## Shifts & Contradictions
...
```

#### Meeting Notes Template

```markdown
---
title: "Meeting Title"
type: source
tags: [meeting]
date: YYYY-MM-DD
---
## Goal
...
## Key Discussions
...
## Decisions Made
...
## Action Items
...
```

---

## Query Workflow

Triggered by:

```text
query: <question> or /wiki-query
```

Steps:
1. Read `wiki/index.md` to identify relevant pages
2. Read those pages with the Read tool
3. Synthesize an answer with inline citations as `[[PageName]]` wikilinks
4. Ask the user if they want the answer filed as `wiki/syntheses/<slug>.md`

---

## Lint Workflow

Triggered by:

```text
lint the wiki or /wiki-lint
```

Use Grep and Read tools to check for:
- **Orphan pages** — wiki pages with no inbound `[[links]]` from other pages
- **Broken links** — `[[WikiLinks]]` pointing to pages that don't exist
- **Contradictions** — claims that conflict across pages
- **Stale summaries** — pages not updated after newer sources
- **Missing entity pages** — entities mentioned in 3+ pages but lacking their own page
- **Data gaps** — questions the wiki can't answer; suggest new sources

Output a lint report and ask if the user wants it saved to `wiki/lint-report.md`.

---

## Health Workflow

Triggered by:

```text
health or /wiki-health
```

Run: `python tools/health.py` (or `python tools/health.py --json` for machine-readable output)

Fast structural integrity checks — **zero LLM calls**, safe to run every session:
- **Empty / stub files** — pages with no content beyond frontmatter (rate-limit damage)
- **Index sync** — `wiki/index.md` entries vs actual files on disk
- **Log coverage** — source pages missing a corresponding `ingest` entry in `wiki/log.md`

Output a health report. Use `--save` to write to `wiki/health-report.md`.

### Health vs Lint Boundary

| Dimension | `health` | `lint` |
|---|---|---|
| **Scope** | Structural integrity | Content quality |
| **LLM calls** | Zero | Yes (semantic analysis) |
| **Cost** | Free | Tokens |
| **Frequency** | Every session, before other work | Every 10-15 ingests |
| **Checks** | Empty files, index sync, log sync | Orphans, broken links, contradictions, gaps |
| **Tool** | `tools/health.py` | `tools/lint.py` |
| **Run order** | First (pre-flight) | After health passes |

> Run `health` first — linting an empty file wastes tokens.

---

## Graph Workflow

Triggered by:

```text
build the knowledge graph or /wiki-graph
```

When the user asks to build the graph, run `tools/build_graph.py` which:
- Pass 1: Parses all `[[wikilinks]]` → deterministic `EXTRACTED` edges
- Pass 2: Infers implicit relationships → `INFERRED` edges with confidence scores
- Runs Louvain community detection
- Outputs `graph/graph.json` + `graph/graph.html`

If the user doesn't have Python/dependencies set up, instead generate the graph data manually:
1. Use Grep to find all `[[wikilinks]]` across wiki pages
2. Build a node/edge list
3. Write `graph/graph.json` directly
4. Write `graph/graph.html` using the vis.js template

---