<div align="center">

```
    _                    _   ____  _    _ _ _
   / \   __ _  ___ _ __ | |_/ ___|| | _(_) | |___
  / _ \ / _` |/ _ \ '_ \| __\___ \| |/ / | | / __|
 / ___ \ (_| |  __/ | | | |_ ___) |   <| | | \__ \
/_/   \_\__, |\___|_| |_|\__|____/|_|\_\_|_|_|___/
        |___/
```

**Procedural knowledge for AI agents. One command to install.**

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Skills](https://img.shields.io/badge/skills-1-blue.svg)](./#available-skills)

**Works with:** Claude Code · OpenCode · Windsurf · Cursor · Codex · [30+ agents](https://skills.sh)

</div>

---

## What Are Agent Skills?

Skills are **reusable capabilities** you plug into your AI coding agent. Instead of repeating prompts, writing custom instructions, or copy-pasting workflows — you install a skill once and it just works.

```bash
npx skills add Maximepodgorski/agent-skills
```

Each skill encodes **deep domain knowledge** — standards, patterns, checklists, templates — so your agent operates like a senior specialist, not a generic assistant.

---

## Available Skills

### [Component](./component/) — Design System Workflow

> **Figma to production. DS-compliant. Zero hardcoded values.**

Take any component from design to ship-ready code. The skill enforces Atomic Design, BEM, DTCG tokens, WCAG 2.1 AA — and catches every hardcoded `#hex` or `16px` before it hits your codebase.

5 actions: `structure` · `doc` · `dev` · `review` · `audit`

```
structure → doc → dev → review
```

Stack-agnostic: React, Vue, Svelte, Angular, Web Components.

[View full documentation →](./component/SKILL.md)

---

## Two Ways to Work

### From Figma (via MCP)

Provide a Figma link — the skill extracts design context automatically: component hierarchy, props, variants, tokens.

```
component structure Button https://figma.com/design/abc123/...?node-id=1-2
component dev Button https://figma.com/design/abc123/...?node-id=1-2
```

Requires [Figma MCP](https://github.com/anthropics/claude-code/blob/main/docs/figma.md) configured in your agent.

### From Prompt (no Figma)

Describe the component — the skill works from your codebase and text input.

```
component structure Button
component dev Button
```

The skill scans your project for existing tokens, conventions, and patterns to stay consistent.

---

## Installation

Install the component skill:

```bash
npx skills add Maximepodgorski/agent-skills --skill component
```

Global install (available across all projects):

```bash
npx skills add Maximepodgorski/agent-skills --skill component -g
```

Specific agent:

```bash
npx skills add Maximepodgorski/agent-skills --skill component --agent claude-code
```

---

## Usage

```bash
# Spec a component from Figma
component structure Button https://figma.com/design/...

# Implement it (iterative ship loop)
component dev Button https://figma.com/design/...

# Generate docs
component doc Button

# Check DS compliance
component review src/components/Button.vue

# Audit the full system
component audit src/components/
```

---

## Contributing

Fork it, adapt it, break it, improve it. PRs and issues welcome.

---

## License

MIT — free to use, modify, and distribute.
