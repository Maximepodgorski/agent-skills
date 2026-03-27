# Craft Action

> **Agent:** Load this file when `craft` triggers. Also load the `figma-use` skill (MANDATORY before any `use_figma` call). Always pass `skillNames: "figma-use"` in every `use_figma` call. Load `references/templates/outputs/craft-output.md` for output format.

Generate a Figma component placement draft from an active screen spec. Maps spec components to Figma DS components, then builds zone-by-zone using `use_figma`.

**Triggers:** "craft", "design in figma", "figma draft", "craft screen"

**Value:** Component selection confirmation + structural layout check before code. This is a placement draft, not a design-ready artifact.

---

## Prerequisites

| Requirement | Check | Error |
|-------------|-------|-------|
| Figma MCP connected | `search_design_system` tool exists | → E1 |
| Active screen spec | `ds/screens/active/` has spec | → E2 |
| `ds/conventions.md` (optional) | Used for canvas width if available | Falls back to 1440px |
| Figma Full seat | First `use_figma` call succeeds | → E5 |
| `figma-use` skill | Loaded before any `use_figma` call | → Load it |

---

## Mode Detection

```
Active spec in ds/screens/active/?
  YES → Load spec, extract zones and components
  NO  → E2

Multiple active specs?
  YES → Ask user which spec to craft
  NO  → Proceed

Figma page named "{screen-name} — craft" already exists?
  YES → Count zones on page vs spec zone count
    zones_on_page < spec_zones → RESUMING (offer: resume or restart)
    zones_on_page = spec_zones → ALREADY_CRAFTED (offer: overwrite or new)
  NO → FRESH
```

---

## Phase 0: Budget Check (BLOCKING)

```
1. Count unique components across all spec zones (deduplicate)
2. reads_needed = N_unique_components + 1 (screenshot)
3. Display: "Craft will use {reads_needed} Figma MCP read calls."
4. If reads_needed > 6: Warn about Starter plan limit (6/month)
5. User confirms → proceed
```

---

## Phase 1: Discovery

### Step 0 — Inspect existing screens (budget optimization)

Before calling `search_design_system`, check if the Figma file already has screens using DS components. Use `use_figma` to walk existing page instances — this gives an authoritative component map without spending read calls. Skip this if the file has no existing screens.

### Step 1 — Discover remaining components

For each unique component name NOT already mapped from inspection:

```
1. Call search_design_system(query={component_name}, fileKey={figma_file_key},
     includeComponents=true, includeStyles=false, includeVariables=false)
2. Filter results to target library (match by libraryName — use the first
     result's libraryName as target if user hasn't specified one)
3. Build mapping: spec_name → { figma_key (.componentKey), figma_name, asset_type, confidence }
4. No match → unmapped (0%)
```

### Step 2 — Discover DS variables (for zone styling)

```
Call search_design_system(query="spacing", fileKey={figma_file_key},
  includeComponents=false, includeStyles=false, includeVariables=true)
```

If DS variables exist for spacing, colors, and radii → use them for zone frames instead of hardcoded values. If no variables found → fall back to defaults (16px padding, 12px gap, light gray background).

### Confidence Levels

| Level | % | Criteria |
|-------|---|----------|
| code-connect | 100 | Explicit Figma↔code mapping via Code Connect |
| name-match | 80 | Exact case-insensitive match |
| fuzzy-match | 60 | Substring match OR edit distance ≤ 3 (case-insensitive) |
| unmapped | 0 | No match found |

### Mapping Confirmation

Display mapping table (see craft-output.md). Then:

```
unmapped_rate > 50% → E3 (warn, offer 3 options)
unmapped_rate ≤ 50% → Ask user to confirm or provide overrides for unmapped items
```

---

## Phase 2: Build

### Step 1 — Create Figma page and screen container

Via `use_figma` (follow figma-use skill patterns):

- Create a new page named `{screen-name} — craft`
- Switch to that page (`setCurrentPageAsync`)
- Call `resize(width, height)` FIRST to set dimensions, THEN set sizing modes (`primaryAxisSizingMode`, etc.) — `resize()` resets sizing modes to FIXED
- Create a root frame: fixed width (1440px or spec breakpoint), auto height via vertical auto-layout
- Position the root frame away from origin — scan existing page children for `maxX` to avoid overlap (especially in RESUMING mode), or use `(100, 100)` on empty pages
- Return the page ID and root frame ID

### Step 1b — Validate page creation

Call `get_metadata` on the page to verify root frame exists with correct hierarchy before building zones. Fix before proceeding.

### Step 2 — Build zones (1-3 `use_figma` calls per zone)

For each zone in spec (top-to-bottom from wireframe):

**Zone frame:** Create a vertical auto-layout frame inside the root. Name it after the zone. Use DS variables for padding, gap, background, and corner radius if discovered in Phase 1. Fall back to defaults (16px padding, 12px gap, light gray `{r:0.96, g:0.96, b:0.96}`, 8px radius) only when no DS tokens exist.

