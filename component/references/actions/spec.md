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
1. **Header** — Goal (one sentence), Architecture (2-3 sentences), Tech Stack (detected from codebase)
2. **User Story** — from Figma design intent + codebase context
3. **Component Tree** — ASCII diagram from Figma hierarchy or manual analysis
4. **File Structure** — matching project's existing pattern, with exact paths (Create / Modify / Test)
5. **API (Props, Slots, Events, Expose)** — derived from Figma variants + codebase conventions
6. **Token Mapping** — Figma values → project tokens, flag missing
7. **Acceptance Criteria** — testable, pass/fail
8. **Edge Cases** — empty states, overflow, disabled, loading, error
9. **Accessibility** — keyboard, screen reader, ARIA
10. **Decisions** — architectural choices with rationale
11. **Blockers** — any OPEN blocker = NO DEV
12. **Implementation Tasks** — bite-sized TDD tasks (see Step 4)

Save to: `ds/specs/active/{component-name}-spec.md`

## Step 4: Generate Implementation Tasks (writing-plans pattern)

Decompose the component into tasks the ship action can execute one-by-one. Each task delivers a self-contained, testable change.

**Task granularity:**
- Task 1 = scaffold + base render (always)
- One task per logical unit: prop, slot, sub-component, behavior, keyboard handler, edge case
- Last task = accessibility polish + lint/typecheck pass
- Each step inside a task = 2-5 minutes

**Per-step format (mandatory):**
1. Write the failing test — actual test code, not "write a test for X"
2. Run test — verify it fails — exact command + expected failure message
3. Implement minimal code — actual code block, using project tokens only
4. Run test — verify it passes — exact command
5. Commit — exact `git add` paths + conventional commit message

**Stack detection drives concrete content:**
- Read package.json / project config to find: test runner, lint cmd, typecheck cmd
- Read existing test files to match the project's test style
- Read CLAUDE.md to follow commit conventions
- Use project's actual file extensions (.vue, .tsx, .svelte, .ts, etc.)

**No placeholders rule (these are spec failures):**
- "TBD", "implement later", "add validation", "handle edge cases"
- "Similar to Task N" (repeat the code — engineers may read tasks out of order)
- `// ...` or empty code blocks
- Steps describing what to do without showing how
- References to types/methods not defined in any task

## Step 5: Self-Review (before saving)

Look at the generated spec with fresh eyes. Run this checklist yourself — not as a subagent dispatch:

1. **Coverage:** Each AC and edge case → is there an Implementation Task exercising it?
2. **Placeholder scan:** grep for the failure patterns above. Fix inline.
3. **Type consistency:** Prop / method / event / class names match across API tables AND task code blocks?
4. **Token discipline:** Zero hex / px / rem literals in any code block. Every value references a project token.
5. **Command accuracy:** Every `Run: {cmd}` is verified against package.json or project conventions.
6. **Granularity:** Each step is 2-5 minutes. Split anything bigger.

If issues found → fix inline. No need to re-review.

## Step 6: Init Folders (if first run)

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
- Implementation Tasks must be executable as-is — no placeholders, exact paths, exact commands, real code
- Spec must be implementable by another developer without additional context
