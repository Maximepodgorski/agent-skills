# Component Skill

Design system component workflow. Figma to production, DS-compliant.

## Quick Start

```bash
component structure Button https://figma.com/design/...    # Generate spec from Figma
component doc Button                                        # Generate documentation
component dev Button https://figma.com/design/...           # Implement with ship loop
component review src/components/Button.vue                  # DS compliance check
component audit src/components/                             # System-wide audit
```

## What It Does

5 actions to take a component from Figma design to production-ready code:

| Action | What | Output |
|--------|------|--------|
| **structure** | Generate component spec from Figma or description | Spec file in `ds/specs/active/` |
| **doc** | Generate component documentation | `doc.md` co-located with component |
| **dev** | Implement component (iterative ship loop) | Production-ready component |
| **review** | Check DS compliance against 5 principles | Compliance report with verdict |
| **audit** | System-wide design system audit | Audit report in `ds/audits/active/` |

## Features

- Stack-agnostic: React, Vue, Svelte, Angular, Web Components
- Figma MCP integration for design context extraction
- Zero hardcoded values enforcement (colors, spacing, radius)
- Token scan protocol: reuse existing tokens, never invent
- Human-in-the-loop: new tokens require user approval
- WCAG 2.1 AA accessibility compliance
- Atomic Design composition model
- BEM naming conventions
- DTCG token standard

## Recommended Workflow

```
New component:      structure → doc → dev → review
Quick (simple):     dev {name} [figma-link]
Maintenance:        review {path}  or  doc {path}
System-wide:        audit {directory}
```

## Principles

| Principle | Standard | Reference |
|-----------|----------|-----------|
| Composition | Atomic Design (Brad Frost) | [composition.md](references/principles/composition.md) |
| Naming | BEM (Block Element Modifier) | [naming.md](references/principles/naming.md) |
| Tokens | DTCG (W3C Design Token Community Group) | [tokens.md](references/principles/tokens.md) |
| Props API | Component API patterns | [props-api.md](references/principles/props-api.md) |
| Accessibility | WCAG 2.1 AA | [accessibility.md](references/principles/accessibility.md) |

## Configuration

All behavior is configurable by editing skill files directly:

| What to change | Edit |
|----------------|------|
| Action logic | `references/actions/{action}.md` |
| DS principles | `references/principles/{principle}.md` |
| Output templates | `references/templates/{template}.md` |
