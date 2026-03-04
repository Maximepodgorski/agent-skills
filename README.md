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
[![Skills](https://img.shields.io/badge/skills-1-blue.svg)](./#available-skills)

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

#### 5 Actions

| Action | What it does | Output |
|--------|-------------|--------|
| **`structure`** | Generate a component spec from Figma or a description | Architecture spec in `ds/specs/active/` |
| **`doc`** | Generate component documentation | `doc.md` next to your component |
| **`dev`** | Implement the component (iterative ship loop) | Production-ready code |
| **`review`** | Check compliance against 5 DS principles | Verdict: COMPLIANT / NEEDS WORK / NON-COMPLIANT |
| **`audit`** | Audit an entire directory or design system | Full audit report in `ds/audits/active/` |

#### The Workflow

```
┌─────────────┐     ┌─────────┐     ┌─────────┐     ┌──────────┐
│  structure   │────▶│   doc   │────▶│   dev   │────▶│  review  │
│  (spec it)   │     │(doc it) │     │(build it)│    │(check it)│
└─────────────┘     └─────────┘     └─────────┘     └──────────┘

Quick path (simple component):    dev Button [figma-link]
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

## Two Ways to Work

### From Figma (via MCP)

Provide a Figma link — the skill calls Figma MCP and extracts everything automatically: component tree, props, variants, design tokens, and a screenshot for visual reference.

```
component structure Button https://figma.com/design/abc123/...?node-id=1-2
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
component structure Button
component dev "A toggle switch with on/off states, supports disabled"
```

---

## Installation

Install all skills:

```bash
npx skills add Maximepodgorski/agent-skills
```

Install only the component skill:

```bash
npx skills add Maximepodgorski/agent-skills --skill component
```

Global install (available across all your projects):

```bash
npx skills add Maximepodgorski/agent-skills --skill component -g
```

Target a specific agent:

```bash
npx skills add Maximepodgorski/agent-skills --skill component --agent claude-code
npx skills add Maximepodgorski/agent-skills --skill component --agent cursor
npx skills add Maximepodgorski/agent-skills --skill component --agent opencode
```

---

## Usage Examples

```bash
# Spec a component from Figma
component structure Button https://figma.com/design/...

# Spec from description
component structure "Dropdown menu with search, multi-select, and keyboard nav"

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

---

## How It Works Under the Hood

The skill is built on **5 principles** × **5 actions** × **8 templates**.

```
component/
├── SKILL.md                          ← Entry point, action router
└── references/
    ├── actions/                      ← What the skill does
    │   ├── structure.md              ← Spec generation logic
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

Everything is plain Markdown. Fork it, edit any file, make it yours.

---

## Contributing

Fork it, adapt it, break it, improve it. PRs and issues welcome.

---

## License

MIT — free to use, modify, and distribute.
