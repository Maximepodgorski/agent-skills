# Context File Schema

> **Agent:** Follow this schema when writing ANY file to `.context/`. This ensures consistent structure that `generate` can reliably read.

## Schema

Every `.context/` file (except `_meta.md` and decision records) follows this format:

```markdown
---
axis: {product | engineering | design | business | marketing | team}
doc: {vision | users | features | stack | architecture | conventions | principles | tokens | model | landscape | positioning | team}
updated: {YYYY-MM-DD}
confidence: {high | medium | low}
---

# {Doc Title}

## {Section 1}

- {Bullet point — factual, concise}
- {Bullet point}
- [TBD — {reason or context for why this is undecided}]

## {Section 2}

- {Bullet point}
```

## Rules

1. **YAML frontmatter is mandatory** — axis, doc name, date, confidence level
2. **Bullets, not prose** — every piece of information is a bullet point
3. **Max 40 lines per file** (excluding frontmatter) — condense aggressively
4. **TBD markers** use the format: `[TBD — {context}]`
5. **No empty sections** — omit a section entirely rather than leaving it blank
6. **Confidence levels:**
   - `high` — user confirmed explicitly ("yes, we use Postgres")
   - `medium` — inferred from bootstrap scan AND user confirmed, OR user mentioned informally without deep detail ("we also kinda use Redis for caching")
   - `low` — inferred from bootstrap scan, NOT yet confirmed by user

### 40-line cut priority

When a doc exceeds 40 lines, cut in this order:

```
CUT FIRST:  Examples and explanations (keep the fact, drop the context)
            Planned/future items (keep current state)
            Low-confidence bullets (keep high/medium)
            [TBD] items (collapse multiple TBDs into one "[TBD — multiple items pending]")
CUT LAST:   Facts the agent needs for every code task (commands, names, versions, patterns)
```

## Example: engineering/stack.md

```markdown
---
axis: engineering
doc: stack
updated: 2026-03-23
confidence: high
---

# Stack

## Languages

- TypeScript 5.3 (strict mode)
- SCSS for styling

## Frameworks

- Vue 3.4 (Composition API, `<script setup>`)
- Nuxt 3.10 (SSR, file-based routing)
- Nitro (server API layer)

## Database

- Supabase (Postgres 15 + Auth + Storage + Realtime)

## Hosting

- Vercel (frontend)
- Supabase Cloud (backend/DB)

## Package Manager

- npm (workspaces enabled)

## Key Dependencies

- Mastra (AI agent framework)
- Vitest (testing)
- Nx (monorepo build system)
```

## Example: product/vision.md

```markdown
---
axis: product
doc: vision
updated: 2026-03-23
confidence: high
---

# Vision

## Problem

- Tech teams spend 30%+ of their time on task management instead of building
- Information is scattered across Slack, docs, and meetings — context is lost

## Solution

- AI agent that eliminates the cognitive burden of organizing work
- Reads team signals (Slack, PRs, standups) and produces structured tasks automatically

## Differentiation

- Not a "better PM tool" — eliminates PM work entirely
- AI generates tasks from existing communication, not from manual input
- Works within existing tools (Slack, GitHub) — no new workflow to adopt

## North Star

- % of tasks created by AI vs manually (target: >70%)

## Stage

- Early users (private beta with 5 teams)
```
