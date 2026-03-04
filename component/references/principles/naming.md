# Naming — BEM + Component Conventions

> Based on **BEM** (Block Element Modifier) methodology. Adapted for modern component-based development.

## Component Naming

| Entity | Convention | Example | Anti-pattern |
|--------|-----------|---------|--------------|
| Component file | PascalCase | `Tooltip.{ext}`, `DataTable.{ext}` | `tooltip.{ext}`, `data-table.{ext}` |
| Component name | PascalCase | `<Tooltip>`, `<DataTable>` | `<tooltip>`, `<data_table>` |
| Compound sub-component | ParentChild PascalCase | `SelectItem`, `DialogTitle` | `Item`, `Title` (too generic) |
| Directory | PascalCase (matches component) | `Tooltip/`, `DataTable/` | `tooltip/`, `data-table/` |
| Test file | ComponentName.test.ts | `Tooltip.test.ts` | `tooltip.spec.js` (match project) |
| Doc file | doc.md | `doc.md` | `Tooltip.doc.md` (folder gives context) |
| Index/barrel | index.ts | `index.ts` | `Tooltip.index.ts` |

## BEM for CSS Classes

Block Element Modifier — flat, predictable, collision-free.

```
.block                    → Component root
.block__element           → Child part of the component
.block--modifier          → Variant of the component
.block__element--modifier → Variant of a child part
```

### Concrete Examples

```css
/* Block */
.tooltip { }

/* Elements */
.tooltip__content { }
.tooltip__arrow { }
.tooltip__trigger { }

/* Modifiers */
.tooltip--dark { }
.tooltip--placement-top { }
.tooltip__content--wide { }
```

### Do / Don't

| Do | Don't |
|----|-------|
| `.button--primary` | `.button.primary` (chaining) |
| `.card__header` | `.card .header` (descendant — fragile) |
| `.form-field__error--visible` | `.form-field .error.is-visible` (nested chaining) |
| `.data-table__row--selected` | `.selected-row` (no BEM relationship) |

### BEM Adaptation

BEM is the reference methodology. If the project uses a different convention, **match the project**:

```
Project uses CSS Modules?
  → .container, .header, .isActive (camelCase, no BEM needed — scoping handles collisions)

Project uses Tailwind?
  → No custom classes — utility composition (no BEM)

Project uses styled-components/CSS-in-JS?
  → Component-level scoping — BEM for internal structure only if needed

Project uses BEM?
  → Follow BEM strictly
```

**Rule:** Detect the project's convention first. BEM is the fallback, not the mandate.

## Props Naming

| Convention | Example | Anti-pattern |
|-----------|---------|--------------|
| camelCase | `isDisabled`, `maxWidth` | `is-disabled`, `max_width` |
| Boolean = adjective | `disabled`, `loading`, `open` | `isDisabled`, `setLoading` (verb prefix) |
| Boolean = positive | `visible` (not `hidden`) | `notVisible`, `isHidden` (negative) |
| Handler = on + Event | `onChange`, `onClose` | `handleChange`, `closeHandler` |
| Enum = value options | `size: 'sm' \| 'md' \| 'lg'` | `size: 'small' \| 'medium' \| 'large'` (verbose) |
| Children slot = default | Default slot for primary content | `children` prop with VNode |

### Enum Standardization

When a prop accepts enum values, use consistent scales across all components:

| Category | Standard scale | Anti-pattern |
|----------|---------------|--------------|
| Size | `xs`, `sm`, `md`, `lg`, `xl` | `small`, `medium`, `large` mixed with `s`, `m`, `l` |
| Variant | `primary`, `secondary`, `tertiary`, `ghost` | `main`, `default`, `outline` (inconsistent) |
| Status | `info`, `success`, `warning`, `error` | `blue`, `green`, `yellow`, `red` (color-based) |
| Placement | `top`, `bottom`, `left`, `right` | `above`, `below` (inconsistent vocabulary) |

**Rule:** Pick ONE scale per category and use it across ALL components. Consistency > preference.

## CSS Variables / Tokens Naming

Follow the project's existing convention. If none exists, use semantic naming:

```
Tier 1 — Primitive (raw values):
  --gray-100, --gray-200, --blue-500

Tier 2 — Semantic (purpose):
  --color-bg-surface, --color-text-primary, --color-border-default

Tier 3 — Component (scoped):
  --tooltip-bg, --tooltip-text, --tooltip-radius
```

### Do / Don't

| Do | Don't |
|----|-------|
| `--color-bg-surface` (semantic) | `--gray-100` (primitive in component code) |
| `--spacing-4` (from scale) | `16px` (hardcoded) |
| `--font-sm` (from type scale) | `14px` (hardcoded) |
| `--radius-md` (from radius scale) | `8px` (hardcoded) |
| `var(--tooltip-bg)` (component token) | `var(--gray-800)` (primitive in component) |

## Event Naming

| Framework | Convention | Example |
|-----------|-----------|---------|
| Vue | `update:propName` for v-model, verb for actions | `update:modelValue`, `close`, `select` |
| React | `on` + PascalCase verb | `onChange`, `onClose`, `onSelect` |
| Svelte | `on:eventname` lowercase | `on:change`, `on:close`, `on:select` |

**Rule:** Match the framework's idiomatic convention. Detect from codebase.

## File Export Naming

```
// index.ts — barrel export
export { Tooltip } from './Tooltip'
export { TooltipTrigger } from './TooltipTrigger'
export { TooltipContent } from './TooltipContent'

// Types
export type { TooltipProps } from './Tooltip'
```

Named exports > default exports (better tree-shaking, explicit imports).
