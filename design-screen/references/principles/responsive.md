# Responsive — Breakpoint Strategy & Collapse Patterns

> How screens adapt across viewports. The agent uses this to ensure proposed layouts work at all breakpoints, not just desktop.

## Breakpoints

Standard breakpoints (adapt to project's actual values):

| Name | Width | Typical Device | Columns |
|------|-------|----------------|---------|
| **xs** | < 640px | Phone portrait | 1 |
| **sm** | 640-767px | Phone landscape | 1-2 |
| **md** | 768-1023px | Tablet | 2-3 |
| **lg** | 1024-1279px | Small desktop | 3-4 |
| **xl** | >= 1280px | Desktop | 4+ |

**Detection rule:** Always check the project's existing breakpoints before proposing. Use `tailwind.config`, CSS media queries, or SCSS variables.

## Collapse Patterns

How layout elements transform at narrower breakpoints.

### Sidebar Collapse

```
Desktop (lg+):                 Tablet (md):              Mobile (xs-sm):
┌──────┬──────────────┐       ┌───┬──────────────┐       ┌──────────────┐
│ Nav  │ Content      │       │ I │ Content      │       │ [=] Content  │
│      │              │  →    │ c │              │  →    │              │
│      │              │       │ o │              │       │              │
│      │              │       │ n │              │       │              │
└──────┴──────────────┘       └───┴──────────────┘       └──────────────┘
 Full sidebar                  Icon-only sidebar          Hamburger menu
```

**Pattern:** Full → Icon-only → Hidden (drawer/overlay).

### Grid Reflow

```
Desktop (lg+):                    Tablet (md):            Mobile (xs):
┌────┬────┬────┬────┐            ┌────┬────┐             ┌────┐
│ 1  │ 2  │ 3  │ 4  │            │ 1  │ 2  │             │ 1  │
├────┼────┼────┼────┤      →     ├────┼────┤      →      ├────┤
│ 5  │ 6  │ 7  │ 8  │            │ 3  │ 4  │             │ 2  │
└────┴────┴────┴────┘            ├────┼────┤             ├────┤
                                 │ 5  │ 6  │             │ 3  │
                                 └────┴────┘             └────┘
 4 columns                       2 columns               1 column (stack)
```

**Pattern:** Reduce columns at each breakpoint. Content order preserved.

### Master-Detail Collapse

```
Desktop (lg+):                          Mobile (xs-sm):
┌──────────┬─────────────────┐         ┌──────────────────┐
│  List    │  Detail         │         │  List             │  ← Screen 1
│  Item 1  │  Title          │    →    │  Item 1           │
│  Item 2  │  Content        │         │  Item 2           │
│  Item 3  │  Actions        │         │  Item 3           │
└──────────┴─────────────────┘         └──────────────────┘
                                       ┌──────────────────┐
                                       │  ← Back  Detail  │  ← Screen 2
                                       │  Title           │
                                       │  Content         │
                                       └──────────────────┘
 Side by side                           Stacked (navigate between)
```

**Pattern:** Split view → full-screen list + full-screen detail with back navigation.

### Table Collapse

```
Desktop:                               Mobile:
┌────┬─────────┬────────┬────────┐    ┌─────────────────┐
│ □  │ Name    │ Status │ Action │    │ ┌─────────────┐ │
├────┼─────────┼────────┼────────┤    │ │ Name        │ │
│ □  │ Row 1   │ Active │  ...   │    │ │ Status: Act │ │
│ □  │ Row 2   │ Draft  │  ...   │    │ │ [Actions]   │ │
└────┴─────────┴────────┴────────┘    │ └─────────────┘ │
                                      │ ┌─────────────┐ │
                                      │ │ Name        │ │
                                      │ │ Status: Dra │ │
                                      │ │ [Actions]   │ │
                                      │ └─────────────┘ │
                                      └─────────────────┘
 Full table                            Card list (stacked rows)
```

**Pattern:** Table → card list. Each row becomes a card. Priority columns shown, rest hidden or expandable.

### Action Bar Collapse

```
Desktop:                               Mobile:
┌─────────────────────────────────┐   ┌─────────────────┐
│ Title    [Filter] [Export] [New] │   │ Title    [+] [...│]
└─────────────────────────────────┘   └─────────────────┘
 All actions visible                   Primary action + overflow menu
```

**Pattern:** Show all actions → primary action + overflow (...) menu.

## Responsive Spec Integration

When speccing a screen, include responsive behavior for each layout zone:

```markdown
## Responsive Behavior

| Zone | Desktop (lg+) | Tablet (md) | Mobile (xs-sm) |
|------|--------------|-------------|----------------|
| sidebar | Full sidebar | Icon-only | Hamburger drawer |
| stats | 4-column grid | 2-column grid | Stack (1 column) |
| primary | Full table | Full table | Card list |
| actions | All visible | All visible | Primary + overflow |
```

## Rules

- **Mobile-first:** Propose the mobile layout first, then enhance for larger screens
- **Content priority:** On mobile, show the most important content first — hide secondary with expand/overflow
- **Touch targets:** Mobile interactive elements >= 44x44px (WCAG 2.5.5)
- **No horizontal scroll:** Content must fit viewport width at every breakpoint
- **Preserve functionality:** Every action available on desktop must be reachable on mobile (even if behind a menu)
- **Test the extremes:** Verify layout at xs (320px) and xl (1440px+) — middle breakpoints usually handle themselves

## Adaptation

- Detect project's responsive approach (Tailwind responsive, CSS Grid, media queries, container queries)
- Use the project's existing responsive patterns over generic advice
- If no responsive system exists, recommend one that fits the project's stack
