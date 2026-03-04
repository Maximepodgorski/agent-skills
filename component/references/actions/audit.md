# Audit Action

> **Agent:** Load this file when `audit` triggers. Also load all files in `references/principles/` and `references/templates/audit-report.md` for the report template.

System-wide DS audit. Two modes: codebase audit (scan directory) or Figma audit (scan design component). Generates a report in `ds/audits/active/`.

---

## Input Detection

```
Input contains figma.com URL?
  YES → Figma audit mode
  NO  → Input is a directory path?
    YES → Codebase audit mode
    NO  → Error: "Provide a directory path or Figma link."
```

## Step 0: Token Discovery (MANDATORY — both modes)

Before any compliance check, scan the project for existing design tokens:

```
1. Run Token Scan Protocol (see SKILL.md → Token Scan Protocol)
2. Build a map: raw_value → project_token_name
   e.g., #1A1A1A → --color-neutral-900, 16px → --spacing-4
3. Use this map to populate "Suggested Token" in the report
4. If no token vocabulary found → warn user, proceed with
   suggestions labeled as UNVERIFIED
```

This step ensures the "Suggested Token" column in the report is populated from actual project tokens, not hallucinated.

---

## Figma Audit Mode

Audit a Figma component for DS compliance BEFORE implementation.

### Step 1: Extract Design Context

```
Figma MCP available?
  YES → Call get_design_context(fileKey, nodeId)
  NO  → Warn user: "Figma MCP unavailable. Cannot perform Figma audit.
         Options: configure Figma MCP, or run a codebase audit instead
         with /component audit {directory}."
         → STOP (do not proceed without design context in Figma mode)
```

1. Call `get_design_context(fileKey, nodeId)`
2. Extract from response:
   - Component name
   - Props/variants defined
   - Values used (colors, spacing, typography, radius, shadows)
   - Sub-components referenced
   - Layer naming

### Step 2: Check Figma Compliance

#### Naming Standardization

```
Component naming:
  [ ] PascalCase? (Tooltip, not tooltip or TOOLTIP)
  [ ] Descriptive? (not "Frame 42" or "Group 7")
  [ ] Consistent with existing DS naming? (if DS exists)

Prop naming:
  [ ] camelCase for props? (isDisabled, not Is Disabled or is_disabled)
  [ ] Boolean props are adjectives? (disabled, not disable)
  [ ] Consistent across variants?

Prop values:
  [ ] Enum values standardized? (sm/md/lg, not small/medium/large mixed with s/m/l)
  [ ] No arbitrary values? (spacing 16 not 17, colors from palette not random)

Layer naming:
  [ ] Meaningful names? (not "Rectangle 4", "Frame 12")
  [ ] Follows convention? (icon, label, container — not random)
```

#### Token Usage

```
Colors:
  [ ] All colors from design token palette?
  [ ] No raw hex values that aren't tokens?
  [ ] Semantic naming? (bg-surface, not gray-100)

Spacing:
  [ ] All spacing from spacing scale?
  [ ] No arbitrary spacing? (e.g., 13px when scale is 4/8/12/16)

Typography:
  [ ] Font sizes from type scale?
  [ ] Font weights from defined set?
  [ ] Line heights standardized?

Radius:
  [ ] Border radius from radius scale?
  [ ] Consistent across similar components?

Shadows:
  [ ] Shadows from shadow tokens?
  [ ] Not custom per-component shadows?
```

### Step 3: Generate Figma Audit Report

**Severity classification:**

| Severity | Trigger |
|----------|---------|
| CRITICAL | Hardcoded value (hex, arbitrary px, non-token color) |
| CRITICAL | Non-standardized prop values (inconsistent enums) |
| WARNING | Naming inconsistency (non-PascalCase, non-camelCase) |
| WARNING | Missing token reference (value exists but not linked to token) |
| INFO | Layer naming could be improved |

**Verdict algorithm (same as codebase mode):**

```
critical_count = findings where severity == CRITICAL
warning_count = findings where severity == WARNING

IF critical_count == 0 AND warning_count <= 2:
  VERDICT = CLEAN
ELIF critical_count == 0 AND warning_count > 2:
  VERDICT = NEEDS WORK
ELSE:
  VERDICT = NON-COMPLIANT
```

## Codebase Audit Mode

Audit all components in a directory for DS consistency.

### Step 1: Discover Components

