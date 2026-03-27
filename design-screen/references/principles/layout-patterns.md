# Layout Patterns — SaaS & Mobile Pattern Catalog

> Standard layout patterns for screen composition. The agent uses this catalog to propose wireframe options that match common, proven structures.

## SaaS Patterns

### Sidebar + Content

```
┌──────┬─────────────────────────────┐
│ Nav  │  Header / Breadcrumbs       │
│      ├─────────────────────────────┤
│      │                             │
│      │  Content area               │
│      │  (scrollable)               │
│      │                             │
│      │                             │
└──────┴─────────────────────────────┘
```

**When:** Settings, admin panels, documentation, dashboards with navigation.
**Components:** SideNav (fixed), Header, Breadcrumb, ContentArea (scrollable).
**Variant:** Collapsible sidebar — sidebar collapses to icon-only on narrow viewports.

### Top Nav + Content

```
┌─────────────────────────────────────┐
│  TopNav (logo + links + actions)    │
├─────────────────────────────────────┤
│                                     │
│  Content area                       │
│  (full width, scrollable)           │
│                                     │
└─────────────────────────────────────┘
```

**When:** Marketing pages, landing pages, simple apps with few nav items.
**Components:** TopNav, ContentArea.
**Variant:** Sticky nav — TopNav stays fixed on scroll.

### Dashboard Grid

```
┌─────────────────────────────────────┐
│  Header (title + filters + actions) │
├──────────┬──────────┬───────────────┤
│  Card 1  │  Card 2  │  Card 3      │
│  (stat)  │  (stat)  │  (stat)      │
├──────────┴──────────┴───────────────┤
│  Primary content (chart / table)    │
│                                     │
├──────────────────┬──────────────────┤
│  Secondary       │  Secondary       │
│  (list/feed)     │  (activity)      │
└──────────────────┴──────────────────┘
```

**When:** Dashboards, analytics pages, overview screens.
**Components:** PageHeader, StatCard, Chart/DataTable, ActivityFeed.
**Key rule:** Stat cards = summary, primary area = detail, secondary = supporting context.

### Master-Detail (List + Detail)

```
┌─────────────────┬───────────────────┐
│  List            │  Detail           │
│  ┌─────────┐   │  ┌─────────────┐  │
│  │ Item 1  │◀──│  │  Title      │  │
│  ├─────────┤   │  │  Content    │  │
│  │ Item 2  │   │  │  Actions    │  │
│  ├─────────┤   │  └─────────────┘  │
│  │ Item 3  │   │                   │
│  └─────────┘   │                   │
└─────────────────┴───────────────────┘
```

**When:** Email clients, chat apps, CRM, issue trackers.
**Components:** ListView, ListItem, DetailPanel, DetailHeader.
**Key rule:** List panel fixed width, detail panel fills remaining space.

### Form Page (Single Column)

```
┌─────────────────────────────────────┐
│  Header (title + description)       │
├─────────────────────────────────────┤
│  ┌─ Section 1 ───────────────────┐  │
│  │  FormField                    │  │
│  │  FormField                    │  │
│  └───────────────────────────────┘  │
│  ┌─ Section 2 ───────────────────┐  │
│  │  FormField                    │  │
│  │  FormField                    │  │
│  └───────────────────────────────┘  │
│  ┌─ Actions ─────────────────────┐  │
│  │  [Cancel]          [Save]     │  │
│  └───────────────────────────────┘  │
└─────────────────────────────────────┘
```

**When:** Settings, profile edit, create forms.
**Components:** FormSection, FormField, Button, PageHeader.
**Key rule:** Max-width container (600-800px), centered. Sections group related fields.

### Tabbed Content

```
┌─────────────────────────────────────┐
│  Header                             │
├─────────────────────────────────────┤
│  [Tab 1] [Tab 2] [Tab 3]           │
├─────────────────────────────────────┤
│                                     │
│  Tab content (varies)               │
│                                     │
└─────────────────────────────────────┘
```

