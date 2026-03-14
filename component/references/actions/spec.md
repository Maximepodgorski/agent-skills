# Spec Action

> **Agent:** Load this file when `spec` triggers. Also load `references/templates/spec.md` for the output template.

Generate a component architecture spec. Primary input: Figma link. Fallback: component name + manual description.

---

## Input Detection

```
Input contains figma.com URL?
  YES → Figma mode (extract design context first)
  NO  → Manual mode (component name + codebase scan)
```

## Step 0: Figma Context (if link provided)

1. Parse Figma URL → extract `fileKey` + `nodeId`
2. Call `get_design_context(fileKey, nodeId)`
3. Receive: code, screenshot, contextual hints
4. Extract from Figma response:
   - Component hierarchy (parent → children → slots)
   - Props/variants defined in Figma
   - Design tokens referenced (colors, spacing, typography)
   - Component instances used (identify sub-components)

## Step 1: Codebase Scan

Before writing anything, scan the project:

```
1. Find existing components → detect file structure pattern
   - Single-file? (Button.{ext})
   - Directory? (Button/Button.{ext} + index.{ext})
   - Compound? (Select/Select.{ext} + SelectItem.{ext} + ...)

2. Find existing tokens → detect naming convention
   - CSS vars: --color-*, --spacing-*, --radius-*
   - SCSS vars: $color-*, $spacing-*
   - Tailwind config: theme.extend.*
   - Token files: tokens.json, *.tokens.*

3. Find similar components → detect API patterns
   - How are props typed? (interface, type, defineProps)
   - How are slots defined? (named, scoped, default)
   - How are events emitted? (emit, callback props)
   - How are components exported? (named, default, barrel)

4. Read CLAUDE.md → project conventions, stack, patterns
```

## Step 2: Cross-Reference (Figma + Codebase)

If Figma context is available:

```
For each Figma prop/variant:
  → Does a similar prop exist in codebase components?
  → Does the naming match project conventions?
  → Are Figma token values mapped to existing project tokens?

For each Figma sub-component:
  → Does it already exist in the codebase?
  → Can it be reused? Or must it be created?
```

## Step 2.5: Existing Spec Check

```
ds/specs/active/{component-name}-spec.md already exists?
  YES → Ask user: "Spec already exists. Overwrite, update, or cancel?"
    Overwrite → Delete existing, generate fresh
    Update → Read existing spec, merge with new Figma/codebase context
    Cancel → Stop, return existing spec path
  NO → Continue
```

## Step 3: Generate Spec

Follow the template in `references/templates/spec.md` exactly.

Generate in this order:
1. **User Story** — from Figma design intent + codebase context
2. **Component Tree** — ASCII diagram from Figma hierarchy or manual analysis
3. **File Structure** — matching project's existing pattern
4. **API (Props, Slots, Events, Expose)** — derived from Figma variants + codebase conventions
5. **Acceptance Criteria** — testable, pass/fail
6. **Edge Cases** — empty states, overflow, disabled, loading, error
7. **Accessibility** — keyboard, screen reader, ARIA
8. **Decisions** — architectural choices with rationale

Save to: `ds/specs/active/{component-name}-spec.md`

## Step 4: Init Folders (if first run)

```
ds/specs/active/ exists?
  NO → Create ds/specs/active/, ds/specs/shipped/, ds/specs/dropped/
  YES → Continue
```

## Output

Follow the output template in `references/templates/spec-output.md`.

## Rules

- Never invent tokens — map Figma values to existing project tokens
- If a value has no matching token → flag it in the spec as "MISSING TOKEN: {value}"
- ASCII tree must reflect the ACTUAL component hierarchy, not a generic template
- Props API must match project conventions (naming, typing, defaults)
- File structure must match project's existing component pattern
- Spec must be implementable by another developer without additional context
