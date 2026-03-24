# Context File Schema

> **Agent:** Follow this schema when writing ANY file to `.context/`. This ensures consistent structure that `generate` can reliably read. Output docs must be RICH — detailed enough for an agent to act on without asking the user.

## Schema

Every `.context/` file (except `_meta.md` and decision records) follows this format:

```markdown
---
axis: {product | engineering | design | business | marketing | team}
doc: {vision | users | features | roadmap | stack | architecture | conventions | principles | tokens | model | landscape | positioning | team}
updated: {YYYY-MM-DD}
confidence: {high | medium | low}
---

# {Doc Title}

## {Section 1}

- {Bullet point — factual, specific, actionable}
- {Bullet point with CONCRETE details: versions, names, numbers}
- [TBD — {reason or context for why this is undecided}]

## {Section 2}

- {Bullet point}

## Open Questions

- {Question the user should answer later to improve context}
- {Question with context on WHY answering it matters for agents}
```

## Rules

1. **YAML frontmatter is mandatory** — axis, doc name, date, confidence level
2. **Bullets, not prose** — every piece of information is a bullet point
3. **No line limit** — write as much actionable context as needed. More detail = better agent output. The only constraint is relevance: every bullet must pass the "Can an agent act on this?" test. Don't pad with filler, but never truncate useful information to save space
4. **TBD markers** use the format: `[TBD — {context}]`
5. **No empty sections** — omit a section entirely rather than leaving it blank
6. **Open Questions section is mandatory** — every doc must end with 2-5 open questions that would improve context if answered. These questions should be specific, not generic ("What's the P95 latency target for the API?" not "Any performance concerns?")
7. **Confidence levels:**
   - `high` — user confirmed explicitly ("yes, we use Postgres")
   - `medium` — inferred from bootstrap scan AND user confirmed, OR user mentioned informally without deep detail ("we also kinda use Redis for caching")
   - `low` — inferred from bootstrap scan, NOT yet confirmed by user
8. **Quality bar: "Can an agent act on this?"** — every bullet must be specific enough for an agent to generate correct code or make correct decisions. Not "Uses React" but "React 18.2 with Next.js 14 App Router, TypeScript strict mode, Zustand for state"

### Content priority (what matters most)

```
ALWAYS INCLUDE:  Facts the agent needs for every code task (commands, names, versions, patterns)
                 Pain scores, severity indicators, exit criteria
                 Open Questions (guide future updates)
                 Concrete examples, real user scenarios
                 Dependencies, constraints, known risks

SKIP ONLY IF:    Redundant — already captured in another axis doc
                 Not actionable — nice-to-know trivia with no impact on agent behavior
```

## Example: product/vision.md

```markdown
---
axis: product
doc: vision
updated: 2026-03-24
confidence: high
---

# Vision

## Problem

- Tech teams (engineering + product) spend 30%+ of their time on task management instead of building
- Information is scattered across Slack, GitHub PRs, meeting notes, and Linear — context is lost between tools
- Current workaround: a PM or tech lead manually reads Slack threads, PR descriptions, and standup notes, then creates/updates tasks by hand. Takes 1-2 hours/day for a 10-person team
- The cognitive load of "what should I work on next?" creates decision fatigue and context-switching costs

## Solution

- AI agent that eliminates the cognitive burden of organizing work for tech teams
- Reads team signals (Slack conversations, PR activity, standup transcripts) and produces structured, prioritized tasks automatically
- Works within existing tools — no new workflow to adopt, no new UI to learn

## Differentiation

- Not a "better PM tool" — eliminates PM work entirely (0→1, not incremental improvement on Linear/Jira)
- AI generates tasks from existing communication patterns, not from manual input — the team never fills a form
- Understands code context: links tasks to PRs, detects blockers from CI failures, knows who owns what area of code
- Competitors (Motion, Reclaim) optimize scheduling; Lyse eliminates task creation and triage altogether

## North Star

- % of tasks created by AI vs manually created — target: >70% within 3 months of adoption
- Secondary: time saved per team member per week on task management (target: 5h → <1h)

## Stage

- Early users — private beta with 5 teams (3 startups, 2 scale-ups), 47 active users
- Activation rate: 62% (users who connect Slack AND see first auto-generated task within 24h)

## Open Questions

- What's the activation threshold? (e.g., "3 auto-tasks accepted in first week" = activated?)
- At what team size does the agent become less effective? (scaling limits for context understanding)
- How do we handle teams that use BOTH Slack and Discord? Priority or both?
- What's the acceptable false positive rate for auto-generated tasks before users lose trust?
```

## Example: product/users.md