**Components inside zone:**
- **Mapped (component_set):** Import via `importComponentSetByKeyAsync(key)`. Use `defaultVariant` (or spec-specified variant). Create instance, append to zone, set horizontal sizing to FILL after append.
- **Mapped (single component):** Import via `importComponentByKeyAsync(key)`. Create instance, append, FILL.
- **Unmapped:** Create a placeholder rectangle (light orange fill `{r:1, g:0.9, b:0.8}`) with a text label showing the component name. **Must call `loadFontAsync({family:"Inter", style:"Regular"})` before creating any text node** — use a common font, fall back to system fonts if Inter unavailable.

**Sidebar layout:** If wireframe shows sidebar, create a horizontal auto-layout parent containing a fixed-width sidebar frame (280px) and a FILL content column, instead of a single vertical stack.

**Variant selection:** Default to `defaultVariant`. If spec specifies a variant (e.g., "size=sm"), find matching child in component set by name. Fall back to default if no match.

### Error handling during build

```
use_figma fails → retry once → skip zone on second failure → continue
Report skipped zones in output (E4)
```

### Step 3 — Update workflow state

Write to spec's `## Workflow` section:
```
last_action: craft
last_action_status: COMPLETE (or PARTIAL)
coverage_at_craft: {mapped_count}/{total_count} ({%})
craft_figma_page: "{screen-name} — craft"
```

---

## Phase 3: Validation

```
1. Call get_screenshot(fileKey, nodeId=rootFrameId)
2. Evaluate:
   - Blank canvas or elements stacked in corner → PARTIAL
   - Recognizable layout → COMPLETE
3. Display screenshot + summary
```

---

## Error Journeys

| Error | Trigger | Message | Recovery |
|-------|---------|---------|----------|
| **E1** | `search_design_system` not available | "Figma MCP not available. Install: [link]" | Install Figma MCP |
| **E2** | `ds/screens/active/` empty | "No active spec. Run `/design-screen spec` first." | Run spec |
| **E3** | >50% unmapped | Mapping table + 3 options: continue / init scan / abort | User chooses |
| **E4** | `use_figma` error mid-build | "Zone {name} failed. Skipping." (after 1 retry) | Manual fix or re-run |
| **E5** | Permission error on `use_figma` | "Write access denied. Requires Full seat." | Upgrade seat |
| **E6** | Rate limit on `search_design_system` | "{N}/{total} mapped. Options: continue / wait / abort" | User chooses |

---

## Figma MCP Constraints

These are handled by the `figma-use` skill — load it before writing any `use_figma` code. Key constraints the agent must respect:

- **`skillNames: "figma-use"`** — pass in every `use_figma` call (logging parameter)
- **`await` every async call** — `importComponentSetByKeyAsync`, `loadFontAsync`, `setCurrentPageAsync` are all async. Unawaited = silent failures.
- **`resize()` before sizing modes** — `resize()` resets sizing to FIXED. Call it before setting `primaryAxisSizingMode`/`counterAxisSizingMode`.
- **`loadFontAsync` before text** — required before creating or modifying any text node (placeholder labels)
- **Fills are read-only arrays** — clone, modify, reassign. Never mutate in place.
- **Colors in 0-1 float range** (not hex)
- **`layoutSizingHorizontal = "FILL"` AFTER `appendChild`** — setting before append throws
- **Page context resets each call** — always `setCurrentPageAsync` first
- **Return all created/mutated node IDs** from every call
- **Never use `figma.notify()`** — throws "not implemented"
- **Never wrap in async IIFE** — code is auto-wrapped
- **Never use `figma.closePlugin()`** — use `return` for output

---

## Output

Follow `references/templates/outputs/craft-output.md`.

| State | Condition |
|-------|-----------|
| COMPLETE | All zones built, screenshot shows recognizable layout |
| PARTIAL | Some zones skipped or screenshot shows issues |
| ABORTED | User chose to abort |
| ERROR | Figma MCP unavailable or permission denied |

---

## Rules

- **Spec is source of truth** — zones and components come from the active spec
- **Placement draft, not design-ready** — set expectations in output
- **Auto-layout zones** — use Figma auto-layout frames (spike-validated), not absolute positioning
- **Deduplicate before discovery** — one `search_design_system` call per unique component
- **Validate with `get_metadata`** — check root frame hierarchy after page creation, before building zones
- **Single screenshot** — one `get_screenshot` at end, not per zone
- **Filter to target library** — first result's libraryName becomes the filter for subsequent searches
- **Load figma-use skill** — MANDATORY before any `use_figma` call

## Never

- Never create components in Figma (uses existing DS only)
- Never modify existing DS components
- Never skip the budget check
- Never call `get_screenshot` per zone
