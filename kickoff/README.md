# Kickoff — Project Context for AI Agents

**15-minute interview. Structured docs. CLAUDE.md that stays alive.**

Every time you start a project with an AI agent, you re-explain your stack, your conventions, your product vision. The agent forgets between sessions. You write a CLAUDE.md by hand, it goes stale in 2 weeks.

**Kickoff fixes that.** A conversational interview captures your project context once, structures it into `.context/` docs, and generates a CLAUDE.md that agents actually use. When things change, surgical updates keep it current.

**Core guarantee: the agent never invents information.**

---

## Quick Start

```bash
# First time — run the kickoff interview (~15-20 min)
kickoff init

# Generate CLAUDE.md from context docs
kickoff generate
```

That's the onboarding. After that, maintenance is fast:

```bash
kickoff update engineering     # Surgical update (2-5 min)
kickoff todo                   # Answer open questions (5-10 min)
kickoff review                 # Detect drift
kickoff analyze                # Challenge your context
```

---

## What It Does

1. **Scans** your project for existing artifacts (package.json, README, git log, configs)
2. **Pre-fills** what it can — "I found Vue 3 + Nuxt. Is this accurate?"
3. **Interviews** you axis by axis — adapts to your answers, challenges vague ones
4. **Writes docs immediately** — after each axis, not at the end (session can be interrupted)
5. **Generates** a CLAUDE.md from the structured docs (no line limit — all actionable context included)
6. **Tracks open questions** in a centralized TODO.md — answer them later to enrich context
7. **Maintains** docs over time with review, update, todo, and analyze actions

```
kickoff init
     │
     ▼
  Bootstrap scan (package.json, README, git log, configs)
     │
     ▼
  Adaptive interview (Product → Engineering → Design → ...)
     │
     ▼  (checkpoint after each axis)
  .context/ docs written
     │
     │
     ▼  .context/TODO.md written (open questions)
     │
     ▼  kickoff generate
  CLAUDE.md produced (merge-safe)
```

---

## Actions

| Action | When | What | Duration |
|--------|------|------|----------|
| **`init`** | First time | Full conversational kickoff | 15-20 min |
| **`update {axis}`** | Something changed | Surgical axis update | 2-5 min |
| **`todo`** | Enrich context | Walk through open questions from TODO.md | 5-10 min |
| **`review`** | Periodic health check | Drift detection with evidence | ~30 sec |
| **`analyze`** | Strategic challenge | 4 parallel subagents challenge your context | 2-3 min |
| **`generate`** | After init or update | Produce CLAUDE.md from .context/ | ~10 sec |

---

## The Interview

**Not a questionnaire.** The agent has objectives (what to know), not scripts (what to ask). It adapts to your answers:

- **Product & Engineering** — deep mode. Challenges vague answers. "Can you describe a concrete moment when someone has this problem?"
- **Design, Business** — standard mode. Accepts "not decided yet" gracefully.
- **Marketing, Team** — light mode. Quick pass, moves on fast.

Decisions are captured inline: when you say "we chose Postgres because...", the agent records it as an ADR-lite decision record.

---

## What It Produces

```
.context/
├── product/
│   ├── vision.md              Problem, solution, differentiation, north star
│   ├── users.md               Personas with pain scores and JTBD
│   ├── features.md            Shipped + planned features
│   └── roadmap.md             Week-by-week plan + directional vision
├── engineering/
│   ├── stack.md               Languages, frameworks, DB, hosting
│   ├── architecture.md        Repo structure, layers, data flow
│   └── conventions.md         Naming, lint, commits, commands
├── decisions/
│   ├── 001-postgres-over-mongodb.md
│   └── 002-monorepo-with-nx.md
├── design/                    (if UI)
│   ├── principles.md          Visual identity, responsive strategy
│   └── tokens.md              Colors, spacing, typography
├── business/                  (if commercial)
│   ├── model.md               Revenue, pricing, unit economics
│   └── landscape.md           Competitors, moat, positioning
├── marketing/                 (if PLG)
│   └── positioning.md         Channels, tone, messaging
├── team/                      (if >1 person)
│   └── team.md                Roles, process, review workflow
├── TODO.md                    Open questions to answer later
└── _meta.md                   Axes status, dates, TBDs
```

