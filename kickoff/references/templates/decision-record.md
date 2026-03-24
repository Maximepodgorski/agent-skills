# Decision Record Template

> **Agent:** Follow this format for each decision captured in `.context/decisions/`. ADR-lite — max 10 lines per record.

## Template

```markdown
---
id: {NNN}
title: {Short decision title}
date: {YYYY-MM-DD}
status: {accepted | superseded | deprecated}
---

# {NNN}: {Decision Title}

## Context

- {Why this decision was needed — 1-3 bullets}

## Decision

- {What was chosen — 1 bullet, clear and specific}

## Alternatives Considered

- {Alternative 1} — rejected because {reason}
- {Alternative 2} — rejected because {reason}

## Consequences

- {What this enables or constrains — 1-3 bullets}
```

## Example

```markdown
---
id: 001
title: Postgres over MongoDB
date: 2026-03-23
status: accepted
---

# 001: Postgres over MongoDB

## Context

- Data is highly relational (users, teams, projects, tasks with complex joins)
- Need strong consistency for financial data (usage tracking, billing)

## Decision

- Use Postgres via Supabase (managed, includes Auth + Storage + Realtime)

## Alternatives Considered

- MongoDB — rejected because relational data needs complex aggregation pipelines
- PlanetScale (MySQL) — rejected because Supabase bundles more (auth, storage)

## Consequences

- Locked into SQL and relational modeling
- Supabase provides auth, storage, and realtime out of the box
- Migration to another Postgres provider is straightforward (standard SQL)
```

## Rules

1. Max 10 lines of content (excluding frontmatter)
2. Numbered sequentially (001, 002, 003...)
3. Slug is kebab-case of the title
4. Always include at least 1 alternative — even "do nothing" counts
5. `superseded` status: link to the new decision that replaces this one
6. Capture decisions as they surface — don't force a separate interview
