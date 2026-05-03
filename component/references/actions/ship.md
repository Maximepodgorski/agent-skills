# Ship Action

> **Agent:** Load this file when `ship` triggers. Also load all files in `references/principles/` for DS compliance during implementation.

Implement a component task-by-task using the **Implementation Tasks** section of the spec. Each task is a TDD bite-sized loop: write failing test → run → implement → run → commit. Inspired by the writing-plans / executing-plans pattern from superpowers.

---

## Input Detection

```
Active spec in ds/specs/active/{name}-spec.md?
  YES → Check Blockers section
    Any blocker with status OPEN?
      YES → Error: "Cannot start ship. {N} open blocker(s):
             - {blocker 1} ({type})
             - {blocker 2} ({type})
             Resolve blockers first or run /component spec {name} to update the spec."
      NO  → Check Implementation Tasks section
        EXISTS and non-empty? → PLAN mode (ask: subagent or inline?)
        MISSING or empty?     → Re-run spec to generate tasks, or fall back to ONE-SHOT
  NO  → ONE-SHOT mode (create inline mini-plan, implement, validate)
```

## Execution Mode Choice (PLAN mode only)

Before starting the task loop, ask the user:

```
"Spec has {N} Implementation Tasks. Two execution options:

  1. Subagent-Driven (recommended for >5 tasks)
     → I dispatch a fresh subagent per task using superpowers:subagent-driven-development
     → Two-stage review between tasks, isolated context, faster on long plans

  2. Inline (recommended for ≤5 tasks or quick iteration)
     → Execute all tasks in this session with checkpoints every 2-3 tasks
     → Stay in main thread, see every step, easier to course-correct mid-task

Which approach?"

Default if user says "go" / no choice: Inline for ≤5 tasks, Subagent-Driven for >5.
```

### Subagent-Driven path

When user picks subagent-driven:
- Use the `superpowers:subagent-driven-development` skill if available, OR dispatch fresh agents manually with `Agent` tool (one per task).
- Per task: brief the subagent with the task block from the spec verbatim (Files + Steps), require TDD discipline, require commit at end.
- Between tasks: review the diff, run lint/typecheck, mark step boxes [x] in the spec, then dispatch the next.
- On subagent failure or off-spec deviation: STOP, surface to user before continuing.

### Inline path

When user picks inline:
- Execute the task loop below directly in this session.
- Checkpoint every 2-3 tasks: pause for DS compliance pass + give user a chance to course-correct.
- Persist checkbox state to the spec after each step (so the session can be interrupted).

## Phase 0: Setup

### Token Scan (MANDATORY)

Before writing any component code, scan the project for existing design tokens:

```
1. SCAN sources:
   ├── CSS custom properties:  grep --include="*.css" --include="*.scss" "var(--" or "$"
   ├── Tailwind config:        read tailwind.config.* → theme.extend
   ├── Token files:            glob "*.tokens.*", "tokens.json", "tokens.yaml"
   └── CSS-in-JS themes:       grep "theme." in styled/emotion files

2. CATALOG found tokens by category (color, spacing, typography, radius, shadow)

3. DETECT naming pattern (--color-* vs --clr-* vs $color-*, scale, tier structure)
```

### Figma Context (if Figma link in spec)

```
1. Call get_design_context(fileKey, nodeId)
2. Cross-reference Figma values against scanned tokens
3. Any missing token → PAUSE, ask user (see Human-in-the-Loop below)
```

### Human-in-the-Loop: Missing Tokens

```
For each Figma value with no token match:
  PAUSE → Present options to user (closest matches + "create new" + "other")
  WAIT for response → apply choice → continue

Rule: NEVER create a token without explicit user approval.
Rule: NEVER use hardcoded values (hex, px, rem literals).
```

## PLAN Mode — Task Loop Algorithm

The spec's Implementation Tasks section IS the plan. The loop below applies to both Inline (executed in main thread) and Subagent-Driven (executed by dispatched agents, one task each).

### Algorithm

