# Composition — Atomic Design + Compound Patterns

> Based on **Atomic Design** by Brad Frost.

## Atomic Design Levels

Components are classified by complexity. Each level has clear rules for what it contains.

```
┌─────────────────────────────────────────────────────┐
│  PAGES         Full screens, route-level            │
│  ┌─────────────────────────────────────────────┐    │
│  │  TEMPLATES   Layout structure, slot-based   │    │
│  │  ┌─────────────────────────────────────┐    │    │
│  │  │  ORGANISMS  Feature blocks           │    │    │
│  │  │  ┌─────────────────────────────┐    │    │    │
│  │  │  │  MOLECULES  Grouped atoms   │    │    │    │
│  │  │  │  ┌─────────────────────┐    │    │    │    │
│  │  │  │  │  ATOMS  Primitives  │    │    │    │    │
│  │  │  │  └─────────────────────┘    │    │    │    │
│  │  │  └─────────────────────────────┘    │    │    │
│  │  └─────────────────────────────────────┘    │    │
│  └─────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────┘
```

| Level | Contains | Examples | Rule |
|-------|----------|---------|------|
| **Atom** | Single UI element, no children components | Button, Input, Icon, Badge, Avatar | No imports of other DS components |
| **Molecule** | 2-3 atoms grouped with a purpose | SearchField (Input + Button), FormField (Label + Input + Error) | Imports atoms only |
| **Organism** | Complex UI block, may contain molecules + atoms | NavBar, Card, DataTable, Modal | Imports atoms + molecules |
| **Template** | Page layout structure with slots | DashboardLayout, AuthLayout | Defines zones, no business logic |
| **Page** | Route-level, connects to data | SettingsPage, DashboardPage | Business logic lives here |

## Classification Rules

```
How to classify a component:

1. Does it import other DS components?
   NO  → Atom
   YES → Continue

2. How many DS components does it compose?
   2-3 atoms only → Molecule
   Mix of atoms + molecules, or 4+ → Organism

3. Does it define layout zones via slots?
   YES → Template

4. Does it connect to data/routes?
   YES → Page
```

## Do / Don't

| Do | Don't |
|----|-------|
| Atom: `<Button>` renders a button, nothing else | Atom imports another DS component |
| Molecule: `<FormField>` composes `<Label>` + `<Input>` + `<ErrorMessage>` | Molecule contains business logic |
| Organism: `<DataTable>` manages rows, sorting, pagination | Organism fetches data directly |
| Template: `<DashboardLayout>` defines sidebar/main/header slots | Template has hardcoded content |
| Page: `<SettingsPage>` fetches data and passes to organisms | Page contains reusable UI logic |

## Compound Component Pattern

For complex components with multiple interactive sub-parts, use the compound pattern:

```
┌─────────────────────────────┐
│ Select (root — state)       │
├── SelectTrigger (opener)    │
├── SelectContent (dropdown)  │
│   ├── SelectGroup (group)   │
│   │   ├── SelectLabel       │
│   │   └── SelectItem[]      │
│   └── SelectSeparator       │
└─────────────────────────────┘
```

### When to use compound

```
Component has multiple interactive zones?
  YES → Compound pattern
  NO  → Single component

Compound test:
  - Could a consumer need to rearrange the parts? → YES = compound
  - Do parts share state but render separately? → YES = compound
  - Could parts be used independently? → NO = compound (they're coupled)
```

### Compound rules

| Rule | Example |
|------|---------|
| Root component owns shared state | `Select` manages open/close, selected value |
| Sub-components receive state via context/provide-inject | `SelectItem` reads selection state from `Select` |
| Each sub-component has a single responsibility | `SelectTrigger` = opens dropdown, nothing else |
| Sub-components are exported together | `export { Select, SelectTrigger, SelectContent, SelectItem }` |
| Internal communication via context, not props drilling | Framework context mechanism (provide/inject, createContext, setContext), never prop chains |

## Single Responsibility

Every component does ONE thing:

```
GOOD:
  <Tooltip>         → shows contextual info on hover/focus
  <TooltipTrigger>  → wraps the element that triggers tooltip
  <TooltipContent>  → renders the tooltip popup

BAD:
  <TooltipButton>   → button that also shows tooltip (2 responsibilities)
  <SmartCard>       → card that fetches data AND renders UI (2 responsibilities)
```

## Composition over Configuration

Prefer slots/children over props for content:

| Do (composition) | Don't (configuration) |
|---|---|
| `<Card><template #header>Title</template></Card>` | `<Card header="Title" />` |
| `<Alert><Icon name="warning" /> Message</Alert>` | `<Alert icon="warning" message="Message" />` |
| `<Dialog><DialogTitle>...</DialogTitle></Dialog>` | `<Dialog title="..." body="..." footer="..." />` |

**Rule:** If a prop accepts JSX/VNode/HTML string, it should be a slot instead.
