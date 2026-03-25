# Interface Spec Template

> **Agent:** Use this template when generating output for Phase 2 of the `spec` action. Fill sections from the compose-block. Skip sections marked with skip-if conditions. Never leave empty tables — omit the section entirely.

Output file: `ds/interfaces/active/{YYYY-MM-DD}-{screen-name}.md`

---

```markdown
# {Screen Name} — Interface Spec

## Context

**Screen:** {screen name}
**Type:** {dashboard | form | list | detail | settings | landing | auth | mobile | ...}
**Pattern:** {layout pattern from layout-patterns.md}
**Coverage:** {N}%
**Tier:** {mini | standard}
**Created:** {YYYY-MM-DD}

## Component Coverage

| Zone | Component | Source | Status |
|------|-----------|--------|--------|
| {zone} | {ComponentName} | existing / gap | MAPPED / GAP |

**Mapped:** {N} | **Gaps:** {N}

### Missing Components

<!-- Skip if coverage = 100% -->

| Component | Needed For | Proposed Solution |
|-----------|-----------|-------------------|
| {name} | {zone description} | create via /component | adapt {existing} | use {alternative} |

## Layout Architecture

### Wireframe

<!-- ASCII wireframe of the chosen layout -->

{paste the chosen wireframe from Phase 1}

### Component Tree

{ScreenName}
├── {ShellComponent} (existing)
│   ├── {NavComponent} (existing)
│   └── {ContentWrapper}
│       ├── {Zone1Component} (existing)
│       │   └── {ChildComponent} (existing)
│       ├── {Zone2Component} (gap → create)
│       └── {Zone3Component} (existing)

### Zone Mapping

| Zone | Component | Props/Config | Data Source |
|------|-----------|-------------|-------------|
| {zone} | {Component} | {key props} | {API endpoint / store / static} |

## Page States

<!-- Skip if screen is stateless -->

| State | Zones Affected | Behavior |
|-------|---------------|----------|
| Loading | {zones} | {skeleton / spinner} |
| Success | all | Full content |
| Empty | {primary zone} | {description + CTA} |
| Error | {zones that fetch} | {inline error + retry} |
| Partial | {independent zones} | Per-zone loading |

## Data Flow

<!-- Skip if no dynamic data -->

| Data | Source | Consumer Zone | Update Trigger |
|------|--------|--------------|----------------|
| {data name} | {API / store / prop} | {zone} | {on mount / on action / realtime} |

## Responsive Behavior

| Zone | Desktop (lg+) | Tablet (md) | Mobile (xs-sm) |
|------|--------------|-------------|----------------|
| {zone} | {behavior} | {behavior} | {behavior} |

## Screen Journey

### Primary Flow

ACTOR: {user role}
GOAL: {what the user accomplishes}

1. User sees {initial state — loading → success}
2. User {primary interaction}
3. System {response}
4. ...

### Error Flows

<!-- Skip if tier = mini -->

| Error | Trigger | Recovery |
|-------|---------|----------|
| {error} | {cause} | {recovery action} |

## Acceptance Criteria

### Must Have

- [ ] AC-1: GIVEN {context} WHEN {action} THEN {result}
- [ ] AC-2: ...

### Edge Cases

<!-- Skip if tier = mini -->

| Case | Expected Behavior |
|------|-------------------|
| {edge case} | {behavior} |

## Accessibility

- **Landmarks:** {nav, main, aside, etc.}
- **Heading hierarchy:** {h1 → h2 → h3 structure}
- **Keyboard:** {tab order, shortcuts}
- **Focus:** {focus management for dynamic content}

## Decisions

| Decision | Rationale |
|----------|-----------|
| {decision} | {why} |

## Notes

<!-- Empty at creation. Filled during ship. -->

## Progress

| # | Scope Item | Status | Iteration |
|---|-----------|--------|-----------|
| 1 | {item} | pending | — |
```

---

## Validation Rules (BLOCKING)

The agent MUST verify before saving:

| # | Rule | Check |
|---|------|-------|
| 1 | Every zone in wireframe has a component mapping | No zone left as "content area" |
| 2 | Coverage % matches actual mapped/total ratio | Math checks out |
| 3 | Every GAP has a proposed solution | No unaddressed gaps |
| 4 | Component tree uses actual component names from inventory | Not generic placeholders |
| 5 | Data flow entries have valid data_source (static/prop/store/api/route/computed/unknown) | Not blank. "unknown" allowed if flagged in Decisions table |
| 6 | Responsive table covers all layout zones | No zone missing |
| 7 | At least 3 Must Have ACs | Spec is actionable |
| 8 | ACs use GIVEN/WHEN/THEN format | Testable |
| 9 | Page states section exists for data-driven screens | Not skipped without reason |
| 10 | Heading hierarchy starts at h1, no gaps | Accessibility baseline |

## Skip-If Rules

| Section | Skip If |
|---------|---------|
| Missing Components | coverage = 100% |
| Page States | screen is stateless (ALL zones have data_source = "static" or "route") |
| Data Flow | no dynamic data (ALL zones have data_source = "static") |
| Error Flows | tier = mini AND no zone has data_source = "api" |
| Edge Cases | tier = mini AND no zone has data_source = "api" |
| State Machine | not applicable (simple screen) |

**Important:** Mini tier does NOT automatically skip Page States. A mini screen with an API call still needs loading/error states. The skip condition is "stateless", not "mini".

## Tier Detection (Auto)

```
IF coverage >= 90%
   AND total_zones <= 8
   AND no custom data flow (all data from existing stores/APIs)
THEN tier = MINI
ELSE tier = STANDARD
```