```markdown
---
axis: product
doc: users
updated: 2026-03-24
confidence: high
---

# Users

## Persona 1: Tech Lead — "Alex"

- **Job title:** Tech Lead / Senior Engineer at a startup (10-30 people)
- **Daily workflow:** Code review → check Slack for blockers → update Linear → standup → code → context-switch to help juniors → more Slack → update tickets at end of day
- **Pain point (severity: 9/10):** Spends 1.5-2h/day on task management instead of coding. Feels like a "ticket secretary" — tracking what everyone is doing, not building
- **Current workaround:** Manually reads all Slack channels, creates Linear tickets from conversations, tags team members. Some info is lost because it's in DMs or threads they missed
- **JTBD:** "I want to focus on architecture and code review, not on tracking who's doing what. The organizational work should happen automatically."
- **Technical level:** High — full-stack, comfortable with APIs, CI/CD, infrastructure
- **Tools:** VS Code, GitHub, Linear, Slack, Notion, Figma (for reviews)
- **Switching trigger:** Would adopt if they could reclaim >1h/day AND trust the AI output within the first week

## Persona 2: Engineering Manager — "Sarah"

- **Job title:** Engineering Manager at a scale-up (30-100 people)
- **Daily workflow:** 1-on-1s → sprint planning → stakeholder updates → metrics review → firefighting → end-of-sprint reporting
- **Pain point (severity: 7/10):** Lacks real-time visibility into what's actually happening. Sprint velocity is a lagging indicator. By the time she sees a blocker in Linear, it's 2 days old
- **Current workaround:** Asks tech leads in Slack "what's the status of X?" — creates interruption chains. Reads PR activity manually to gauge progress
- **JTBD:** "I need a live pulse of my team's work without interrupting anyone. Not a dashboard I have to interpret — actual insights like 'Feature X is blocked because CI is failing on the auth module.'"
- **Technical level:** Medium — can read code, doesn't write production code daily
- **Tools:** Linear, Slack, Google Docs, Loom, GitHub (read-only)
- **Switching trigger:** Would adopt if she could eliminate the "status update" meeting entirely

## ICP (Ideal Customer Profile)

- Tech-first startup or scale-up, 8-30 engineers
- Already uses Slack + GitHub + a task tracker (Linear, Jira, Asana)
- Has a tech lead or EM who spends >1h/day on task management
- Engineering-led culture (decisions made by builders, not managers)

## Open Questions

- Is there a Persona 3? (e.g., the IC developer who just wants to know "what should I work on next?")
- How does the buyer persona differ from the user persona? (Who signs the contract vs who uses daily?)
- What's the company size ceiling? At 100+ engineers, do teams fragment too much for one agent to cover?
- Do we need to handle non-English Slack conversations?
```

## Example: product/roadmap.md

```markdown
---
axis: product
doc: roadmap
updated: 2026-03-24
confidence: medium
---

# Roadmap

## This Week (Week 1)

- **Slack → Task pipeline v1**: Connect to Slack workspace, read public channels, extract actionable items from conversations → create draft tasks in Linear
  - Exit criteria: 3 beta teams see auto-generated task drafts within 24h of connecting Slack
  - Dependencies: Slack OAuth flow (done), Linear API integration (in progress)
  - Risk: False positive rate — need to tune extraction quality before teams lose trust

## Week 2-4 (Short-term Must-haves)

- **Task acceptance flow**: Users can accept, edit, or dismiss AI-generated tasks with one click. Feedback loop improves future task quality
  - Why first: Without acceptance data, we can't improve the model. This is the learning loop
  - Exit criteria: >50% acceptance rate on auto-generated tasks across beta teams
- **PR → Task linking**: Detect when a PR addresses a task, auto-update task status
  - Why: Eliminates "update your tickets" nagging. Status updates happen automatically
  - Exit criteria: 80% of merged PRs auto-close or update their linked task
- **Daily digest**: Morning Slack message summarizing what the agent created/updated overnight
  - Why: Builds trust by showing work done. Users see value without opening a dashboard

## Month 2-3 (Medium-term)

- **Blocker detection**: Identify stalled PRs, CI failures, and unresponsive reviewers → surface as blockers on related tasks
  - Unlock: Moves from "task creation" to "project intelligence"
- **Sprint scope suggestion**: Based on velocity + current task backlog, suggest realistic sprint scope
  - Unlock: Replaces sprint planning meeting prep
- **Multi-channel context**: Read GitHub PR comments, Notion docs (later: meeting transcripts) in addition to Slack
  - Unlock: Richer context = better task quality

## Month 3-6 (Directional Vision)

- Autonomous standup reports (replaces async standup tools)
- Predictive delivery estimates based on historical team velocity
- Cross-team dependency detection (for 20+ person orgs)
- [TBD — customer interviews will shape priorities beyond month 3]

## Open Questions

- Should we build our own LLM fine-tuning pipeline or rely on prompt engineering for task extraction quality?
- What's the right granularity for auto-generated tasks? (Epic-level vs individual tickets)
- When do we need a web dashboard vs staying Slack-native for everything?
- How do we handle private channels and DMs? Privacy implications for team trust?
```

