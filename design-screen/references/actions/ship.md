# Ship Action

> **Agent:** Load this file when `ship` triggers. Also load `references/principles/composition.md` and `references/principles/page-states.md`. Load `references/templates/spec.md` for spec validation rules.

Implement the screen from spec using a ship loop. Handles both spec-driven iteration and one-shot quick screens.

**Triggers:** "ship", "implement", "build", "ship screen"

---

## Mode Detection

```
Active spec in ds/screens/active/?
  YES → LOOP mode (iterate until spec complete)
  NO  → Estimate scope
    Simple screen (< 5 zones, high coverage)  → ONE-SHOT (quick compose + implement)
    Complex screen                             → Error: "Run /design-screen spec first."

ds/conventions.md exists?
  NO → Error: "No conventions found. Run /design-screen init first."
  YES → Load conventions for implementation guidance

Component inventory scan returns 0 components?
  YES → Error: "No components found. The design-screen skill composes from existing components.
         Create components first (manually or via /component), then retry."
  NO → Continue
```

## Task Tracking (MANDATORY for standard tier)

Before starting any implementation, create tasks in your agent's built-in task/todo system.

**Create these tasks at the start of each ship cycle:**
```
[ ] {zone/scope item 1} → AC-{N}
[ ] {zone/scope item 2} → AC-{N}
[ ] ...
[ ] Quick pass: lint + typecheck (changed files)
[ ] Self-review: spec fidelity check
[ ] Fix BLOCKING issues
[ ] Full pass: lint (changed) + typecheck (full) + build (full)
[ ] Exit check: all scope items + ACs + quality gates passing
```

Update tasks as you work: mark in-progress when starting, complete when done. One task in-progress at a time.

## Phase 0: Setup

1. **Load spec** — read full screen spec from ds/screens/active/
2. **Load conventions** — read ds/conventions.md
3. **Extract scope items** — from spec's Progress table
4. **Detect resume state:**
   ```
   All items pending     → FRESH (start from beginning)
   Some items complete   → RESUMING (continue from last checkpoint)
     → Read spec's Notes section for any context from previous session
     → Start from first unchecked item
     → Verify previously completed items still pass (quick lint/typecheck)
   All items complete    → READY_FOR_DONE (suggest /design-screen review)
   ```
5. **Set iteration bounds:**
   ```
   total_items = count of scope items in spec
   max_iterations = total_items × 2 (cap at 20)
   stuck_threshold = max(3, max_iterations / 2)
   ```

### Default Iterations by Size

| Tier | Max Iterations | Stuck Threshold |
|------|---------------|-----------------|
| mini (< 8 zones) | 8 | 4 |
| standard (8+ zones) | 16 | 8 |
| standard + complex data | 20 | 10 |

## ONE-SHOT Flow

For quick screens when no spec exists:

```
1. Live scan component inventory
2. Quick compose: propose 1-2 layout options inline
3. User picks → capture compose-block
4. Implement directly (no spec file generated)
5. Quality gates (lint → typecheck)
6. Self-review (spec fidelity)
7. Output summary
```

ONE-SHOT skips spec-review and formal progress tracking. Use for simple screens only.

## LOOP Flow (Ship Loop)

Core iteration algorithm for spec-driven work:

```
iteration = 0

WHILE iteration < max_iterations:
  iteration++

  # 1. SELECT
  Pick next unchecked scope item from spec Progress table
  Read the zone mapping, component, and data source for this item

  # 2. IMPLEMENT
  Implement the scope item:
    - Use components from conventions.md paths (ALL roots if multi-root)
    - Match import patterns from conventions.md
    - Match naming conventions
    - Use existing tokens (never hardcode values)
    - Follow layout architecture from spec
    - Reuse existing components — NEVER reinvent

  # 3. VALIDATE
  Quick quality pass:
    - Lint changed files (if linter detected)
    - Typecheck changed files (if TS detected)

  Mark spec item [x] if passing
  Update spec Progress table

  # 4. REVIEW (every 2-3 items)
  Self-check against spec:
    - Does implementation match wireframe layout?
    - Are all zone components connected correctly?
    - Does data flow match spec's data flow section?
    - Are page states handled per spec?
    - Is responsive behavior implemented per spec?
    - No component reinvented (core guarantee)?

  Collect issues: BLOCKING vs WARN

  # 5. FIX
  Fix all BLOCKING issues
  Log WARN issues in spec Notes section

  # 6. EXIT CHECK
  all_scope_complete = all Progress items checked [x]
  quality_clean = lint + typecheck pass (if available)
  no_blockers = no BLOCKING review issues
  spec_fidelity = implementation matches spec wireframe and data flow

  IF all_scope_complete AND quality_clean AND no_blockers AND spec_fidelity:
    EXIT → clean (suggest /design-screen review)

  # 7. STUCK DETECTION
  progress = items_completed / total_items
  IF iteration >= stuck_threshold AND progress < 0.5:
    ESCALATE (see below)
```

## Quality Gates (Per Iteration)

**Quick pass** (after each scope item):
- Lint changed files
- Typecheck changed files

**Full pass** (before exit):
- Lint changed files
- Typecheck full project
- Build full project (if build command detected)
- Spec fidelity: implementation matches wireframe + data flow + page states

Auto-detect tooling from project files. If no linter/TS detected, skip those gates — never block on missing tooling.

## Stuck Escalation

Three levels, triggered by lack of progress:

| Level | Trigger | Action |
|-------|---------|--------|
| Warning | At stuck_threshold, progress < 50% | Log warning, continue |
| Pause | 2 iterations after warning, no progress | Present options to user |
| Hard stop | At max_iterations | Force exit with summary |

**Pause options:**
1. Continue with different approach
2. Reduce scope (cut items)
3. Defer to next session
4. Drop and rethink

## Bug Encounter Protocol

When a bug is found during implementation (component doesn't work as expected, API mismatch, etc.):

```
1. Log bug in spec under "## Encountered Bugs"
   Format: BUG-{N}: {summary} | Status: Investigating/Fixed/Deferred
2. Classify: blocks current scope item? (Y/N)
3. If blocking:
   - Is it a component issue? → Suggest /component fix or manual fix
   - Is it a data issue? → Note in spec, adjust data flow
   - Is it a layout issue? → Adjust wireframe in spec
4. If non-blocking: defer, continue with scope
5. All bugs must be Fixed or Deferred before exit
```

## Component Gap Protocol

When a missing component is encountered during implementation:

```
Component gap detected (spec said "gap" or discovered during implementation)
  │
  ├─ Propose to user:
  │   1. Create via /component spec {name} → pause ship, create, resume
  │   2. Adapt existing component → extend props/slots inline
  │   3. Use alternative existing component → swap in spec
  │   4. Stub it (document in spec Notes) → proceed with placeholder
  │
  └─ User decides → action taken → resume ship loop
```

**Resume after /component:** When component creation completes, ship detects RESUMING state and continues from last checkpoint.

## Risk Flag System

After each implementation step, assess risk:

```
For each changed file:
  Modifying shared layout/shell?    → HIGH (affects all screens)
  Changing component API?           → HIGH (breaks other consumers)
  Adding new dependency?            → MEDIUM
  Modifying responsive behavior?    → MEDIUM
  Adding new zone content?          → LOW
  Tests/docs only?                  → LOW

Highest risk across all changes = overall risk
```

| Risk | Action |
|------|--------|
| LOW | Continue autonomously |
| MEDIUM | Note in spec, mention in self-review |
| HIGH | **PAUSE.** Present to user: what changed, what could go wrong. User approves before continuing. |

## Review Integration

Self-review runs automatically during ship loop (every 2-3 items). Checks:

**Core (always):** Spec fidelity, Component reuse, Token usage, Import patterns
**Conditional:** Page states (if data-driven), Responsive (if breakpoints in spec), Accessibility (landmarks, headings)

See `actions/review.md` for the full compliance check (run post-ship via `/design-screen review`).

## Parallel Execution

When scope items are independent (no shared files), implement in parallel:

```
Check: Do scope items share files or zones?
  NO shared files → parallelizable (implement simultaneously)
  YES shared files → sequential (implement in order)

Parallel rules:
  - Each parallel item gets its own quick pass
  - Merge results before running full pass
  - If parallel items conflict → resolve, re-run quick pass
```

## Progress Tracking

Update spec after each iteration:

```markdown
## Progress

| # | Scope Item | Status | Iteration |
|---|-----------|--------|-----------|
| 1 | PageHeader zone | [x] Complete | 1 |
| 2 | Sidebar navigation | [x] Complete | 2 |
| 3 | Content area + FormSection | [~] In progress | 3 |
| 4 | Page states (loading/empty) | [ ] Pending | — |
```

## Spec Mutation Protocol

When user requests scope changes during the ship loop:

```
User: "also add X" / "change Y" / "remove Z"
  │
  ├─ 1. PAUSE current implementation
  ├─ 2. UPDATE SPEC FILE:
  │     - Add/modify/remove scope items in Progress table
  │     - Update zones if layout changes
  │     - Update ACs if behavior changes
  │     - Update component coverage if new gaps
  │     - Re-run validation rules from spec template
  ├─ 3. UPDATE TASKS: create new, delete obsolete
  ├─ 4. RE-ASSESS: does change affect tier, iteration limit, or risk?
  └─ 5. RESUME ship loop with updated scope
```

The spec is the **SINGLE SOURCE OF TRUTH**. It must reflect reality at all times.

## Intent Auto-Detection

No flags needed. The agent detects intent from natural language:

| User Says | Agent Does |
|-----------|-----------|
| "skip review" | Skip self-review step (log reason) |
| "just the header for now" | Reduce scope to specified items |
| "resume" | Detect RESUMING state, continue from last checkpoint |
| "different approach" | Re-attempt current item with alternative implementation |

Always document any skipped gates in output.

## Output

Follow `references/templates/outputs/ship-output.md`:
- During loop: iteration progress template
- On exit: clean / partial / stuck / hard stop template

Exit states:

| State | Condition |
|-------|-----------|
| Clean | All scope done, ACs pass, gates green, no blockers |
| Partial | Scope done but ACs failing or quality gate issues |
| Stuck | Hit stuck threshold, user chose to stop |
| Hard stop | Hit max iterations |

## Rules

- **Spec is source of truth** — never implement something not in the spec
- **Progress is tracked in spec** — update after every iteration
- **Resume is seamless** — detect state and continue from last checkpoint
- **Stuck detection is automatic** — never loop forever
- **Convention compliance** — use project's naming, tokens, patterns (never invent)
- **Never hardcode** — values come from tokens, components come from inventory
- **Zero reinvention** — if a component exists, use it. This is the core guarantee.

## Never

- Never runs `git push`
- Never runs deploy commands
- Never makes production changes
- Never runs destructive git commands
