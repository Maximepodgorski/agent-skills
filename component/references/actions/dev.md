# Dev Action

> **Agent:** Load this file when `dev` triggers. Also load all files in `references/principles/` for DS compliance during implementation.

Implement a component using the ship loop pattern. Figma design context + spec + existing project tokens drive the implementation.

---

## Input Detection

```
Input contains figma.com URL?
  YES → Load Figma context via get_design_context
  NO  → Continue without Figma

Active spec in ds/specs/active/?
  YES → Check Blockers section
    Any blocker with status OPEN?
      YES → Error: "Cannot start dev. {N} open blocker(s):
             - {blocker 1} ({type})
             - {blocker 2} ({type})
             Resolve blockers first or run /component spec {name} to update the spec."
      NO  → LOOP mode (iterate over spec items)
  NO  → ONE-SHOT mode (create inline spec, implement, validate)
```

## Phase 0: Setup

### Token Scan (MANDATORY)

Before writing any component code, scan the project for existing design tokens:

```
1. SCAN sources:
   ├── CSS custom properties:  grep --include="*.css" --include="*.scss" "var(--" or "$"
   ├── Tailwind config:        read tailwind.config.* → theme.extend
   ├── Token files:            glob "*.tokens.*", "tokens.json", "tokens.yaml"
   └── CSS-in-JS themes:       grep "theme." in styled/emotion files

2. CATALOG found tokens:
   | Category    | Pattern          | Count | Example                    |
   |-------------|------------------|-------|----------------------------|
   | Color       | --color-*        | 24    | --color-neutral-700        |
   | Spacing     | --spacing-*      | 8     | --spacing-4                |
   | Typography  | --font-*         | 6     | --font-sm                  |
   | Radius      | --radius-*       | 4     | --radius-md                |
   | Shadow      | --shadow-*       | 3     | --shadow-sm                |

3. DETECT naming pattern:
   - What prefix convention? (--color-* vs --clr-* vs $color-*)
   - What tier structure? (primitive → semantic → component)
   - What scale? (4px increments? t-shirt sizes? numbered?)
```

### Figma Context (if available)

```
1. Call get_design_context(fileKey, nodeId)
2. Receive: code + screenshot + hints
3. Map Figma values to scanned tokens:
   - Figma #1A1A1A → --color-neutral-900 ✓
   - Figma 16px → --spacing-4 ✓
   - Figma #FF6B35 → ??? NO MATCH → FLAG for user
4. Store mapping for implementation
```

### Human-in-the-Loop: Missing Tokens

```
For each Figma value with no token match:
  PAUSE → Present to user:

  "Missing token for: #FF6B35 (used as accent background)
   Closest matches:
     1. --color-primary-500 (#3B82F6) — different hue
     2. --color-warning-500 (#F59E0B) — closer but not exact
     3. Create new token: --color-accent-500
     4. Other

   Which option?"

  WAIT for user response → apply choice → continue
```

**Rule: NEVER create a token without explicit user approval. NEVER use hardcoded values.**

## ONE-SHOT Flow

For quick implementations without an active spec:

```
1. Analyze component requirements (from Figma or user description)
2. Create inline spec (minimal: component tree, props, key behaviors)
3. Run token scan
4. Implement component
5. Quick quality pass (lint + typecheck on changed files)
6. Self-review against DS principles
7. Output summary
```

## LOOP Flow (Ship Loop)

Core iteration algorithm for spec-driven component development:

```
iteration = 0
max_iterations = 12  (default for component dev)
stuck_threshold = 6

WHILE iteration < max_iterations:
  iteration++

  # 1. BUILD
  Pick next unchecked spec item (prop, slot, sub-component, behavior)
  Implement it using:
    - Existing project tokens (from scan)
    - Figma design context (if available)
    - Project conventions (from CLAUDE.md + codebase patterns)

  RULES during BUILD:
    - Use tokens, NEVER raw values (hex, px, rem)
    - Match project's naming convention for CSS classes/variables
    - Match project's prop typing pattern
    - Match project's component export pattern

  # 2. TEST
  Run quick quality pass:
    - Lint changed files
    - Typecheck changed files
  Mark spec item [x] if passing

  # 3. REVIEW (every 2-3 items)
  Check against DS principles:
    - composition.md: Is component structure correct? (atomic level, compound pattern)
    - naming.md: Do CSS classes follow BEM? Variables follow convention?
    - tokens.md: Zero hardcoded values? All tokens from project?
    - props-api.md: Props minimal? Defaults smart? Types strict?
    - accessibility.md: Keyboard nav? ARIA? Focus management?

  Collect issues: CRITICAL (hardcoded values, a11y) vs WARN (naming, structure)

  # 4. FIX
  Fix all CRITICAL issues immediately
  Log WARN issues in spec notes

  # 5. EXIT CHECK
  all_spec_items_complete = all spec scope items checked [x]
  zero_hardcoded_values = no hex, px, rem literals in component code
  tokens_all_project = all tokens exist in project (none invented)
  quality_clean = lint + typecheck pass
  no_critical_issues = no CRITICAL review issues

  IF all_spec_items_complete AND zero_hardcoded_values
     AND tokens_all_project AND quality_clean AND no_critical_issues:
    EXIT → suggest /component review for final pass

  # 6. STUCK DETECTION
  progress = spec_items_completed / total_spec_items
  IF iteration >= stuck_threshold AND progress < 0.5:
    ESCALATE to user:
      1. Continue with different approach
      2. Reduce scope
      3. Defer
```

## Token Naming Adaptation

The skill NEVER imposes its own naming. It adapts to the project:

```
Project uses --bg-* prefix?
  → Generate: --bg-tooltip (not --color-tooltip-bg)

Project uses $spacing-* SCSS vars?
  → Generate: $spacing-tooltip-offset (not --spacing-tooltip-offset)

Project uses Tailwind utilities?
  → Generate: class="bg-surface p-4 rounded-md" (not custom CSS)

Project uses CSS Modules?
  → Generate: styles.tooltipContainer (not .tooltip-container)
```

## Progress Tracking

Update spec after each iteration:

```markdown
## Progress
| # | Spec Item       | Status        | Iteration |
|---|-----------------|---------------|-----------|
| 1 | Base component  | [x] Complete  | 1         |
| 2 | Props interface | [x] Complete  | 2         |
| 3 | Slot system     | [~] In progress | 3       |
| 4 | Keyboard nav    | [ ] Pending   | -         |
```

## Output

Follow the output template in `references/templates/dev-output.md`. Use the iteration template during the ship loop and the appropriate exit template when done.

## Never

- Never use hardcoded hex values — always map to tokens
- Never use hardcoded px/rem values — always use spacing tokens
- Never create tokens without user approval
- Never invent naming patterns — match the project
- Never hardcode framework-specific syntax — detect from codebase
