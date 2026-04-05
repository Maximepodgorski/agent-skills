# Craft Output Template

> Chat messages returned during and after `/design-screen craft`. Covers budget check, mapping table, iteration progress, and exit states.

---

## Phase 0: Budget Check

```markdown
## Craft: {screen name}

**Spec:** `ds/screens/active/{filename}.md`
**Zones:** {N} | **Unique components:** {N}
**Figma reads needed:** {N} ({N} lookups + 1 screenshot)

{If reads > 6:}
> Starter plan allows 6 reads/month. Org plan (200/day) recommended.

Proceed with discovery?
```

---

## Phase 1: Mapping Table

```markdown
## Mapping: {screen name}

| Spec Component | Figma Component | Confidence | Key |
|----------------|-----------------|------------|-----|
| {name} | {figma_name} | name-match (80%) | `{key}` |
| {name} | {figma_name} | fuzzy-match (60%) | `{key}` |
| {name} | — | unmapped (0%) | — |

**Mapped:** {N}/{total} ({N}%)
{If unmapped > 0:} **Unmapped:** {list} → will be placed as labeled placeholders

{If unmapped_rate > 50%:}
> High unmapped rate. Options:
> 1. Continue with placeholders
> 2. Run `/design-screen init` with Figma scan
> 3. Abort

Confirm mapping to proceed.
```

---

## Phase 2: Zone Progress (per zone)

```markdown
### Zone: {zone name} ({N}/{total})

**Components placed:** {N}
**Instances created:** {list}
{If skipped components:} **Skipped:** {list} (unmapped → placeholder)
```

---

## Exit: COMPLETE

```markdown
## Craft: {screen name} — Complete

**Figma page:** `{screen-name} — craft`
**Zones built:** {N}/{N}
**Components placed:** {N} instances
**Coverage:** {mapped}/{total} ({N}%)
{If unmapped > 0:} **Placeholders:** {N} (labeled rectangles for unmapped components)

**Known limitations:**
- Placement draft — not design-ready
- Auto-layout zones approximate the spec wireframe structure
- Component variants use defaults (manual variant selection may be needed)
- No responsive variants (single breakpoint)

**Manual verification checklist:**
- [ ] Open Figma page and verify component selection is correct
- [ ] Check that zone structure matches spec wireframe
- [ ] Adjust variant properties where defaults don't match intent
- [ ] Review spacing and sizing (auto-layout may differ from final design)

Next:
  /design-screen ship            (implement in code)
  Edit the spec manually         (adjust before building)
  Re-run /design-screen craft    (rebuild with different mapping)
```

---

## Exit: PARTIAL

```markdown
## Craft: {screen name} — Partial

**Figma page:** `{screen-name} — craft`
**Zones built:** {N}/{total}
{If skipped zones:} **Skipped zones:** {list} (use_figma failures)
**Components placed:** {N} instances

> Screenshot shows potential layout issues. Open Figma to verify manually.

**What happened:**
{List of issues: skipped zones, layout problems, screenshot evaluation}

Next:
  Open Figma and verify manually
  Re-run /design-screen craft    (restart or resume)
  /design-screen ship            (proceed to code anyway)
```

---

## Exit: ABORTED

```markdown
## Craft: {screen name} — Aborted

**Reason:** {user chose to abort / high unmapped rate / budget concern}
{If partial mapping:} **Mapped so far:** {N}/{total}

No Figma page created.

Next:
  /design-screen init            (improve component mapping)
  /design-screen ship            (skip craft, implement directly)
  Re-run /design-screen craft    (try again)
```

---

## Mid-Build: Zone Failure (E4)

```markdown
### Zone: {zone name} — Failed

**Error:** {use_figma error message}
**Retry:** {passed | failed}
**Action:** Skipping zone. Remaining zones will continue.

{zone name} can be manually built in Figma or re-run craft to retry.
```

---

## Mid-Discovery: Rate Limit (E6)

```markdown
## Craft: {screen name} — Rate Limit

**Figma MCP read limit reached.**
**Mapped:** {N}/{total} components ({N}%)
**Remaining:** {list of unmapped component names}

Options:
  1. Continue with partial mapping (unmapped → placeholders)
  2. Wait for rate limit reset and retry
  3. Abort

Choose an option to proceed.
```

---

## Exit: ERROR

```markdown
## Craft: {screen name} — Error

**Error:** {error description}

{E1:} Figma MCP not available. Install: https://github.com/anthropics/claude-code/blob/main/docs/figma.md
{E2:} No active spec. Run `/design-screen spec` first.
{E5:} Write access denied. Requires Full seat in Figma (not Dev seat).

Next:
  {E1:} Install Figma MCP, then re-run `/design-screen craft`
  {E2:} Run `/design-screen spec "{screen name}"` first
  {E5:} Upgrade to Full seat or use a different Figma account
```

---

## Error: No Conventions

```markdown
## Craft: Setup Required

No `ds/conventions.md` found. Running init first...
```

---

## Error: No Spec

```markdown
## Craft: {screen name}

No active spec found in `ds/screens/active/`.

Run `/design-screen spec "{screen name}"` first to create a screen spec.
```
