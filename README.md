<div align="center">

```
 █████╗  ██████╗ ███████╗███╗   ██╗████████╗
██╔══██╗██╔════╝ ██╔════╝████╗  ██║╚══██╔══╝
███████║██║  ███╗█████╗  ██╔██╗ ██║   ██║
██╔══██║██║   ██║██╔══╝  ██║╚██╗██║   ██║
██║  ██║╚██████╔╝███████╗██║ ╚████║   ██║
╚═╝  ╚═╝ ╚═════╝ ╚══════╝╚═╝  ╚═══╝   ╚═╝

███████╗██╗  ██╗██╗██╗     ██╗     ███████╗
██╔════╝██║ ██╔╝██║██║     ██║     ██╔════╝
███████╗█████╔╝ ██║██║     ██║     ███████╗
╚════██║██╔═██╗ ██║██║     ██║     ╚════██║
███████║██║  ██╗██║███████╗███████╗███████║
╚══════╝╚═╝  ╚═╝╚═╝╚══════╝╚══════╝╚══════╝
```

**Procedural knowledge for AI agents. One command to install.**

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Skills](https://img.shields.io/badge/skills-3-blue.svg)](./#available-skills)
[![Version](https://img.shields.io/badge/version-1.1-blue.svg)](./component/CHANGELOG.md)

Works with **Claude Code** · **OpenCode** · **Windsurf** · **Cursor** · **Codex** · [30+ agents](https://skills.sh)

</div>

---

## What Are Agent Skills?

Your AI coding agent is smart — but it doesn't know **your** standards. It doesn't know your token naming convention, your component structure, or that you enforce WCAG 2.1 AA on everything.

**Skills fix that.** They're reusable knowledge packs you install once. Your agent absorbs the domain expertise and operates like a senior specialist — not a generic assistant guessing at best practices.

```bash
npx skills add Maximepodgorski/agent-skills
```

> Think of it as giving your agent a brain transplant from someone who's built 500 design systems.

---

## Available Skills

### [Component](./component/) — Design System Workflow

> **Figma to production. DS-compliant. Zero hardcoded values.**

The full lifecycle of a design system component — from Figma design to shipped, documented, accessible code. The skill enforces real standards (Atomic Design, BEM, DTCG, WCAG 2.1 AA) and catches every hardcoded `#hex` or `16px` before it reaches your codebase.

#### 6 Actions

| Action | What it does | Output |
|--------|-------------|--------|
| **`spec`** | Generate a component spec from Figma or a description | Architecture spec in `ds/specs/active/` |
| **`spec-review`** | Multi-perspective spec review (4 parallel reviewers) | Consolidated review with verdicts |
| **`doc`** | Generate component documentation | `doc.md` next to your component |
| **`dev`** | Implement the component (iterative ship loop) | Production-ready code |
| **`review`** | Check compliance against 5 DS principles | Verdict: COMPLIANT / NEEDS WORK / NON-COMPLIANT |
| **`audit`** | Audit an entire directory or design system | Full audit report in `ds/audits/active/` |

#### Spec-Review: 4 Parallel Perspectives

The `spec-review` action launches 4 expert reviewers simultaneously for fast, thorough spec validation before implementation:

| Perspective | Focus | Verdict |
|-------------|-------|---------|
| **Front Engineer** | Implementability, prop types, edge cases, testability | READY / NEEDS WORK / BLOCKED |
| **DS Manager** | Naming consistency, token coverage, composition, API surface | CONSISTENT / NEEDS ALIGNMENT / INCONSISTENT |
| **Accessibility Specialist** | WCAG 2.1 AA, keyboard nav, ARIA, screen reader, contrast | ACCESSIBLE / GAPS / NON-COMPLIANT |
| **Product Designer** | Design intent fidelity, visual states, responsive, content flexibility | FAITHFUL / DRIFT / MISSING INTENT |

Consolidated verdicts: **APPROVED** · **APPROVED WITH NOTES** · **NEEDS REVISION** · **BLOCKED**

#### The Workflow

```
┌──────┐     ┌─────────────┐     ┌─────────┐     ┌─────────┐     ┌──────────┐
│ spec │────▶│ spec-review │────▶│   doc   │────▶│   dev   │────▶│  review  │
│      │     │ (4 experts)  │     │(doc it) │     │(build it)│    │(check it)│
└──────┘     └─────────────┘     └─────────┘     └─────────┘     └──────────┘

Quick path (simple component):    dev Button [figma-link]
Spec validation:                  spec-review Button
Maintenance (existing component): review src/components/Button.vue
System-wide:                      audit src/components/
```

#### What It Enforces

| Principle | Standard | What the skill checks |
|-----------|----------|-----------------------|
| **Composition** | Atomic Design (Brad Frost) | Component hierarchy, single responsibility, compound patterns |
| **Naming** | BEM | Block__Element--Modifier, consistent prop/event naming |
| **Tokens** | DTCG (W3C) | Zero hardcoded values — every color, spacing, radius mapped to tokens |
| **Props API** | Component API patterns | Max 7 props, smart defaults, strict types, slots over props |
| **Accessibility** | WCAG 2.1 AA | Semantic HTML, keyboard nav, ARIA, focus management, contrast |

#### Stack-Agnostic

Works with **React** · **Vue** · **Svelte** · **Angular** · **Web Components**. The skill detects your stack and adapts.

[View full documentation →](./component/SKILL.md)

---

### [Interface](./interface/) — Compose UI from Existing Components

> **Scan your components. Propose ASCII layouts. Spec the chosen one. Build it.**

Assembles full UI screens by composing your existing components — not by creating from scratch. The skill proposes ASCII wireframe layout options with real component mapping and coverage percentages, then specs and builds the chosen layout.

#### 5 Actions

| Action | What it does | Output |
|--------|-------------|--------|
| **`init`** | Scan project conventions and patterns | `ds/conventions.md` |
| **`spec`** | Propose ASCII layouts → spec the chosen one | Interface spec in `ds/interfaces/active/` |
| **`spec-review`** | Multi-perspective spec review (3-6 dynamic reviewers) | Verdict with findings |
| **`build`** | Implement the screen (iterative ship loop) | Built interface |
| **`review`** | Check reuse compliance and interface quality | Verdict: COMPLIANT / NEEDS WORK / NON-COMPLIANT |

#### The Compose Phase

The `spec` action proposes 2-3 structurally different layouts as ASCII wireframes, each mapped to your real components:

```
Option A — Sidebar + Form (Coverage: 92%)

┌──────┬──────────────────────────┐
│ Nav  │  PageHeader              │
│(fixed)├──────────────────────────┤
│      │  TabBar                  │
│      ├──────────────────────────┤
│      │  FormSection (scrollable)│
└──────┴──────────────────────────┘

Components: AppLayout, SideNav, PageHeader, TabBar, FormSection
Missing: PreferenceToggle → adapt existing Toggle
```

Pick an option, request variants, or mix options. Max 3 rounds + free-form fallback.

#### The Workflow

```
┌──────┐     ┌─────────────┐     ┌─────────┐     ┌──────────┐
│ spec │────▶│ spec-review │────▶│  build  │────▶│  review  │
│      │     │(3-6 experts) │     │(ship it) │    │(check it)│
└──────┘     └─────────────┘     └─────────┘     └──────────┘

Daily use:    spec "settings page" → build
Complex:      spec → spec-review → build → review
```

#### Core Guarantee

**Zero component reinvented.** If your project has a `Button`, the skill uses it. If a component is missing, it flags the gap and proposes solutions (create via /component, adapt existing, or use alternative).

#### Works with Component Skill

The interface skill **composes** screens from components. The component skill **creates** individual components. They complement each other:

```
Need a screen?  → /interface spec "dashboard"
Missing a component? → /component spec StatCard
Screen complete? → /interface build
```

[View full documentation →](./interface/SKILL.md)

---

### [Kickoff](./kickoff/) — Project Context for AI Agents

> **15-minute interview. Structured docs. CLAUDE.md that stays alive.**

Conversational onboarding that captures your project context once, structures it into `.context/` docs across 7 axes, and generates a CLAUDE.md that agents actually use. When things change, surgical updates keep it current.

#### 6 Actions

| Action | What it does | Output |
|--------|-------------|--------|
| **`init`** | Full conversational kickoff (~15-20 min) | `.context/` folder with axis docs + TODO.md |
| **`update {axis}`** | Surgical axis update (2-5 min) | Updated axis docs |
| **`todo`** | Walk through open questions to enrich context | Updated docs + trimmed TODO.md |
| **`review`** | Evidence-based drift detection | Drift report with file paths |
| **`analyze`** | 4 parallel subagents challenge your context | Findings with severity + verdict |
| **`generate`** | Produce CLAUDE.md from .context/ | Merge-safe CLAUDE.md |

#### The Interview

**Not a questionnaire.** The agent has objectives, not scripts. It adapts:

- Scans your project first (package.json, README, git log, configs)
- Pre-fills what it can: "I found Vue 3 + Nuxt. Is this accurate?"
- Challenges vague answers on Product and Engineering
- Accepts "not decided yet" on optional axes
- Writes docs after each axis (session can be interrupted)

#### 7 Axes

```
.context/
├── product/         Vision, users, features, roadmap  (always)
├── engineering/     Stack, architecture, conventions  (always)
├── decisions/       ADR-lite decision records         (always)
├── design/          Principles, tokens               (if UI)
├── business/        Model, landscape                  (if commercial)
├── marketing/       Positioning                       (if PLG)
├── team/            Roles, process                    (if >1 person)
├── TODO.md          Open questions to answer later
└── _meta.md         Axes status, dates, TBDs
```

#### Maintenance

Context goes stale. Four actions handle this:

```
kickoff todo      →  Walk through open questions to enrich context
kickoff review    →  "stack.md states Jest — actual: Vitest"  (evidence-based)
kickoff update    →  "We switched to pnpm" → docs updated in 2 min
kickoff analyze   →  4 experts challenge your docs for gaps
```

[View full documentation →](./kickoff/SKILL.md)

---

## Two Ways to Work

### From Figma (via MCP)

Provide a Figma link — the skill calls Figma MCP and extracts everything automatically: component tree, props, variants, design tokens, and a screenshot for visual reference.

```
component spec Button https://figma.com/design/abc123/...?node-id=1-2
component dev Button https://figma.com/design/abc123/...?node-id=1-2
```

```
Figma link
  │
  ▼
┌──────────────────────────┐
│  Figma MCP extracts:     │
│  - Component hierarchy   │
│  - Props & variants      │
│  - Design tokens used    │
│  - Screenshot            │
└────────────┬─────────────┘
             │
             ▼
┌──────────────────────────┐
│  Skill cross-references  │
│  with your codebase:     │
│  - Existing tokens       │
│  - Naming conventions    │
│  - Project patterns      │
└────────────┬─────────────┘
             │
             ▼
       Production code
```

Requires [Figma MCP](https://github.com/anthropics/claude-code/blob/main/docs/figma.md) configured in your agent.

### From Prompt (no Figma needed)

No Figma? No problem. Describe the component — the skill scans your project for existing tokens, conventions, and patterns, then builds from there.

```
component spec Button
component dev "A toggle switch with on/off states, supports disabled"
```

---

## Installation

Install all skills:

```bash
npx skills add Maximepodgorski/agent-skills
```

Install a single skill:

```bash
npx skills add Maximepodgorski/agent-skills --skill component
npx skills add Maximepodgorski/agent-skills --skill interface
npx skills add Maximepodgorski/agent-skills --skill kickoff
```

Global install (available across all your projects):

```bash
npx skills add Maximepodgorski/agent-skills --skill component -g
npx skills add Maximepodgorski/agent-skills --skill interface -g
npx skills add Maximepodgorski/agent-skills --skill kickoff -g
```

Target a specific agent:

```bash
npx skills add Maximepodgorski/agent-skills --skill component --agent claude-code
npx skills add Maximepodgorski/agent-skills --skill component --agent cursor
npx skills add Maximepodgorski/agent-skills --skill component --agent opencode
```

---

## Usage Examples

### Component Skill

```bash
# Spec a component from Figma
component spec Button https://figma.com/design/...

# Spec from description
component spec "Dropdown menu with search, multi-select, and keyboard nav"

# Review the spec from 4 expert perspectives
component spec-review Button

# Implement it (iterative ship loop, up to 12 iterations)
component dev Button https://figma.com/design/...

# Generate docs for an existing component
component doc src/components/Button/Button.vue

# Check DS compliance
component review src/components/Button.vue

# Audit your entire component library
component audit src/components/

# Audit against Figma source of truth
component audit src/components/ https://figma.com/design/...
```

### Interface Skill

```bash
# First time — scan project conventions
interface init

# Compose + spec a settings page (proposes ASCII wireframes)
interface spec "settings page"

# Compose from Figma design
interface spec figma.com/design/abc123/...?node-id=1-2

# Multi-perspective spec review (3-6 experts)
interface spec-review

# Build the screen (ship loop)
interface build

# Post-build compliance check
interface review
```

### Kickoff Skill

```bash
# First time — full kickoff interview
kickoff init

# Generate CLAUDE.md from context docs
kickoff generate

# Something changed — surgical update
kickoff update engineering
kickoff update product

# Periodic health check
kickoff review

# Strategic challenge (4 parallel subagents)
kickoff analyze
```

---

## How It Works Under the Hood

### Component Skill

Built on **5 principles** x **6 actions** x **9 templates**.

```
component/
├── SKILL.md                          ← Entry point, action router
├── CHANGELOG.md                      ← Release notes
└── references/
    ├── actions/                      ← What the skill does
    │   ├── spec.md                   ← Spec generation logic
    │   ├── spec-review.md            ← Multi-perspective review
    │   ├── doc.md                    ← Documentation generation
    │   ├── dev.md                    ← Ship loop implementation
    │   ├── review.md                 ← Compliance checking
    │   └── audit.md                  ← System-wide audit
    ├── principles/                   ← What the skill knows
    │   ├── composition.md            ← Atomic Design rules
    │   ├── naming.md                 ← BEM conventions
    │   ├── tokens.md                 ← DTCG token standard
    │   ├── props-api.md              ← API design patterns
    │   └── accessibility.md          ← WCAG 2.1 AA checklist
    └── templates/                    ← What the skill outputs
        ├── spec.md                   ← Component spec template
        ├── doc.md                    ← Documentation template
        ├── audit-report.md           ← Audit report template
        └── *-output.md              ← Chat message formats
```

### Interface Skill

Built on **4 principles** x **5 actions** x **7 templates**.

```
interface/
├── SKILL.md                          ← Entry point, action router
└── references/
    ├── actions/                      ← What the skill does
    │   ├── init.md                   ← Project onboarding
    │   ├── spec.md                   ← Compose + spec (core action)
    │   ├── spec-review.md            ← Multi-perspective review
    │   ├── build.md                  ← Ship loop implementation
    │   └── review.md                 ← Post-build compliance
    ├── principles/                   ← What the skill knows
    │   ├── layout-patterns.md        ← SaaS/Mobile pattern catalog
    │   ├── composition.md            ← Screen assembly rules
    │   ├── page-states.md            ← Loading/empty/error/success
    │   └── responsive.md             ← Breakpoint strategy
    └── templates/                    ← What the skill outputs
        ├── conventions.md            ← Project DNA template
        ├── spec.md                   ← Interface spec template
        └── outputs/*-output.md       ← Chat message formats
```

### Kickoff Skill

Built on **1 principle** x **5 actions** x **16 templates**.

```
kickoff/
├── SKILL.md                          ← Entry point, action router
└── references/
    ├── actions/                      ← What the skill does
    │   ├── init.md                   ← Full kickoff interview
    │   ├── update.md                 ← Surgical axis updates
    │   ├── analyze.md                ← 4-perspective challenge
    │   ├── review.md                 ← Evidence-based drift detection
    │   └── generate.md              ← CLAUDE.md production
    ├── principles/                   ← What the skill knows
    │   └── interview.md             ← Conversation conduct rules
    └── templates/                    ← What the skill outputs
        ├── axes/                     ← 7 axis interview templates
        ├── context-file.md           ← .context/ file schema
        ├── claude-md.md              ← CLAUDE.md template
        ├── meta.md                   ← _meta.md template
        ├── decision-record.md        ← ADR-lite template
        └── outputs/*-output.md       ← Chat message formats
```

Everything is plain Markdown. Fork it, edit any file, make it yours.

---

## Contributing

Fork it, adapt it, break it, improve it. PRs and issues welcome.

---

## License

MIT — free to use, modify, and distribute.
