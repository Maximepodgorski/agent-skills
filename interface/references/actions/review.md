# Review Action

> **Agent:** Load this file when `review` triggers. Also load all files in `references/principles/`.

Post-ship quality check. Verifies the implemented interface against the spec, project conventions, and interface principles.

**Triggers:** "review", "check interface", "compliance check"

---

## Input Detection

```
Active spec in ds/interfaces/active/?
  YES → Review against spec (spec-driven)
  NO  → Review against conventions only (standalone)

Shipped interface files exist?
  YES → Continue
  NO  → Error: "No shipped interface found. Run /interface ship first."
```

## Step 1: Load Context

1. **Load spec** (if exists) — layout architecture, zones, coverage, ACs
2. **Load conventions** — ds/conventions.md
3. **Load shipped files** — read all files created/modified during ship
4. **Load principles** — all 4 principle files

## Step 2: Run Compliance Checks

### Check 1: Reuse Compliance (composition.md)

```
[ ] Every spec zone maps to an actual component in code
[ ] No component was reinvented (check against conventions.md component paths)
[ ] Coverage % in implementation matches spec coverage %
[ ] Missing components flagged in spec are handled (created via /component, adapted, or alternative used)
[ ] No orphan components (components created but not used in any zone)
```

### Check 2: Layout Consistency (layout-patterns.md)

```
[ ] Implementation matches spec wireframe structure
[ ] Zones are in correct hierarchy (shell → layout → content)
[ ] Shell components (nav, sidebar) reuse existing app layout
[ ] Grid/flex structure matches spec's zone arrangement
[ ] Spacing between zones uses tokens (not hardcoded)
```

### Check 3: Component Usage (composition.md)

```
[ ] Components receive correct props per spec zone mapping
[ ] Data flow matches spec (correct data sources connected)
[ ] Component composition follows project patterns (slots, props, events)
[ ] No prop drilling where context/state management exists
```

### Check 4: Page States (page-states.md)

```
[ ] Loading state implemented for data-driven zones
[ ] Empty state implemented with CTA
[ ] Error state implemented with recovery action
[ ] Skeleton matches success layout (no layout shift)
[ ] Per-zone loading for independent data zones
```

**Skip if:** Screen is stateless (spec marked as no dynamic data).

### Check 5: Responsive (responsive.md)

```
[ ] Layout collapses correctly at each breakpoint
[ ] Sidebar collapse pattern matches spec
[ ] Grid reflow follows spec's responsive table
[ ] Touch targets >= 44x44px on mobile
[ ] No horizontal scroll at any breakpoint
[ ] All actions accessible on mobile (overflow menu if needed)
```

### Check 6: Accessibility

**Static checks (from source code analysis):**

```
[ ] Landmark elements used (nav, main, aside, header, footer)
    → Grep for <nav>, <main>, <aside>, <header>, <footer> in built files
[ ] Heading hierarchy is sequential (h1 → h2 → h3, no gaps)
    → Grep for <h1>...<h6>, verify order
[ ] Interactive elements have accessible names
    → Grep for <button>, <a>, <input> — each must have text content, aria-label, or aria-labelledby
[ ] ARIA attributes on dynamic content (live regions, expanded/collapsed)
    → Grep for aria-live, aria-expanded, aria-hidden, role= in dynamic sections
[ ] Images have alt text
    → Grep for <img — verify alt= attribute present
```

**Inference checks (can't fully verify from source, but flag likely issues):**

```
[ ] Focus order likely follows visual order (DOM order matches layout)
    → Check if CSS reordering (order:, flex-direction: row-reverse) could break focus order
[ ] Color contrast likely meets WCAG 2.1 AA
    → Flag any hardcoded color values not from tokens (tokens assumed to be pre-validated)
    → If custom colors found → WARNING: "Verify contrast ratio manually"
[ ] Keyboard navigation likely works
    → Check that click handlers on non-interactive elements have keyboard equivalents
    → Flag div/span with onClick but no tabIndex/onKeyDown
```

**Note:** Full a11y validation requires runtime testing (screen reader, browser devtools). These source-level checks catch the most common issues.

## Step 3: Generate Report

### Severity Levels

| Level | Meaning | Example |
|-------|---------|---------|
| CRITICAL | Must fix — breaks core interface quality | Reinvented existing component, missing loading state, layout doesn't match spec |
| WARNING | Should fix — impacts quality | Inconsistent naming, suboptimal responsive collapse, hardcoded spacing |

### Verdict Rules

```
CRITICAL count = 0 AND WARNING count <= 3  → COMPLIANT
CRITICAL count = 0 AND WARNING count > 3   → NEEDS WORK
CRITICAL count > 0                         → NON-COMPLIANT
```

**N/A rule:** Mark a check N/A when genuinely not applicable (e.g., Page States N/A for static pages, Responsive N/A for desktop-only tool). Never N/A to skip a hard check.

## Output

Follow `references/templates/outputs/review-output.md`.

## Rules

- **Load ALL principles before reviewing** — never skip a check (use N/A only if genuinely not applicable)
- **Every finding includes:** principle violated, location (file:line), concrete fix
- **Reinvented components = CRITICAL** — the core guarantee is zero reinvention
- **Spec fidelity = CRITICAL** — implementation must match spec structure
- **Hardcoded values = WARNING** — token usage is important but may have edge cases
- **Don't review business logic** — this is interface compliance only
- **Compare against spec first** — standalone review (no spec) is less thorough but still valid
- **"build" = "ship"** — if user says "build" or "ship", both trigger the ship action
