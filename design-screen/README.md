# Design Screen — Compose UI from Existing Components

**Proposes ASCII wireframe layouts mapped to your real components. Specs the chosen one. Ships it.**

Your project has 50 components. You need a settings page. Instead of starting from scratch, this skill scans your component library, proposes layout options with exact coverage percentages, and specs the chosen layout with production-level rigor.

**Core guarantee: zero component reinvented.** Optional Figma draft step validates component selection visually before coding.

---

## Quick Start

```bash
# First time — scan your project (once)
/design-screen init

# Compose + spec a screen
/design-screen spec "settings page"

# Optional — validate in Figma before coding
/design-screen craft

# Ship it
/design-screen ship
```

That's the daily workflow: **spec → ship**. Add **craft** for Figma validation. Init runs once.

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
     ▼  /design-screen craft (optional)
  Figma placement draft → visual validation
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
| **`craft`** | Before ship (optional) | Figma component placement draft | Figma page |
| **`ship`** | After spec | Ship loop on spec items | Implemented screen |
| **`review`** | After ship | Compliance check | Verdict report |

---

## Daily Use

For most screens, you need two commands:

```bash
/design-screen spec "dashboard"    # Propose + spec
/design-screen ship                # Implement
```

For Figma validation before coding:

```bash
/design-screen spec "dashboard"
/design-screen craft                # Figma placement draft → verify components
/design-screen ship
```

For complex or critical screens, add all quality gates:

```bash
/design-screen spec "admin dashboard"
/design-screen spec-review          # 3-6 expert perspectives review the spec
/design-screen craft                # Figma placement draft
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

## Craft — Figma Component Draft

**For designers and non-developers.** After a screen is spec'd, `craft` generates a visual draft in Figma using your real design system components. This lets you verify the component selection and layout structure before any code is written.

### What you need

| Requirement | How to check |
|-------------|-------------|
| Figma account with **Full seat** (not Dev/Viewer) | You can create and edit frames in Figma |
| Figma MCP connected to your agent | Run `/mcp` in Claude Code — should show "figma" as connected |
| A Figma file with a published design system library | Your team's DS file with components (Button, Input, etc.) |
| An active screen spec | You already ran `/design-screen spec "..."` |

### Step-by-step

**Step 1 — Run the command**

```bash
/design-screen craft
```

The agent loads your screen spec and counts the components to map.

**Step 2 — Budget check**

The agent tells you how many Figma API calls it needs:

```
Craft will use 8 Figma MCP read calls (7 component lookups + 1 screenshot).
Proceed with discovery?
```

Say **yes** to continue. If you're on a Starter Figma plan (6 reads/month), the agent warns you.

**Step 3 — Component mapping**

The agent searches your Figma design system for each component in the spec and shows a mapping table:

```
| Spec Component | Figma Component | Confidence |
|----------------|-----------------|------------|
| PageHeader     | PageHeader      | name-match (80%) |
| FormSection    | Form Section    | fuzzy-match (60%) |
| CustomWidget   | —               | unmapped (0%) |
```

Review the table. You can:
- **Confirm** to proceed as-is
- **Override** unmapped components by telling the agent which Figma component to use
- **Abort** if too many components are missing

**Step 4 — Figma page built**

The agent creates a new Figma page named `{screen name} — craft` and places component instances zone by zone. You'll see progress in chat:

```
Zone: Header (1/4) — 2 components placed
Zone: Sidebar (2/4) — 3 components placed
Zone: Content (3/4) — 5 components placed
Zone: Footer (4/4) — 1 component placed
```

**Step 5 — Visual verification**

The agent takes a screenshot of the result and shows it to you. Open the Figma page to:

- [ ] Verify the right components were selected for each zone
- [ ] Check the zone structure matches what you expected
- [ ] Adjust variants if defaults don't match your intent (e.g., wrong button size)
- [ ] Note any changes to feed back into the spec before coding

**Step 6 — Next steps**

After verifying in Figma, either:
- Tell the dev team to run `/design-screen ship` to implement in code
- Ask the agent to adjust the spec based on what you saw
- Re-run `/design-screen craft` with mapping overrides

### What craft is NOT

- It is **not a design-ready mockup** — it's a structural draft for validation
- It does **not handle responsive layouts** — single breakpoint only (v1)
- Components use **default variants** — you may need to manually switch variants in Figma
- Spacing is **approximate** — auto-layout gives a reasonable structure, not pixel-perfect design

### Common issues

| Problem | Solution |
|---------|----------|
| "Figma MCP not available" | Run `/mcp` to connect Figma |
| "Write access denied" | You need a Full seat in Figma, not a Dev seat |
| Many components show "unmapped" | Your Figma DS uses different names than the code — provide overrides or run `/design-screen init` first |
| Layout looks wrong in Figma | This is expected for complex layouts — craft gives structure, not final design |

---

## Examples

### New settings page

```bash
/design-screen spec "workspace settings"
# → See 3 layout options (Sidebar+Tabs, Single Column Form, Master-Detail)
# → Pick "A" → Spec generated at ds/screens/active/2026-03-24-workspace-settings.md

/design-screen craft
# → Maps spec components to Figma DS → placement draft in Figma
# → Verify component selection visually before coding

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
- **Figma MCP** — optional for spec (design-driven input), **required for craft** (Figma draft output)
- **Figma Full seat** — required for craft only (Dev/Viewer seats can't write to Figma)

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
    │   ├── craft.md                   ← Figma component placement draft
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
            ├── craft-output.md
            ├── ship-output.md
            └── review-output.md
```

**4 principles** x **6 actions** x **8 templates**. Everything is plain Markdown.

[Full documentation →](./SKILL.md)
