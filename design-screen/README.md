# Design Screen — Compose UI from Existing Components

**Proposes ASCII wireframe layouts mapped to your real components. Specs the chosen one. Ships it.**

Your project has 50 components. You need a settings page. Instead of starting from scratch, this skill scans your component library, proposes layout options with exact coverage percentages, and specs the chosen layout with production-level rigor.

**Core guarantee: zero component reinvented.**

---

## Quick Start

```bash
# First time — scan your project (once)
/design-screen init

# Compose + spec a screen
/design-screen spec "settings page"

# Ship it
/design-screen ship
```

That's the daily workflow: **spec → ship**. Init runs once.

---

## What It Does

1. **Scans** your codebase for existing components, tokens, and patterns
2. **Proposes** 2-3 ASCII wireframe layout options in chat — each with real component mapping and coverage %
3. **Lets you iterate** — pick an option, ask for variants, or mix options (max 3 rounds)
4. **Specs** the chosen layout with zones, data flow, page states, responsive behavior, and acceptance criteria
5. **Ships** the screen from the spec using a ship loop
6. **Reviews** the result against reuse compliance and screen principles

```
/design-screen spec "settings page"
     │
     ▼
  3 ASCII wireframe options with coverage %
     │
     ▼  (user picks)
  Full screen spec → ds/screens/active/
     │
     ▼  /design-screen ship
  Implemented screen (ship loop with progress tracking)
     │
     ▼  /design-screen review
  Compliance check → COMPLIANT / NEEDS WORK
```

---

## Actions

| Action | When | What | Output |
|--------|------|------|--------|
| **`init`** | First time | Scan project → `ds/conventions.md` | Project DNA file |
| **`spec`** | Every screen | Propose layouts → spec file | `ds/screens/active/` |
| **`spec-review`** | Complex screens | Multi-perspective review | Verdict + findings |
| **`ship`** | After spec | Ship loop on spec items | Implemented screen |
| **`review`** | After ship | Compliance check | Verdict report |

---

## Daily Use

For most screens, you need two commands:

```bash
/design-screen spec "dashboard"    # Propose + spec
/design-screen ship                # Implement
```

For complex or critical screens, add the quality gates:

```bash
/design-screen spec "admin dashboard"
/design-screen spec-review          # 3-6 expert perspectives review the spec
/design-screen ship
/design-screen review               # Post-ship compliance check
```

---

## How the Compose Phase Works

When you run `/design-screen spec "settings page"`, the skill:

1. Loads your project conventions and scans components live
2. Proposes 2-3 structurally different layout options as ASCII wireframes:

```
### Option A — Sidebar + Form

┌──────┬──────────────────────────┐
│ Nav  │  PageHeader              │
│(fixed)├──────────────────────────┤
│      │  TabBar                  │
│      ├──────────────────────────┤
│      │  FormSection (scrollable)│
│      │                          │
│      ├──────────────────────────┤
│      │  ButtonGroup (sticky)    │
└──────┴──────────────────────────┘

Coverage: 92% | Mapped: 6 | Gaps: 1
Components: AppLayout, SideNav, PageHeader, TabBar, FormSection, ButtonGroup
Missing: PreferenceToggle → adapt existing Toggle component
```

3. You pick, mix, or iterate:
   - `"A"` → proceeds to spec
   - `"A but without tabs"` → variant (same round)
   - `"something different"` → new round (max 3)
   - After 3 rounds → describe the exact layout you want

4. The chosen layout becomes the spec — no re-scanning, no re-analysis.

---

## Figma Integration (Optional)

Provide a Figma URL for design-driven composition:

```bash
/design-screen spec figma.com/design/abc123/file?node-id=1-2
/design-screen spec "settings page" figma.com/design/abc123/...
```

The skill extracts the design's layout structure and maps Figma components to your existing library. Coverage % is based on actual design-to-code mapping.

Requires [Figma MCP](https://github.com/anthropics/claude-code/blob/main/docs/figma.md). Falls back to text mode if unavailable.

---

## Init — Project Onboarding

Run once per project. The skill scans your codebase in parallel and saves stable conventions:

```bash
/design-screen init
```

**What it saves** (`ds/conventions.md`):
- Stack and versions
- Component naming and structure patterns
- Token naming and categories
- Import patterns
- Layout patterns already used

**What it doesn't save** (scanned live every time):
- Component inventory (names, props, count)
- Token values
- Route list

If conventions are older than 30 days, the skill warns you to re-init.

---

## Examples

### New settings page

```bash
/design-screen spec "workspace settings"
# → See 3 layout options (Sidebar+Tabs, Single Column Form, Master-Detail)
# → Pick "A" → Spec generated at ds/screens/active/2026-03-24-workspace-settings.md

/design-screen ship
# → Ship loop: implements header, navigation, form sections, page states
# → Progress tracked in spec

/design-screen review
# → COMPLIANT: all zones mapped, page states handled, responsive OK
```

### Dashboard with Figma reference

```bash
/design-screen spec "analytics dashboard" figma.com/design/abc/file?node-id=10-5
# → Figma layout extracted, mapped to components
# → Options include a faithful reproduction + optimized variant
# → Coverage: 85% (2 custom chart components needed)

/design-screen spec-review
# → 4 perspectives: Data Viz Expert, UX, Performance, Skeptic
# → APPROVED WITH NOTES: "Consider lazy loading chart components"

/design-screen ship
/design-screen review
```

---

## Requirements

- **Project with existing components** — the skill composes, it doesn't create from scratch
- **Agent with file read/write + code search** — essential for scanning
- **Figma MCP** — optional, for design-driven flow

Works with **React** / **Vue** / **Svelte** / **Angular** / **Web Components**. The skill detects your stack and adapts.

---

## How It Works Under the Hood

```
design-screen/
├── SKILL.md                          ← Entry point, action router
├── README.md                         ← You're reading this
└── references/
    ├── actions/                      ← What the skill does
    │   ├── init.md                   ← Project onboarding
    │   ├── spec.md                   ← Compose + spec (core action)
    │   ├── spec-review.md            ← Multi-perspective review
    │   ├── ship.md                   ← Ship loop implementation
    │   └── review.md                 ← Post-ship compliance
    ├── principles/                   ← What the skill knows
    │   ├── layout-patterns.md        ← SaaS/Mobile pattern catalog
    │   ├── composition.md            ← Screen assembly rules
    │   ├── page-states.md            ← Loading/empty/error/success
    │   └── responsive.md             ← Breakpoint strategy
    └── templates/                    ← What the skill outputs
        ├── conventions.md            ← Project DNA template
        ├── spec.md                   ← Screen spec template
        └── outputs/                  ← Chat message formats
            ├── init-output.md
            ├── spec-output.md
            ├── spec-review-output.md
            ├── ship-output.md
            └── review-output.md
```

**4 principles** x **5 actions** x **7 templates**. Everything is plain Markdown.

[Full documentation →](./SKILL.md)
