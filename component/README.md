# Component Skill

Design system component workflow. Figma to production, DS-compliant.

## Quick Start

```bash
component spec Button https://figma.com/design/...          # Generate spec from Figma
component spec-review Button                                 # Multi-perspective spec review
component doc Button                                         # Generate documentation
component dev Button https://figma.com/design/...            # Implement with ship loop
component review src/components/Button.vue                   # DS compliance check
component audit src/components/                              # System-wide audit
```

## What It Does

6 actions to take a component from Figma design to production-ready code:

| Action | What | Output |
|--------|------|--------|
| **spec** | Generate component spec from Figma or description | Spec file in `ds/specs/active/` |
| **spec-review** | Multi-perspective spec review (4 parallel reviewers) | Consolidated review with verdicts |
| **doc** | Generate component documentation | `doc.md` co-located with component |
| **dev** | Implement component (iterative ship loop) | Production-ready component |
| **review** | Check DS compliance against 5 principles | Compliance report with verdict |
| **audit** | System-wide design system audit | Audit report in `ds/audits/active/` |

## Spec-Review: 4 Perspectives

The `spec-review` action launches 4 expert reviewers in parallel for fast, thorough spec validation:

| Perspective | Focus | Verdict |
|-------------|-------|---------|
| **Front Engineer** | Implementability, types, edge cases, testability | READY / NEEDS WORK / BLOCKED |
| **DS Manager** | Consistency, naming, tokens, composition, API surface | CONSISTENT / NEEDS ALIGNMENT / INCONSISTENT |
| **Accessibility Specialist** | WCAG 2.1 AA, keyboard, ARIA, screen reader, contrast | ACCESSIBLE / GAPS / NON-COMPLIANT |
| **Product Designer** | Design intent, states, responsive, content flexibility | FAITHFUL / DRIFT / MISSING INTENT |

Consolidated verdicts: **APPROVED** | **APPROVED WITH NOTES** | **NEEDS REVISION** | **BLOCKED**

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
- Parallel spec review from 4 expert perspectives

## Recommended Workflow

```
New component:      spec → spec-review → doc → dev → review
Quick (simple):     dev {name} [figma-link]
Maintenance:        review {path}  or  doc {path}
Spec validation:    spec-review {spec-path}
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