**When:** Settings with categories, multi-section profile, feature comparison.
**Components:** TabBar, TabItem, TabPanel, PageHeader.
**Variant:** Sidebar tabs (vertical) for settings with many sections.

### Data Table Page

```
┌─────────────────────────────────────┐
│  Header (title + [+ New])           │
├─────────────────────────────────────┤
│  Filters (search + dropdowns)       │
├─────────────────────────────────────┤
│  Table                              │
│  ┌────┬─────────┬────────┬────────┐ │
│  │ □  │ Name    │ Status │ Action │ │
│  ├────┼─────────┼────────┼────────┤ │
│  │ □  │ Row 1   │ Active │  ...   │ │
│  │ □  │ Row 2   │ Draft  │  ...   │ │
│  └────┴─────────┴────────┴────────┘ │
├─────────────────────────────────────┤
│  Pagination                         │
└─────────────────────────────────────┘
```

**When:** User management, product lists, log views, any CRUD table.
**Components:** PageHeader, FilterBar, DataTable, Pagination, Checkbox.
**Key rule:** Always include empty state, loading state, and bulk action bar.

## Mobile Patterns

### Stack (Scroll)

```
┌─────────────────┐
│  StatusBar       │
├─────────────────┤
│  Header          │
├─────────────────┤
│                  │
│  Content         │
│  (scroll)        │
│                  │
├─────────────────┤
│  BottomNav       │
└─────────────────┘
```

**When:** Most mobile screens — feeds, lists, settings.
**Components:** Header, ContentArea, BottomNav.

### Tab Bar Navigation

```
┌─────────────────┐
│  Header          │
├─────────────────┤
│  [Tab1] [Tab2]   │
├─────────────────┤
│                  │
│  Tab content     │
│  (swipeable)     │
│                  │
├─────────────────┤
│  BottomNav       │
└─────────────────┘
```

**When:** Content with categories — explore, inbox, settings sections.
**Components:** Header, TabBar, SwipeableContent, BottomNav.

### Card Stack

```
┌─────────────────┐
│  Header          │
├─────────────────┤
│  ┌─────────────┐ │
│  │ Card 1      │ │
│  └─────────────┘ │
│  ┌─────────────┐ │
│  │ Card 2      │ │
│  └─────────────┘ │
│  ┌─────────────┐ │
│  │ Card 3      │ │
│  └─────────────┘ │
├─────────────────┤
│  BottomNav       │
└─────────────────┘
```

**When:** Dashboard on mobile, social feed, notifications.
**Components:** Header, Card, BottomNav.

### Floating Action

```
┌─────────────────┐
│  Header          │
├─────────────────┤
│                  │
│  Content         │
│                  │
│              [+] │  ← FAB
│                  │
├─────────────────┤
│  BottomNav       │
└─────────────────┘
```

**When:** Primary creation action — new message, new task, new photo.
**Components:** Header, ContentArea, FAB, BottomNav.
**Key rule:** One FAB per screen. Maps to the single most important action.

## Pattern Selection Rules

```
Agent selection logic:

1. DETECT screen type from user description:
   - Keywords: "dashboard", "overview", "analytics" → Dashboard Grid
   - Keywords: "settings", "profile", "config" → Sidebar + Content OR Form Page
   - Keywords: "list", "table", "manage", "CRUD" → Data Table Page
   - Keywords: "detail", "email", "chat", "inbox" → Master-Detail
   - Keywords: "landing", "home", "marketing" → Top Nav + Content
   - Keywords: "mobile", "app" → Use mobile patterns

2. SELECT 2-3 patterns that fit (not always the same 3):
   - Primary match (highest relevance)
   - Alternative match (different approach)
   - Hybrid (mix of 2 patterns)

3. NEVER propose 3 identical patterns with minor tweaks.
   Each option must have a structurally different layout.
```

## Adaptation Rules

- These are reference patterns, not rigid templates
- Combine patterns (e.g., Sidebar + Dashboard Grid inside content area)
- Adapt to project's existing patterns — if the project uses a custom layout, prefer it
- Mobile patterns apply to responsive breakpoints, not just native apps