```
tasks = parse Implementation Tasks section from spec
current_task = first task with any unchecked step

WHILE current_task exists:

  # 1. ANNOUNCE
  Output: "Task {N}/{total}: {task subject}"
  Output: "Files: {Create/Modify/Test list}"

  # 2. EXECUTE STEPS in order
  FOR each step in current_task:
    IF step is "Write failing test":
      → write the test code shown in the spec verbatim (or adapt to detected stack if spec used placeholders)
      → mark step [x]

    IF step is "Run test — verify fails":
      → run the exact command from the spec
      → confirm FAIL with the expected message
      → IF passes unexpectedly → STOP, escalate (test is wrong or component already exists)
      → mark step [x]

    IF step is "Implement":
      → write the code shown in the spec verbatim
      → use ONLY project tokens (zero hardcoded values)
      → mark step [x]

    IF step is "Run test — verify passes":
      → run the exact command
      → confirm PASS
      → IF fails → debug, fix, re-run; if blocked after 3 attempts → STOP, escalate
      → mark step [x]

    IF step is "Commit":
      → run the exact git add + git commit commands
      → mark step [x]

  # 3. CHECKPOINT (every 2-3 tasks)
  Quick DS compliance pass against principles:
    - tokens.md: zero hardcoded values?
    - naming.md: BEM / project convention?
    - props-api.md: minimal, smart defaults, strict types?
    - accessibility.md: keyboard, ARIA, focus?
    - composition.md: correct atomic level / pattern?
  Collect: CRITICAL (hardcoded values, a11y) | WARN (naming, structure)
  Fix CRITICAL immediately. Log WARN in spec Notes section.

  # 4. UPDATE SPEC
  Persist step checkboxes [x] back to ds/specs/active/{name}-spec.md
  Update Notes with any deviation from spec

  # 5. NEXT
  current_task = next task with any unchecked step

# 6. EXIT CHECK (when no tasks remain)
all_tasks_done = every step in every task is [x]
zero_hardcoded   = no hex / px / rem literals in component code
tokens_all_project = all tokens exist (none invented without approval)
quality_clean    = lint + typecheck pass on changed files

IF all → suggest /component review for final pass
IF not all → output Partial state with list of remaining steps
```

### Spec Drift Handling

The spec is the source of truth, BUT during ship you may discover:
- A test in the spec is wrong (e.g. tests behavior the spec API doesn't support)
- A code block in the spec has a typo or uses an outdated token name
- A task is missing a step that's clearly needed

**Rule:** Don't silently rewrite the spec. Apply this protocol:
1. Apply the obvious fix in code (correct token name, fix typo)
2. Log the deviation in the spec's `Notes` section: `Task {N} step {M}: {what changed} — {why}`
3. If the deviation is structural (missing step, wrong API) → STOP, ask the user whether to update the spec or proceed inline

### Stuck Detection

```
attempts_on_current_step >= 3 (test won't pass) OR
critical_review_issues_unresolved >= 2 (same violation across tasks):
  ESCALATE to user:
    1. Continue with different approach (describe alternative)
    2. Reduce scope (drop a task, mark spec follow-up)
    3. Defer (commit progress, exit Partial)
```

## ONE-SHOT Mode

Used when no spec exists (quick component, throwaway, exploration).

```
1. Analyze requirements (from Figma or user description)
2. Create inline mini-plan in chat: 2-4 tasks, same TDD shape
3. Run token scan + Figma context
4. Execute the inline plan (same loop as PLAN mode)
5. Self-review against DS principles
6. Suggest: "Run /component spec {name} to formalize this for the audit trail"
```

## Token Naming Adaptation

The skill NEVER imposes its own naming. It adapts to the project:

```
Project uses --bg-* prefix?     → Generate: --bg-tooltip
Project uses $spacing-* SCSS?   → Generate: $spacing-tooltip-offset
Project uses Tailwind utilities? → Generate: class="bg-surface p-4 rounded-md"
Project uses CSS Modules?       → Generate: styles.tooltipContainer
```

## Progress Tracking

Update the spec's Implementation Tasks section after each step. Spec stays the single source of truth for resumability — if the session is interrupted, re-running `/component ship {name}` picks up at the first unchecked step.

## Output

Follow the output template in `references/templates/ship-output.md`. Use the per-task update format during the loop and the appropriate exit template (Complete / Partial / Stuck / Blocked) when done.

## Never

- Never use hardcoded hex / px / rem values — always map to tokens
- Never create tokens without user approval
- Never invent naming patterns — match the project
- Never hardcode framework-specific syntax — detect from codebase
- Never skip the failing-test step — TDD discipline is non-negotiable
- Never silently rewrite the spec — log deviations in Notes
- Never amend a previous commit — create a new commit per step that says "commit"
