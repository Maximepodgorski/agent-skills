# Meta File Template

> **Agent:** Write this file as `.context/_meta.md` during `init` and update it after `update`, `review`, or `generate` actions.

## Template

```markdown
---
project: {project name}
created: {YYYY-MM-DD}
last_generated: {YYYY-MM-DD or "never"}
skill_version: "1.0"
---

# Context Meta

## Active Axes

| Axis | Status | Last Updated | Confidence | Docs |
|------|--------|-------------|------------|------|
| product | {complete / partial / TBD} | {YYYY-MM-DD} | {high / medium / low} | vision.md, users.md, features.md |
| engineering | {complete / partial / TBD} | {YYYY-MM-DD} | {high / medium / low} | stack.md, architecture.md, conventions.md |
| decisions | {N records} | {YYYY-MM-DD} | {high / medium / low} | 001-*.md, ... |
| design | {complete / partial / skipped} | {YYYY-MM-DD} | {high / medium / low} | principles.md, tokens.md |
| business | {complete / partial / skipped} | {YYYY-MM-DD} | {high / medium / low} | model.md, landscape.md |
| marketing | {complete / partial / skipped} | {YYYY-MM-DD} | {high / medium / low} | positioning.md |
| team | {complete / partial / skipped} | {YYYY-MM-DD} | {high / medium / low} | team.md |

## Bootstrap Sources

- {source 1}: {what was detected} ({date})
- {source 2}: {what was detected} ({date})

## Open TBDs

- [{axis}] {what's undecided — context}
- [{axis}] {what's undecided — context}
```

## Rules

1. Update the relevant axis row after any `update` action
2. Update `last_generated` after any `generate` action
3. Track TBDs — these are the "known unknowns" the agent should treat as uncertain
4. `skipped` means the axis was not applicable (not interviewed, not TBD)
5. **`_meta.md` is the canonical source of truth for dates.** When `review` or `generate` need to compare dates (e.g., "was this axis updated after last generation?"), they MUST read dates from `_meta.md`, not from individual file frontmatter. Individual files have their own `updated:` field for reference, but `_meta.md` is authoritative.
6. **Status values:** `complete` (all objectives met), `partial` (some objectives met, some TBD), `skipped` (axis not applicable), `{N} records` (for decisions axis only)