```
1. Scan directory for component files:
   - *.vue, *.tsx, *.jsx, *.svelte (detect from project)
   - Exclude: *.test.*, *.spec.*, *.story.*, *.stories.*

2. For each component, extract:
   - Props interface
   - CSS/styles (inline, module, scoped, styled-components)
   - Token usage
   - Sub-component imports
```

### Step 2: Scan for Hardcoded Values (ZERO TOLERANCE)

```
For EVERY component file, grep for:

CRITICAL — Hardcoded colors:
  /#[0-9a-fA-F]{3,8}/          → hex values
  /rgba?\([^)]+\)/             → rgb/rgba values
  /hsla?\([^)]+\)/             → hsl/hsla values
  /oklch\([^)]+\)/             → oklch values

CRITICAL — Hardcoded spacing:
  /: \d+px/                    → px values in CSS
  /: \d+rem/                   → rem values in CSS
  /: \d+em/                    → em values in CSS
  /margin|padding.*\d+/        → inline spacing
  /calc\([^)]*\d+px[^)]*\)/   → raw values inside calc()
  /clamp\([^)]*\d+px[^)]*\)/  → raw values inside clamp()

CRITICAL — Hardcoded typography:
  /font-size:\s*\d+/           → raw font sizes
  /font-weight:\s*\d+/         → raw font weights (except in token definitions)
  /line-height:\s*\d+/         → raw line heights

CRITICAL — Hardcoded radius:
  /border-radius:\s*\d+/       → raw radius values

CRITICAL — Hardcoded shadows:
  /box-shadow:\s*\d+/          → raw shadow values

CRITICAL — Hardcoded z-index:
  /z-index:\s*\d+/             → raw z-index values

CRITICAL — Hardcoded motion:
  /transition.*\d+(\.\d+)?m?s/ → raw transition durations
  /animation-duration:\s*\d+/  → raw animation durations

ALLOWED values (not violations):
  0, 100%, 1px, auto, inherit, transparent, currentColor, none

EXCLUDE from scan:
  - Token definition files (tokens.json, *.tokens.*, variables.css,
    _variables.scss, theme.ts — or any file where >50% of lines
    are CSS var / SCSS var declarations)
  - Test files: *.test.*, *.spec.*
  - Story files: *.story.*, *.stories.*
  - Config files: *.config.*, tailwind.config.*
  - SVG files: *.svg
  - CSS resets/base: reset.*, normalize.*, base.*
  - Tailwind: if project uses Tailwind (detected via tailwind.config.*),
    exclude utility class values — only scan custom CSS

CONTEXT-AWARE exclusions:
  - Values inside var() fallbacks: var(--token, #fff) → the #fff is
    a fallback, flag as WARNING not CRITICAL
  - CSS custom property declarations in token files → excluded
```

### Step 3: Cross-Component Consistency

```
Naming consistency:
  [ ] All components use same naming convention (PascalCase)
  [ ] All CSS follows same methodology (BEM or project pattern)
  [ ] All prop naming follows same pattern

Token consistency:
  [ ] Same colors referenced by same token across components
  [ ] No duplicate tokens (two names for same value)
  [ ] Semantic tier used consistently (not mixing --gray-100 and --bg-surface)

API consistency:
  [ ] Similar components have similar prop patterns
    (e.g., all components with size use sm/md/lg, not some with small/medium/large)
  [ ] Event naming consistent (onChange vs onUpdate vs update:value)
  [ ] Slot naming consistent (default, header, footer patterns)

Composition consistency:
  [ ] Similar atomic levels for similar complexity
  [ ] Compound patterns used consistently
```

### Step 4: Generate Codebase Audit Report

Follow the template in `references/templates/audit-report.md`.

## Init Folders (if first run)

```
ds/audits/active/ exists?
  NO → Create ds/audits/active/, ds/audits/shipped/, ds/audits/dropped/
  YES → Continue
```

## Report File

Save to: `ds/audits/active/{YYYY-MM-DD}-audit-{scope}.md`

Where `{scope}` is:
- Component name (Figma audit): `2026-02-22-audit-tooltip.md`
- Directory name (codebase audit): `2026-02-22-audit-components.md`

## Output

Follow the output template in `references/templates/audit-output.md`. Use the appropriate mode template (Figma or Codebase).

## Rules

- ZERO TOLERANCE on hardcoded values — every single one is CRITICAL
- Token definition files are excluded from the scan (they define values, not consume them)
- Figma audit focuses on design compliance — naming, values, token usage
- Codebase audit focuses on implementation compliance — hardcoded values, consistency
- Cross-component consistency only in codebase mode (Figma audit is single-component)
- Report must be actionable — every finding has a concrete fix
