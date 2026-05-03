# Component Spec Template

> **Agent:** Use this template when generating output for the `spec` action. Fill every section that applies. Omit sections that don't apply (e.g., Expose for stateless, Slots if framework doesn't support them, Token Mapping if no Figma link). Never leave empty tables — omit the section entirely.
>
> **Inspired by:** writing-plans (superpowers). The Implementation Tasks section turns the spec into an executable plan: bite-sized TDD steps with exact files, code, commands, and commits.

Output file: `ds/specs/active/{component-name}-spec.md`

---

```markdown
# {ComponentName} — Spec

> **For agents:** This spec doubles as an implementation plan. Run `/component ship {name}` to execute the Implementation Tasks section task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** {one sentence — what this component delivers}

**Architecture:** {2-3 sentences — atomic level, compound vs single, key behaviors, how it slots into the DS}

**Tech Stack:** {framework + styling + test runner detected from codebase, e.g. Vue 3 + SCSS + Vitest}

---

## User Story

As a {user/developer}, I want {goal} so that {benefit}.

## Component Tree

<!-- ASCII diagram of the component hierarchy. Box-drawing characters. -->

┌─────────────────────────────┐
│ {ComponentName}             │
├── {SubComponent}            │
│   └── slot ({name})         │
├── {SubComponent}            │
└─────────────────────────────┘

**Atomic level:** {atom | molecule | organism}
**Pattern:** {single component | compound component}

## File Structure

<!-- Match the project's existing component pattern. List exact paths. -->

- Create: `path/to/{ComponentName}/{ComponentName}.{ext}`
- Create: `path/to/{ComponentName}/index.{ext}`
- Create: `path/to/{ComponentName}/{ComponentName}.test.{ext}`
- Create: `path/to/{ComponentName}/doc.md`
- Modify: `path/to/index.{ext}` (barrel export, lines {start-end})

## API

### Props

| Prop | Type | Default | Required | Description |
|------|------|---------|----------|-------------|
|      |      |         |          |             |

### Slots

| Slot | Description | Fallback |
|------|-------------|----------|
|      |             |          |

### Events

| Event | Payload | Description |
|-------|---------|-------------|
|       |         |             |

### Expose

| Method | Description |
|--------|-------------|
|        |             |

## Token Mapping

| Figma Value | Project Token | Category | Status |
|-------------|---------------|----------|--------|
| {#hex}      | {--token}     | Color    | MAPPED / MISSING |
| {16px}      | {--spacing-4} | Spacing  | MAPPED / MISSING |

**Missing tokens:** {list — requires user approval before ship starts}

## Acceptance Criteria

### Must Have

- [ ] AC-1: GIVEN {context} WHEN {action} THEN {result}
- [ ] AC-2: GIVEN {context} WHEN {action} THEN {result}

### Edge Cases

| Case | Expected Behavior |
|------|-------------------|
| Empty content | {behavior} |
| Overflow / long text | {behavior} |
| Disabled state | {behavior} |
| Loading state | {behavior} |
| Error state | {behavior} |
| Rapid interaction | {behavior} |

## Accessibility

- **Keyboard:** {Tab, Enter, Escape, Arrows — as appropriate}
- **Screen reader:** {what is announced, when}
- **ARIA:** {required attributes: role, aria-label, aria-expanded, etc.}
- **Focus:** {focus management, focus trap if overlay}

## Decisions

| Decision | Rationale |
|----------|-----------|
|          |           |

## Blockers

<!-- Any OPEN blocker = NO SHIP. The ship action refuses to start if blockers are open. -->

| Blocker | Type | Status | Owner |
|---------|------|--------|-------|
|         |      | OPEN / RESOLVED |       |

**Types:** `Token` | `Dependency` | `Context` | `Decision` | `Accessibility` | `Design`

**Rule:** All blockers must be RESOLVED before running `/component ship`.

---

## Implementation Tasks

<!--
  Bite-sized TDD tasks. Each task = one logical unit (scaffold, prop, slot, behavior, a11y).
  Each step = 2-5 minutes (write test → run fail → implement → run pass → commit).

  RULES (no placeholders — these are spec failures):
  - No "TBD", "implement later", "add validation", "handle edge cases", "similar to Task N"
  - Every code step shows actual code (not "write the test for X")
  - Every run step shows the exact command + expected output
  - Every commit step shows the exact files staged + commit message
  - Type/method names are consistent across tasks (clearLayers ≠ clearFullLayers)
-->

### Task 1: Scaffold + base render

**Files:**
- Create: `path/to/{ComponentName}/{ComponentName}.{ext}`
- Create: `path/to/{ComponentName}/index.{ext}`
- Test:   `path/to/{ComponentName}/{ComponentName}.test.{ext}`

- [ ] **Step 1: Write the failing test**

  ```{lang}
  // Test that the component renders with required props.
  // Use the project's test framework (detected from codebase).
  ```

- [ ] **Step 2: Run test — verify it fails**

  Run: `{exact command from package.json/project}`
  Expected: FAIL with "{ComponentName} is not defined"

- [ ] **Step 3: Implement the minimal component**

  ```{lang}
  // Minimal scaffold: imports, props interface, root element with class.
  // Use ONLY tokens from the project (no hex, no px literals).
  ```

- [ ] **Step 4: Run test — verify it passes**

  Run: `{exact command}`
  Expected: PASS

- [ ] **Step 5: Commit**

  ```bash
  git add path/to/{ComponentName}/
  git commit -m "feat({component}): scaffold base render"
  ```

### Task 2: {next prop / slot / behavior}

**Files:**
- Modify: `path/to/{ComponentName}.{ext}` (add prop {name})
- Test:   `path/to/{ComponentName}.test.{ext}` (add test for {behavior})

- [ ] **Step 1: Write the failing test**

  ```{lang}
  // Concrete test code for this behavior.
  ```

- [ ] **Step 2: Run test — verify it fails**

  Run: `{exact command --filter to this test}`
  Expected: FAIL with "{specific assertion message}"

- [ ] **Step 3: Implement**

  ```{lang}
  // Concrete diff or block to add.
  ```

- [ ] **Step 4: Run test — verify it passes**

- [ ] **Step 5: Commit**

  ```bash
  git add ...
  git commit -m "feat({component}): {what was added}"
  ```

### Task N: Accessibility + final polish

**Files:**
- Modify: `path/to/{ComponentName}.{ext}` (ARIA, keyboard handlers)
- Test:   `path/to/{ComponentName}.test.{ext}` (keyboard + screen reader)

- [ ] **Step 1: Write keyboard test** ({code})
- [ ] **Step 2: Run — fails**
- [ ] **Step 3: Implement keyboard handlers** ({code})
- [ ] **Step 4: Run — passes**
- [ ] **Step 5: Run lint + typecheck**

  Run: `{lint cmd} && {typecheck cmd}`
  Expected: 0 errors, 0 warnings

- [ ] **Step 6: Commit**

## Notes

<!-- Empty at creation. Filled during ship (decisions taken, deviations from spec, follow-ups). -->
```

---

## Self-Review Checklist (run after generating, before saving)

Run this against the spec yourself — not as a subagent dispatch.

1. **Spec coverage:** Each AC and edge case → is there an Implementation Task that exercises it? List gaps, fix inline.
2. **Placeholder scan:** Search for "TBD", "implement later", "add validation", "handle X", "similar to Task N", `// ...`. Replace with concrete content.
3. **Type consistency:** Do prop names, method names, event names, class names match across the API tables AND the Implementation Tasks code blocks?
4. **Token discipline:** Zero hex / px / rem literals in code blocks. Every value references a project token.
5. **Command accuracy:** Every `Run: {cmd}` is a real command for this project (verified against package.json or project conventions).
6. **Granularity:** Each step = 2-5 minutes. If a step looks like a 30-minute task, split it.

If issues found → fix inline, no need to re-review.
