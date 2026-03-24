# Kickoff

Project context kickoff for AI coding agents. Conversational interview that produces structured docs and generates your CLAUDE.md.

## The Problem

Every time you start a project with an AI agent, you re-explain your stack, your conventions, your product vision. The agent forgets between sessions. You write a CLAUDE.md by hand, it goes stale in 2 weeks.

## The Solution

A 15-20 minute conversational kickoff that captures your project context once, structures it, and keeps it alive.

```
kickoff init       →  Kickoff interview (adapts to your answers)
                   →  Generates .context/ docs
kickoff generate   →  Produces CLAUDE.md from docs
kickoff review     →  Detects drift between docs and codebase
kickoff update     →  Surgical updates to specific axes
kickoff analyze    →  Challenges your context for gaps
```

## Quick Start

```bash
# Install the skill
npx skills add Maximepodgorski/agent-skills --skill kickoff

# Run the kickoff
kickoff init

# Generate CLAUDE.md
kickoff generate
```

## What It Produces

```
.context/
├── product/            Vision, users, features
├── engineering/        Stack, architecture, conventions
├── decisions/          ADR-lite decision records
├── design/             Principles, tokens (if UI)
├── business/           Model, landscape (if commercial)
├── marketing/          Positioning (if PLG)
├── team/               Roles, process (if team)
└── _meta.md            Axes status, dates, TBDs
```

From these docs → condensed CLAUDE.md (< 200 lines) that agents actually use.

**How it fits together:** `.context/` is the source of truth (structured docs). CLAUDE.md is the compiled output agents consume. `update` modifies the source, `generate` compiles to the output.

## How the Interview Works

**Not a questionnaire.** The agent:

1. **Scans your project first** — reads package.json, README, git log, file structure
2. **Pre-fills what it can** — "I found Vue 3 + Nuxt. Is this accurate?"
3. **Asks open questions** — "Tell me about your product. What problem does it solve?"
4. **Adapts to your answers** — challenges vague responses on Product and Engineering, accepts "not decided yet" on optional axes
5. **Writes docs immediately** — after each axis, not at the end (session can be interrupted)

## Axes

| Axis | Required | What the agent learns |
|------|----------|----------------------|
| Product | Always | Problem, users, features, north star |
| Engineering | Always | Stack, architecture, conventions, commands |
| Decisions | Always | Why key choices were made (captured inline) |
| Design | If UI | Visual identity, tokens, responsive strategy |
| Business | If commercial | Revenue model, competitors, moat |
| Marketing | If PLG/content | Positioning, channels, tone |
| Team | If >1 person | Roles, review process, tools |

## Maintenance

Context goes stale. The skill handles this:

- **`kickoff review`** — scans codebase, compares with docs, reports drift with evidence
- **`kickoff update {axis}`** — fast, surgical updates (2-5 min, not a re-interview)
- **`kickoff analyze`** — 4 parallel subagents challenge your context for gaps and contradictions
- **`kickoff generate`** — regenerates CLAUDE.md (merges with your custom sections, never overwrites)

## Design Principles

- **Bootstrap first** — read artifacts before asking questions
- **Objectives, not scripts** — the agent knows WHAT to learn, decides HOW to ask
- **Checkpoint per axis** — session can be interrupted and resumed
- **TBD is valid** — "not decided yet" is captured explicitly, never fabricated
- **Merge, never overwrite** — CLAUDE.md generation preserves your custom sections
- **Intention over description** — captures WHY decisions were made, not just WHAT exists

## License

MIT