**How it fits together:** `.context/` is the source of truth (structured docs). CLAUDE.md is the compiled output agents consume. `update` modifies the source, `generate` compiles to the output.

---

## Axes

| Axis | Required | What the agent learns | Depth |
|------|----------|----------------------|-------|
| **Product** | Always | Problem, users, features, north star | Deep |
| **Engineering** | Always | Stack, architecture, conventions, commands | Deep |
| **Decisions** | Always | Why key choices were made (captured inline) | Medium |
| **Design** | If UI | Visual identity, tokens, responsive strategy | Standard |
| **Business** | If commercial | Revenue model, competitors, moat | Standard |
| **Marketing** | If PLG/content | Positioning, channels, tone | Light |
| **Team** | If >1 person | Roles, review process, tools | Light |

---

## Maintenance

Context goes stale. The skill handles this:

### Review — Drift Detection

```bash
kickoff review
```

Scans your codebase and compares against `.context/` docs. Every finding includes evidence:

```
[DRIFT:CRITICAL] stack.md states "Jest" — actual: Vitest
                 Evidence: test files import from 'vitest'
                 Suggestion: Run 'kickoff update engineering'

[OK] stack.md — stated framework matches package.json — consistent
```

Severity tiers: CRITICAL (wrong framework/DB) → HIGH (wrong version/command) → MEDIUM (naming drift).

### Update — Surgical Changes

```bash
kickoff update engineering    # "We switched to pnpm and added Tailwind"
kickoff update product        # "We launched feature X"
```

Shows current state, asks what changed, writes updated docs. Cross-axis awareness: if an engineering change implies a product change, the skill flags it.

### Analyze — Strategic Challenge

```bash
kickoff analyze
```

Launches 4 parallel subagents that challenge your context:

| Perspective | Focus |
|-------------|-------|
| **Devil's Advocate** | Assumptions that could be wrong |
| **Technical Validator** | Stack consistency, architectural gaps |
| **Product Strategist** | Problem-market coherence |
| **Consistency Checker** | Contradictions between axes |

Verdict: COHERENT / COHERENT WITH NOTES / REVIEW RECOMMENDED / NEEDS ATTENTION.

### Generate — CLAUDE.md Production

```bash
kickoff generate
```

Reads all `.context/` docs and produces a CLAUDE.md with all actionable context. Merge-safe: preserves your custom sections using `<!-- context:generated -->` markers.

---

## Design Principles

- **Bootstrap first** — read artifacts before asking questions
- **Objectives, not scripts** — the agent knows WHAT to learn, decides HOW to ask
- **Checkpoint per axis** — session can be interrupted and resumed
- **TBD is valid** — "not decided yet" is captured explicitly, never fabricated
- **Merge, never overwrite** — CLAUDE.md generation preserves your custom sections
- **Intention over description** — captures WHY decisions were made, not just WHAT exists

---

## How It Works Under the Hood

```
kickoff/
├── SKILL.md                          ← Entry point, action router
├── README.md                         ← You're reading this
└── references/
    ├── actions/                      ← What the skill does
    │   ├── init.md                   ← Full kickoff interview
    │   ├── update.md                 ← Surgical axis updates
    │   ├── todo.md                   ← Open questions walkthrough
    │   ├── analyze.md                ← 4-perspective challenge
    │   ├── review.md                 ← Evidence-based drift detection
    │   └── generate.md              ← CLAUDE.md production
    ├── principles/                   ← What the skill knows
    │   └── interview.md             ← Conversation conduct rules
    └── templates/                    ← What the skill outputs
        ├── axes/                     ← 7 axis interview templates
        │   ├── product.md
        │   ├── engineering.md
        │   ├── decisions.md
        │   ├── design.md
        │   ├── business.md
        │   ├── marketing.md
        │   └── team.md
        ├── context-file.md           ← .context/ file schema
        ├── claude-md.md              ← CLAUDE.md template
        ├── meta.md                   ← _meta.md template
        ├── decision-record.md        ← ADR-lite template
        └── outputs/                  ← Chat message formats
            ├── init-output.md
            ├── update-output.md
            ├── analyze-output.md
            ├── review-output.md
            └── generate-output.md
```

**1 principle** x **6 actions** x **16 templates**. Everything is plain Markdown.

[Full documentation →](./SKILL.md)