## Example: engineering/stack.md

```markdown
---
axis: engineering
doc: stack
updated: 2026-03-24
confidence: high
---

# Stack

## Languages

- TypeScript 5.3 (strict mode, `noUncheckedIndexedAccess` enabled)
- SCSS for component styling (no Tailwind — custom design system)

## Frameworks

- Vue 3.4 (Composition API exclusively, `<script setup>` pattern)
- Nuxt 3.10 (SSR mode, file-based routing, auto-imports enabled)
- Nitro (server API layer — all backend logic runs here)

## Database

- Supabase (managed Postgres 15)
  - Auth: Supabase Auth (email + GitHub OAuth)
  - Storage: Supabase Storage (avatars, attachments)
  - Realtime: Supabase Realtime (task updates, notifications)
  - Client: `@supabase/supabase-js` v2 — no ORM, raw SQL via RPC for complex queries

## AI Layer

- Mastra (AI agent framework) — runs as a separate service
- Mistral (primary LLM) — task extraction and classification
- Embeddings: Mistral embed for semantic search across Slack messages

## Hosting

- Vercel (frontend — Nuxt SSR)
- Supabase Cloud (database, auth, storage)
- Railway (Mastra agent service)

## Package Manager

- npm with workspaces enabled (not pnpm/yarn)
- Lock file: `package-lock.json` (committed)

## Key Dependencies

- Nx 17.2 (monorepo build system + task orchestration)
- Vitest 1.2 (testing — unit + integration)
- VueUse (composables library)
- Zod (runtime validation on API boundaries)

## Open Questions

- Should we migrate from npm to pnpm for faster installs in CI?
- Mastra vs LangChain: re-evaluate at month 3 based on agent complexity
- Is Railway the right long-term host for the agent service? (cost at scale)
- When do we need a Redis layer for caching? (currently no caching strategy)
```

## Example: engineering/architecture.md

```markdown
---
axis: engineering
doc: architecture
updated: 2026-03-24
confidence: high
---

# Architecture

## Repo Structure (Nx Monorepo)

```
lyse-app/
├── apps/
│   ├── agent-app/        ← Nuxt 3 frontend (main product UI)
│   └── figma-plugin/     ← Figma plugin (design handoff)
├── libs/
│   ├── lyse-agent/       ← Mastra AI agent (task extraction, classification)
│   ├── database/         ← Supabase client, migrations, RPC functions
│   └── shared/           ← Shared types, utils, constants
├── nx.json
└── package.json
```

## Layers

- **Frontend (agent-app):** Nuxt 3 SSR — pages, components, composables, stores (Pinia)
- **API (agent-app/server/):** Nitro API routes — thin controllers that call services
- **Services (libs/):** Business logic lives in libs, not in components or API routes
- **Database (libs/database):** Supabase client + typed RPC functions + migrations
- **AI (libs/lyse-agent):** Mastra agent — connects to Slack, processes messages, generates tasks

## Data Flow

```
User action in UI
  → Nuxt component calls composable
  → Composable calls Nitro API route (/api/tasks/...)
  → API route calls service in libs/database
  → Service executes Supabase RPC or direct query
  → Response flows back: DB → service → API → composable → reactive UI update

AI pipeline (async):
  Slack webhook → Mastra agent → extract task candidates → Supabase insert → Realtime push → UI updates
```

## Key Patterns

- **Composables over stores:** Use `useXxx()` composables for data fetching + state. Pinia only for global app state (auth, preferences)
- **Server-first validation:** All input validation happens in Nitro API routes (Zod schemas). Frontend validation is UX-only
- **Typed end-to-end:** Supabase generates TypeScript types from DB schema → shared across all apps/libs
- **Feature folders:** Components grouped by feature (`/components/tasks/`, `/components/workspace/`), not by type

## Open Questions

- Should composables call Supabase directly or always go through Nitro API routes? (Currently mixed — need to standardize)
- How do we handle optimistic updates for task acceptance? (Currently waiting for server response)
- Is the Figma plugin still actively developed or should it be archived?
- What's the strategy for database migrations in production? (Currently manual via Supabase dashboard)
```
