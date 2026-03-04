<div align="center">

# Agent Skills

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Skills](https://img.shields.io/badge/skills-1-blue.svg)](./#available-skills)

**Compatible with:** Claude Code, OpenCode, Windsurf, Cursor, Codex, and more via [skills.sh](https://skills.sh)

[GitHub](https://github.com/mapple) | [Web](https://getlyse.com)

</div>

---

## Installation

```bash
npx skills add mapple/agent-skills
```

---

## Available Skills

### [Component](./component/) - Design System Component Workflow

Figma to production, DS-compliant. Structure, document, implement, review, and audit design system components.

5 actions: `structure`, `doc`, `dev`, `review`, `audit`.

[View skill documentation →](./component/SKILL.md)

---

## Installation Options

Install the component skill:

```bash
npx skills add mapple/agent-skills --skill component
```

Global install:
```bash
npx skills add mapple/agent-skills --skill component -g
```

Specific agent:
```bash
npx skills add mapple/agent-skills --skill component --agent claude-code
```

---

## Two Ways to Work

### From Figma (via MCP)

Provide a Figma link — the skill extracts design context automatically (component hierarchy, props, variants, tokens).

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

## Usage Examples

Structure a component from Figma:
```
component structure Button https://figma.com/design/...
```

Implement from description:
```
component dev Button
```

Review DS compliance:
```
component review src/components/Button.vue
```

Audit the full design system:
```
component audit src/components/
```

---

## Contributing

Feel free to:
- Fork and adapt for your needs
- Submit issues for bugs or improvements
- Share your own variations

---

## License

MIT License - Free to use, modify, and distribute.
