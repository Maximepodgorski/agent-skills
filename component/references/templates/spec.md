# Component Spec Template

> **Agent:** Use this template when generating output for the `structure` action. Fill every section that applies. Omit sections that don't apply to this component (e.g., Expose for stateless components, Slots if framework doesn't support them, Token Mapping if no Figma link provided). Never leave empty tables — omit the section entirely.

Output file: `ds/specs/active/{component-name}-spec.md`

---

```markdown
# {ComponentName} — Spec

## User Story

As a {user/developer}, I want {goal} so that {benefit}.

## Component Tree

<!-- ASCII diagram of the component hierarchy -->
<!-- Show parent → children → slots relationship -->
<!-- Use box-drawing characters -->

┌─────────────────────────────┐
│ {ComponentName}             │
├── {SubComponent}            │
│   └── slot ({name})         │
├── {SubComponent}            │
└─────────────────────────────┘

**Atomic level:** {atom | molecule | organism}
**Pattern:** {single component | compound component}

## File Structure

<!-- Match the project's existing component structure pattern -->

{ComponentName}/
├── {ComponentName}.{ext}
├── {SubComponent}.{ext}       <!-- if compound -->
├── {ComponentName}.test.{ext}
├── doc.md
└── index.{ext}

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

<!-- Map Figma values (if available) to project tokens -->

| Figma Value | Project Token | Category | Status |
|-------------|---------------|----------|--------|
| {#hex}      | {--token-name} | Color   | MAPPED / MISSING |
| {16px}      | {--spacing-4}  | Spacing | MAPPED / MISSING |

**Missing tokens:** {list tokens that need to be created — requires user approval}

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

<!-- Architectural choices with rationale -->

| Decision | Rationale |
|----------|-----------|
|          |           |

## Blockers

<!-- Any OPEN blocker = NO SHIP. The dev action refuses to start if blockers are open. -->

| Blocker | Type | Status | Owner |
|---------|------|--------|-------|
|         |      | OPEN / RESOLVED |       |

**Types:** `Token` (value without matching token) | `Dependency` (required sub-component missing) | `Context` (missing info/Figma) | `Decision` (unresolved architectural choice) | `Accessibility` (unclear a11y pattern) | `Design` (Figma incomplete — missing states, inconsistent variants)

**Rule:** All blockers must be RESOLVED before running `/component dev`.

## Recommendations

| Priority | Recommendation | Rationale |
|----------|---------------|-----------|
|          |               |           |

**Priorities:** `Must` (required for ship) | `Should` (improves quality) | `Could` (nice to have)

## Notes

<!-- Empty at creation. Filled during dev. -->
```
