# Composition — Screen-Level Assembly Rules

> How to assemble a full screen from existing components. Different from component-level composition (Atomic Design) — this is about arranging organisms and templates into a cohesive page.

## Screen Anatomy

Every screen has 3 layers:

```
┌─────────────────────────────────────┐
│  SHELL (fixed)                      │  ← Navigation, sidebars, toolbars
│  ┌─────────────────────────────────┐│
│  │  LAYOUT (structural)            ││  ← Grid, columns, sections
│  │  ┌─────────────────────────────┐││
│  │  │  CONTENT (dynamic)          │││  ← Data, forms, lists, cards
│  │  └─────────────────────────────┘││
│  └─────────────────────────────────┘│
└─────────────────────────────────────┘
```

| Layer | Responsibility | Changes? | Examples |
|-------|---------------|----------|----------|
| **Shell** | App-level navigation and chrome | Rarely (per-app) | SideNav, TopNav, BottomNav |
| **Layout** | Content arrangement and grid | Per screen type | 2-column, grid, single column |
| **Content** | Actual UI within layout zones | Per screen instance | DataTable, FormSection, StatCards |

## Assembly Rules

### Rule 1: Shell First

The shell defines the app's navigation context. Every screen inherits it.

```
GIVEN a project with existing navigation
WHEN composing a new screen
THEN reuse the existing shell (don't recreate navigation)

Detection:
  - Scan for layout components (AppLayout, DashboardLayout, etc.)
  - Scan for route wrappers or layout files
  - If found → compose INSIDE the existing shell
  - If not found → note shell as missing, propose it
```

### Rule 2: Layout Maps to Content Zones

Each layout pattern defines named zones. Content fills zones.

```
Dashboard Grid layout defines:
  zone:header   → PageHeader component
  zone:stats    → StatCard[] (horizontal row)
  zone:primary  → Chart or DataTable
  zone:secondary → ActivityFeed or SummaryList

The agent MUST:
  1. Name every zone in the wireframe
  2. Map each zone to a specific component from inventory
  3. Flag zones where no existing component fits
```

### Rule 3: Component Slot Matching

When placing a component in a zone, verify it fits:

```
For each zone → component mapping:
  1. Does the component accept the expected data shape?
  2. Does the component support the required variants (size, density)?
  3. Does the component handle the zone's responsive behavior?

If mismatch:
  → Propose wrapper or adapter
  → Or flag as gap (needs new component / component extension)
```

### Rule 4: Hierarchy Follows Data

Screen structure should mirror data relationships, not visual arrangement.

```
GOOD (data-driven):
  SettingsPage
  ├── UserSection (user data)
  │   ├── AvatarUpload
  │   └── ProfileForm
  ├── PreferencesSection (prefs data)
  │   ├── ThemeToggle
  │   └── NotificationSettings
  └── DangerSection (destructive actions)
      └── DeleteAccount

BAD (visual-driven):
  SettingsPage
  ├── LeftColumn
  │   ├── Avatar
  │   └── Toggle
  └── RightColumn
      ├── Form
      └── Button
```

### Rule 5: Component Coverage Calculation

Coverage measures how much of the screen uses existing components.

```
coverage = (zones_with_existing_components / total_zones) × 100

Classification:
  100%     → Full reuse, no new components needed
  90-99%   → Near-complete, minor gaps (wrapper, variant)
  70-89%   → Good reuse, some new components needed
  50-69%   → Significant gaps, consider building components first
  <50%     → Component library doesn't cover this screen type well
```

## Compose-Block Format

The compose-block is the internal data structure created during Phase 1 (compose) and consumed by Phase 2 (spec).

```markdown
## Compose Block

**Pattern:** {pattern name from layout-patterns.md}
**Coverage:** {N}%

### Zones

| Zone | Component | Source | Props/Config | Status |
|------|-----------|--------|-------------|--------|
| header | PageHeader | existing | title="Settings" | MAPPED |
| nav | TabBar | existing | items=[...] | MAPPED |
| content | FormSection | existing | — | MAPPED |
| actions | ButtonGroup | existing | align="right" | MAPPED |
| sidebar | — | — | — | GAP |

### Component Tree

{ASCII tree showing nesting}

### Missing Components

| Component | Needed For | Proposed Solution |
|-----------|-----------|-------------------|
| {name} | {zone} | create / adapt {existing} / use alternative {alt} |
```

## Do / Don't

| Do | Don't |
|----|-------|
| Map every zone to a named component | Leave zones as "content area" (too vague) |
| Reuse existing layouts from the project | Reinvent navigation structure per screen |
| Show coverage % honestly | Inflate coverage by counting wrappers |
| Flag gaps explicitly with proposed solutions | Silently create placeholder components |
| Match component data expectations to actual APIs | Assume components accept any data shape |
