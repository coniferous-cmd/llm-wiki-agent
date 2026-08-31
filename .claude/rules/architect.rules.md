---
paths: "*"
---
## Directory Layout

```
raw/              # Immutable source documents — never modify these
wiki/             # Claude owns this layer entirely
  index.md        # Catalog of all pages — update on every ingest
  log.md          # Append-only chronological record
  overview.md     # Living synthesis across all sources
  sources/        # One summary page per source document
  entities/       # People, companies, projects, products
  concepts/       # Ideas, frameworks, methods, theories
  syntheses/      # Saved query answers
graph/            # Auto-generated graph data
tools/            # Standalone Python scripts
  health.py       # Structural checks (deterministic, no LLM calls)
  lint.py         # Content quality checks (uses LLM for semantic analysis)
  build_graph.py  # Knowledge graph generation
```
