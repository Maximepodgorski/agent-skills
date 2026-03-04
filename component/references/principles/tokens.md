# Tokens — DTCG Standard + Reuse > Create

> Based on **Design Token Community Group (DTCG)** W3C standard.

## Core Principle

**Reuse > Create. Never hardcode. Never invent.**

```
Need a value?
  │
  ├─ Token exists in project? → USE IT
  │
  ├─ Similar token exists? → ASK user: use closest match or create new?
  │
  └─ No match at all? → ASK user: create new token with what name?

  NEVER → Hardcode a raw value (hex, px, rem)
  NEVER → Create a token without user approval
```

## What is a Design Token

A design token is a named value that captures a design decision. Instead of `#1A1A1A`, you use `--color-text-primary`. The value can change; the intent remains.

```
Raw value:    color: #1A1A1A;              ← WRONG (hardcoded)
Token:        color: var(--color-text);     ← CORRECT (semantic intent)
```

## DTCG Token Format

The W3C Design Token Community Group defines a standard JSON format:

```json
{
  "color": {
    "primary": {
      "$value": "#3B82F6",
      "$type": "color",
      "$description": "Primary brand color"
    }
  },
  "spacing": {
    "4": {
      "$value": "16px",
      "$type": "dimension"
    }
  }
}
```

The skill doesn't enforce DTCG JSON format — it's a reference standard. The project may use CSS vars, SCSS vars, Tailwind config, or any format.

## Token Tiers

Three tiers from abstract to concrete:

```
┌─────────────────────────────────────────────┐
│  Tier 3: Component tokens (scoped)          │
│  --tooltip-bg, --button-radius              │
│  ┌─────────────────────────────────────┐    │
│  │  Tier 2: Semantic tokens (purpose)  │    │
│  │  --color-bg-surface, --spacing-md   │    │
│  │  ┌─────────────────────────────┐    │    │
│  │  │  Tier 1: Primitive (values) │    │    │
│  │  │  --gray-100, --blue-500     │    │    │
│  │  └─────────────────────────────┘    │    │
│  └─────────────────────────────────────┘    │
└─────────────────────────────────────────────┘
```

| Tier | Purpose | Used in | Example |
|------|---------|---------|---------|
| Primitive | Raw values, the palette | Token definitions only | `--gray-100: #F5F5F5` |
| Semantic | Design intent | Anywhere | `--color-bg-surface: var(--gray-100)` |
| Component | Scoped overrides | Specific component | `--tooltip-bg: var(--color-bg-surface)` |

### Rule: Components use Semantic or Component tier, NEVER Primitive.

```css
/* GOOD — semantic tier */
.tooltip { background: var(--color-bg-surface); }

/* GOOD — component tier */
.tooltip { background: var(--tooltip-bg); }

/* BAD — primitive tier in component */
.tooltip { background: var(--gray-100); }

/* CRITICAL — hardcoded value */
.tooltip { background: #F5F5F5; }
```

## Token Categories

| Category | What it captures | Examples |
|----------|-----------------|---------|
| Color | Brand, surface, text, border, state | `--color-primary`, `--color-bg-surface`, `--color-text-muted` |
| Spacing | Margins, paddings, gaps | `--spacing-1` (4px), `--spacing-2` (8px), `--spacing-4` (16px) |
| Typography | Font size, weight, family, line height | `--font-sm`, `--font-weight-bold`, `--font-family-mono` |
| Radius | Border radius | `--radius-sm`, `--radius-md`, `--radius-full` |
| Shadow | Box shadows | `--shadow-sm`, `--shadow-md`, `--shadow-lg` |
| Motion | Duration, easing | `--duration-fast`, `--easing-ease-out` |
| Z-index | Stacking layers | `--z-dropdown`, `--z-modal`, `--z-tooltip` |

## Hardcoded Value Detection

These patterns are ALWAYS violations:

| Pattern | Example | Severity |
|---------|---------|----------|
| Hex color | `color: #1A1A1A` | CRITICAL |
| RGB/RGBA | `background: rgba(0,0,0,0.5)` | CRITICAL |
| HSL/HSLA | `color: hsl(0, 0%, 10%)` | CRITICAL |
| Raw px in spacing | `padding: 16px` | CRITICAL |
| Raw rem/em | `margin: 1.5rem` | CRITICAL |
| Raw font-size | `font-size: 14px` | CRITICAL |
| Raw font-weight | `font-weight: 600` | CRITICAL |
| Raw border-radius | `border-radius: 8px` | CRITICAL |
| Raw box-shadow | `box-shadow: 0 2px 4px rgba(...)` | CRITICAL |
| Raw z-index | `z-index: 999` | CRITICAL |

### Exceptions (NOT violations)

| Pattern | Why it's OK |
|---------|-------------|
| `0` or `0px` | Zero is universal |
| `100%`, `50%` | Percentage layouts |
| `1px` for borders | Common convention, often not tokenized |
| `auto`, `inherit`, `currentColor` | CSS keywords |
| Token definition files | They DEFINE the raw values |
| Tailwind utility classes | Values come from config, not hardcoded |

### When no token exists

For one-off values that don't warrant a token (specific `height`, `width`, `max-width`, etc.), **always use `rem` over `px`**. `rem` respects user font-size preferences (accessibility), `px` does not.

```css
/* GOOD — rem for non-tokenized values */
.modal { max-width: 32rem; }
.avatar { width: 2.5rem; height: 2.5rem; }

/* BAD — px ignores user font-size */
.modal { max-width: 512px; }
.avatar { width: 40px; height: 40px; }
```

**Exception:** `1px` borders remain OK — `0.0625rem` is unreadable for no real benefit.

## Naming Adaptation

The skill matches the project's existing token naming:

```
Project: --bg-*          → Generate: --bg-tooltip
Project: --color-bg-*    → Generate: --color-bg-tooltip
Project: $bg-*           → Generate: $bg-tooltip
Project: theme.colors.*  → Generate: theme.colors.tooltip.bg
Project: bg-surface      → Generate: class="bg-tooltip" (Tailwind)
```

**Rule:** Scan existing tokens, detect the naming pattern, follow it exactly. Never impose a different convention.

## Do / Don't

| Do | Don't |
|----|-------|
| `var(--color-text-primary)` | `#1A1A1A` |
| `var(--spacing-4)` | `16px` |
| `var(--font-sm)` | `14px` |
| `var(--radius-md)` | `8px` |
| `var(--shadow-sm)` | `0 1px 2px rgba(0,0,0,0.1)` |
| `var(--tooltip-bg)` (component token) | `var(--gray-800)` (primitive in component) |
| Ask user before creating `--color-accent-500` | Silently create a new token |
| `class="bg-surface p-4 rounded-md"` (Tailwind) | `style="background: #f5f5f5; padding: 16px"` |
