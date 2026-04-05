---
title: "design-screen craft action — Figma design generation from spec"
status: shipped
created: 2026-03-27
estimate: 4h
tier: standard
---

# design-screen craft action — Figma design generation from spec

## Context

The design-screen skill composes UI screens from existing components (spec → ship → review). Currently the flow goes from spec (Markdown) straight to code (ship). There's no visual checkpoint. The craft action fills this gap: it reads a screen spec, maps spec components to Figma design system components, and generates a component placement draft in Figma — zone by zone — using the Figma MCP `use_figma` tool. This gives design engineers a structural artifact to confirm component selection and layout structure before shipping code.

**WHAT:** New `craft` action for the design-screen skill that generates Figma component placement drafts from screen specs.
**WHY:** Component selection confirmation + structural layout check before code — catch wrong-component and wrong-structure issues in Figma, not in code review. This is a placement draft, not a design-ready artifact (Figma MCP beta: absolute positioning, no auto-layout).
**WHO:** Design engineers who use both Figma and code, working with an existing Figma design system. Requires Figma Full seat (not Dev seat) and Org+ plan recommended (Starter: 6 reads/month is insufficient for most screens).

## Codebase Impact (MANDATORY)

| Area | Impact | Detail |
|------|--------|--------|
| `design-screen/references/actions/craft.md` | CREATE | Core action logic — load spec, discover Figma DS, map components, build zone-by-zone, validate with screenshots |
| `design-screen/references/templates/outputs/craft-output.md` | CREATE | Chat output format for craft action (iteration progress, exit states, mapping table) |
| `design-screen/SKILL.md` | MODIFY | Add craft to: frontmatter description (line 6), actions table (line 36-42), flow diagram (line 46-58), action router (line 138-156), triggers (line 8-10), agent capabilities table (line 23-31), principles loading table (line 182-188), status section (line 222-237), templates table (line 199-207), references (line 252-262) |
| `design-screen/README.md` | MODIFY | Add craft to: actions table, flow diagrams, examples section, architecture tree, update action count from "5 actions" to "6 actions", update "4 principles x 5 actions x 7 templates" to "4 principles x 6 actions x 8 templates" |
| `design-screen/references/templates/outputs/spec-output.md` | MODIFY | Add `/design-screen craft` as next step option in Phase 2 output |
| `README.md` (root) | MODIFY | Update design-screen entry from 5 to 6 actions, mention craft in workflow |

**Files:** 2 create | 4 modify | 0 affected
**Reuse:** Ship action's loop pattern (mode detection, spec loading, zone iteration, stuck detection, progress tracking). Spec action's compose-block data structure (zones, coverage, components). Init action's parallel scan pattern.
**Breaking changes:** None — craft is additive. All existing actions unchanged.
**New dependencies:** None — uses Figma MCP tools already available. No new packages.

## User Journey (MANDATORY)

### Primary Journey

ACTOR: Design engineer with an active screen spec and a Figma file with a design system
GOAL: Generate a Figma design from the screen spec to visually validate the layout before shipping code

PRECONDITION: `ds/conventions.md` exists, active spec in `ds/screens/active/`, Figma MCP available, user has Full seat in Figma

1. User runs `/design-screen craft`
   → System loads active spec from `ds/screens/active/`
   → User sees spec loaded confirmation with screen name and zone count

2. System discovers Figma DS components
   → Calls `search_design_system` for each spec component
   → System builds a mapping table: spec component → Figma component + confidence level
   → User sees mapping table with confidence levels (code-connect: 100%, name-match: 80%, fuzzy-match: 60%, unmapped: 0%)

3. User confirms mapping (or adjusts unmapped components)
   → System asks user to confirm mapping before proceeding
   → User approves or provides manual overrides for unmapped items

4. System creates a new Figma page via `use_figma`
   → Page named `{screen-name} — craft`
   → User sees page creation confirmation

5. System builds design zone-by-zone (1-3 `use_figma` calls per zone)
   → Each zone: places Figma component instances at calculated coordinates (see Coordinate Strategy below)
   → Default variant used for each component (overridable in mapping step)
   → User sees iteration progress after each zone

6. System validates with `get_screenshot`
   → Takes screenshot of completed design
   → Agent evaluates screenshot: if canvas appears blank or all elements are stacked in one corner → exit with PARTIAL state + "Screenshot shows potential layout issues. Open Figma to verify manually."
   → If screenshot shows recognizable layout → exit with COMPLETE state
   → User sees screenshot + summary (zones built, coverage achieved, known limitations)

POSTCONDITION: Figma page exists with component placement draft matching spec structure. User reviews in Figma to confirm component selection and approximate layout before `/design-screen ship`.

### Resume State Detection

```
Figma page named "{screen-name} — craft" already exists?
  YES → Count zones on page vs spec zone count
    zones_on_page < spec_zones → RESUMING (offer: resume from last zone or restart)
    zones_on_page = spec_zones → ALREADY_CRAFTED (offer: overwrite or create new)
  NO → FRESH (start from beginning)
```

### Design Decisions (craft.md must document these)

**Coordinate Strategy (auto-layout — spike-validated 2026-03-27):**
- Canvas width: 1440px (desktop default, configurable in spec)
- Zone placement: auto-layout vertical stacking (zones self-size based on component intrinsic heights)
- Sidebar zones: HORIZONTAL auto-layout parent with fixed-width sidebar + FILL content column
- Spacing: managed by auto-layout `itemSpacing` property
- **No overlap expected** — auto-layout handles sizing. Components render at their natural dimensions.
- **Supersedes** the original 80px/line heuristic after spike validated that Figma MCP `use_figma` supports full auto-layout API.

**Variant Selection:**
- Default: use "default" or first variant for each component
- If spec zone specifies a variant (e.g., "Button/primary"), use that variant
- User can override variants in the mapping confirmation step (step 3)

**Zone → Figma Mapping:**
- Each zone becomes a Figma Frame (not Group or Section)
- Frame is named after the zone name from spec
- Component instances are placed inside the zone frame

**Color/Token Handling:**
- Figma MCP `use_figma` requires RGB 0-1 float format (not hex)
- When using DS components, colors come from the component itself (no conversion needed)
- For background fills or custom colors: convert hex to 0-1 at runtime (e.g., #FF0000 → {r: 1, g: 0, b: 0})

**Figma MCP Detection:**
- Detection strategy: check if `search_design_system` tool exists in agent's available tools
- If tool not available → E1 (no Figma MCP)
- If tool available → use first component name from spec's zone list as probe query (doubles as first discovery call)
- If probe returns permission error → E5 (no Full seat)
- If probe returns results → MCP confirmed + first component mapped

**Read Rate Limit & Budget Awareness (Phase 0 — BLOCKING):**
- `search_design_system` IS a read call (rate limited: 6/month Starter, 200/day Org)
- `get_screenshot` IS a read call — call once at end, not per zone
- **Budget check before discovery:** Calculate `reads_needed = N_unique_components + 1 (screenshot)`. If on Starter tier and reads_needed > 6, warn: "Craft needs {N} Figma MCP read calls. Starter plan allows 6/month. Recommend Org plan, or reduce components. Continue anyway?"
- Discovery strategy: call `search_design_system` once per unique component name (not per zone — deduplicate first). Each call costs 1 read.
- Minimum viable: Org plan (200 reads/day). Starter plan is technically supported but impractical for screens with 6+ components.

**`search_design_system` → `use_figma` Field Contract:**
- `search_design_system` returns: `{ name, key, description }` per component
- The `key` field is the component key needed by `use_figma` to create an instance
- Mapping table stores: `{ spec_component_name, figma_component_key, figma_component_name, confidence }`
- `use_figma` call uses `figma_component_key` to create instance at specified x/y coordinates
- If `search_design_system` response shape changes (beta API), craft fails at mapping step with clear error

**Fuzzy-Match Algorithm:**
- Code-connect (100%): component has explicit Figma↔code mapping via Code Connect
- Name-match (80%): exact case-insensitive match between spec component name and Figma component name
- Fuzzy-match (60%): substring match OR Levenshtein edit distance ≤ 3 characters between names (case-insensitive). Example: spec "DataTable" matches Figma "Data Table" (space removed = substring match)
- Unmapped (0%): no match found — component becomes a labeled placeholder rectangle in Figma

### Error Journeys

E1. No Figma MCP available
   Trigger: Figma MCP tools not detected in agent capabilities
   1. User runs `/design-screen craft`
      → System detects no Figma MCP
      → User sees: "Figma MCP not available. Craft requires Figma MCP with write access. Install: https://github.com/anthropics/claude-code/blob/main/docs/figma.md"
   Recovery: User installs Figma MCP, retries

E2. No active spec found
   Trigger: `ds/screens/active/` is empty
   1. User runs `/design-screen craft`
      → System finds no active spec
      → User sees: "No active spec found. Run `/design-screen spec` first."
   Recovery: User runs spec first

E3. Component mapping fails (>50% unmapped)
   Trigger: Figma DS doesn't match code components
   1. System runs `search_design_system` for each component
      → More than 50% of components return no match
      → User sees mapping table with high unmapped count
   2. System asks: "High unmapped rate ({N}%). Options: (1) Continue with available components, (2) Run `/design-screen init` with Figma scan to improve mapping, (3) Abort"
   Recovery: User runs init with Figma scan, or continues with partial mapping

E4. `use_figma` call fails mid-build
   Trigger: Figma API error during zone construction
   1. System's `use_figma` call returns error
      → System logs error and zone that failed
      → User sees: "Zone {name} failed: {error}. Retrying..."
   2. System retries once
      → If retry fails: "Zone {name} failed after retry. Skipping. Continue with remaining zones."
   Recovery: User can manually fix the zone in Figma, or re-run craft

E5. No Figma Full seat (write access denied)
   Trigger: `use_figma` returns permission error
   1. System attempts first `use_figma` call
      → Returns permission/access error
      → User sees: "Write access denied. Craft requires a Full seat in Figma (not Dev seat). Check your Figma plan."
   Recovery: User upgrades seat or uses a different Figma account

E6. Read rate limit exhausted mid-discovery
   Trigger: `search_design_system` returns rate limit error during component discovery
   1. System's `search_design_system` call returns rate limit error
      → System logs mapped components so far
      → User sees: "Figma MCP read limit reached. {N}/{total} components mapped. Options: (1) Continue with partial mapping, (2) Wait and retry later, (3) Abort"
   Recovery: User continues with partial mapping or waits for rate limit reset

### Edge Cases

EC1. Spec has 0 gaps (100% coverage): All components map directly, simplest path — no user decisions needed on gaps
EC2. Spec has only gaps (0% coverage): Every component is unmapped — warn user, offer to abort or continue with placeholder rectangles
EC3. Multiple active specs: System asks user which spec to craft
EC4. Very large spec (20+ zones): Warn about number of `use_figma` calls needed, ask user to confirm before proceeding
EC5. Figma DS uses different naming than code (e.g., "CTA Button" vs "Button"): Fuzzy matching catches most, user confirms edge cases in mapping step

## Acceptance Criteria (MANDATORY)

### Must Have (BLOCKING — all must pass to ship)

- [ ] AC-1: GIVEN an active spec in `ds/screens/active/` WHEN user runs `/design-screen craft` THEN the system loads the spec and displays zone count + component list
- [ ] AC-2: GIVEN a loaded spec WHEN system discovers Figma DS components THEN a mapping table is displayed showing each spec component → Figma component with confidence level (code-connect/name-match/fuzzy-match/unmapped)
- [ ] AC-3: GIVEN a confirmed mapping WHEN system builds the design THEN each zone produces 1 `use_figma` call (simple zones) or 2-3 calls (complex zones with nested components), creating Figma component instances at heuristic coordinates (overlap possible — manual cleanup may be needed)
- [ ] AC-4: GIVEN all zones built WHEN system validates THEN a `get_screenshot` call returns a visual of the completed design
- [ ] AC-5: GIVEN craft completes successfully WHEN user sees craft-output.md THEN next steps suggest `/design-screen ship` and show Figma page link
- [ ] AC-6: GIVEN the craft action definition WHEN SKILL.md is read THEN craft appears in: frontmatter, actions table, flow diagram, action router, triggers, agent capabilities, principles loading table, status section, templates table, references section (10 locations)
- [ ] AC-7: GIVEN the spec-output.md template WHEN Phase 2 spec is generated THEN `/design-screen craft` appears as a next step option
- [ ] AC-8: GIVEN a previous craft run exists WHEN user re-runs craft THEN system detects existing Figma page and asks: overwrite or create new
- [ ] AC-9: GIVEN read budget exceeds Starter tier limit WHEN craft starts THEN user sees budget warning with read count estimate before discovery begins

### Error Criteria (BLOCKING — all must pass)

- [ ] AC-E1: GIVEN no Figma MCP WHEN user runs craft THEN error message with install link is shown (not a crash)
- [ ] AC-E2: GIVEN no active spec WHEN user runs craft THEN error message suggests `/design-screen spec` first
- [ ] AC-E3: GIVEN >50% unmapped components WHEN mapping completes THEN user is warned and given options (continue/init-scan/abort)
- [ ] AC-E4: GIVEN a `use_figma` failure WHEN building a zone THEN system retries once, skips on second failure, continues with remaining zones
- [ ] AC-E5: GIVEN write access denied WHEN first `use_figma` call fails THEN clear error about Full seat requirement
- [ ] AC-E6: GIVEN craft was interrupted mid-build WHEN user re-runs craft THEN system detects partial state (existing page with fewer zones than expected) and offers: resume from last zone or restart

### Deferred (v2)

- AC-D1: Page states generation as separate Figma frames
- AC-D2: Responsive variants as separate Figma frames
- AC-D3: Init Scan F for Figma DS pre-mapping
- AC-D4: Figma DS mapping section in conventions.md

## Scope

- [ ] 1. Create `craft.md` action file with full logic (Phase 0 budget check, mode detection, resume state, spec loading, Figma discovery with field contract, mapping with fuzzy algorithm, coordinate strategy, zone-by-zone build, variant selection, screenshot evaluation) → AC-1, AC-2, AC-3, AC-4, AC-5, AC-8, AC-9
- [ ] 2. Create `craft-output.md` output template (mapping table with component key, confirmation prompt, iteration progress, exit states, known limitations section, manual verification checklist for <100% confidence components) → AC-5
- [ ] 3. Update `SKILL.md` with craft action (10 locations: frontmatter, actions table, flow, router, triggers, capabilities, principles, status, templates, references) → AC-6
- [ ] 4. Update `README.md` (skill) with craft documentation (actions table, flow, examples, architecture tree, counts) → AC-6
- [ ] 5. Update `spec-output.md` to suggest craft as next step → AC-7
- [ ] 6. Update root `README.md` (action count, mention craft) → AC-6
- [ ] 7. Implement error handling in craft.md (no MCP, no spec, mapping failures, use_figma failures, permission errors, rate limit exhaustion, resume from interruption) → AC-E1, AC-E2, AC-E3, AC-E4, AC-E5, AC-E6

### Out of Scope (v1)

- Pencil MCP support or any non-Figma design tool
- Bidirectional sync (Figma → spec updates)
- Asset/image generation in Figma (Figma MCP doesn't support images)
- Custom font handling (Figma MCP limitation)
- Automated design review (visual diff between craft output and spec wireframe)
- Component creation in Figma (craft uses existing DS components only)

### Deferred to v2

- Scan F (Figma Discovery) in init.md — pre-map Figma DS to code components (craft works without it via runtime `search_design_system`)
- Figma DS mapping section in conventions.md — depends on Scan F
- Page states generation as separate Figma frames (AC-8)
- Responsive variants as separate Figma frames (AC-9) — unrealistic with current Figma MCP (no auto-layout API, 2-3x call multiplication)

## Quality Checklist

### Blocking (must pass to ship)

- [ ] All Must Have ACs passing
- [ ] All Error Criteria ACs passing
- [ ] All scope items implemented
- [ ] No regressions in existing actions (spec, ship, review, init, spec-review all unchanged in behavior)
- [ ] Error states handled (not just happy path)
- [ ] SKILL.md validates (all sections internally consistent — router matches table matches flow)
- [ ] Markdown files have no broken internal links
- [ ] craft.md follows same structural pattern as ship.md (mode detection → setup → loop → exit)

### Advisory (should pass, not blocking)

- [ ] All Should Have ACs passing
- [ ] craft.md includes Figma MCP constraint documentation (sequential calls, color 0-1 format, fills immutable, 20kb limit)
- [ ] craft.md includes coordinate strategy and variant selection rules
- [ ] craft.md includes read rate limit awareness (batch search_design_system, single get_screenshot)
- [ ] README examples show craft in context of full workflow

## Test Strategy (MANDATORY)

### Test Environment

| Component | Status | Detail |
|-----------|--------|--------|
| Test runner | not applicable | Pure Markdown skill — no executable code |
| E2E framework | not applicable | Validation is manual (read Markdown, check structure) |
| Figma MCP | required for runtime | Cannot test write calls without real Figma |
| Manual validation | primary method | Read generated files, verify structure and consistency |

### AC → Test Mapping

| AC | Test Type | Test Intention |
|----|-----------|----------------|
| AC-1 | Manual review | craft.md has spec loading logic matching ship.md pattern |
| AC-2 | Manual review | craft.md has Figma discovery with field contract + mapping table with 4 defined confidence levels |
| AC-3 | Manual review | craft.md has zone-by-zone loop with 1-3 `use_figma` calls per zone + coordinate strategy + overlap warning |
| AC-4 | Manual review | craft.md has screenshot validation step with evaluation criteria (blank canvas detection) |
| AC-5 | Manual review | craft-output.md suggests `/design-screen ship` + includes known limitations section |
| AC-6 | Manual review | SKILL.md has craft in all 10 required locations |
| AC-7 | Manual review | spec-output.md Phase 2 suggests `/design-screen craft` as next step |
| AC-8 | Manual review | craft.md has existing page detection with resume/overwrite logic |
| AC-9 | Manual review | craft.md has Phase 0 budget check with read count estimate + Starter tier warning |
| AC-E1 | Manual review | craft.md has Figma MCP detection via tool availability check |
| AC-E2 | Manual review | craft.md has spec existence check with error message |
| AC-E3 | Manual review | craft.md has mapping quality check at 50% threshold |
| AC-E4 | Manual review | craft.md has retry logic for use_figma failures |
| AC-E5 | Manual review | craft.md has permission error handling |
| AC-E6 | Manual review | craft.md has resume from interruption (partial state detection) |

### Failure Mode Tests (MANDATORY)

| Source | ID | Test Intention | Priority |
|--------|----|----------------|----------|
| Error Journey | E1 | Manual: craft.md gracefully handles missing Figma MCP | BLOCKING |
| Error Journey | E3 | Manual: craft.md handles high unmapped rate without crashing | BLOCKING |
| Error Journey | E4 | Manual: craft.md handles use_figma failures with retry + skip | BLOCKING |
| Error Journey | E6 | Manual: craft.md handles rate limit exhaustion mid-discovery | BLOCKING |
| Failure Hypothesis | FH-CRITICAL | Manual: craft.md has Phase 0 budget check before any read calls | BLOCKING |
| Failure Hypothesis | FH-1 | Manual: SKILL.md internal consistency (router, table, flow all match) | BLOCKING |
| Failure Hypothesis | FH-2 | Manual: craft.md Figma MCP constraints documented (sequential, colors, fills) | BLOCKING |
| Failure Hypothesis | FH-3 | Manual: craft.md includes field contract (search_design_system → use_figma) | BLOCKING |
| Failure Hypothesis | FH-4 | Manual: craft.md screenshot evaluation handles blank canvas → PARTIAL | BLOCKING |
| Edge Case | EC4 | Manual: craft.md warns on large specs (20+ zones) | Advisory |

### Mock Boundary

| Dependency | Strategy | Justification |
|------------|----------|---------------|
| Figma MCP | Real (runtime) | Cannot mock MCP tools in Markdown skill — validated at runtime by actual agent |
| Figma DS | Real (runtime) | Component discovery requires real Figma file |

### TDD Commitment

N/A — pure Markdown skill. No executable test suite. Validation is structural review of generated Markdown files against acceptance criteria. Each AC is verified by reading the relevant file section.

## Risks

| Risk | Impact | Likelihood | Mitigation |
|------|--------|------------|------------|
| Starter tier users exhaust monthly read budget in single craft run | HIGH | HIGH | Phase 0 budget check, Org plan recommended, explicit warning before discovery |
| Figma MCP `use_figma` is beta-quality — may produce broken/overlapping layouts | HIGH | MED | Reframed as "placement draft", screenshot evaluation, PARTIAL exit state, manual verification checklist |
| `use_figma` page context resets between calls — zone positioning may drift | HIGH | HIGH | Explicit x/y coordinates per call, spacing buffer, overlap documented as expected |
| Figma DS component naming doesn't match code component naming | MED | HIGH | Multi-level matching (code-connect → name → fuzzy with defined algorithm), user confirmation step |
| `use_figma` may not support variant property overrides on instances | MED | MED | Spike to validate; fallback: place base component, document in output |
| `use_figma` 20kb response limit — complex zones may hit it | MED | LOW | Keep each zone call focused (1 component tree per call), warn if zone is complex |
| Figma MCP beta ends → becomes paid → users lose free access | LOW | MED | Document beta status, skill works without craft (it's optional), monitor Figma pricing changes |
| Sequential `use_figma` calls make large screens slow (20+ zones = 20+ calls) | MED | MED | Warn user on large specs, suggest zone consolidation, show progress per zone |

**Kill criteria:** If Figma MCP removes `use_figma` write capability or makes it prohibitively expensive, the craft action becomes unviable. Fall back to spec → ship (no visual step). The skill's core value (zero reinvention via spec) is unaffected.

## State Machine

```
[idle] ──/design-screen craft──▶ [loading_spec]
                                      │
                              spec found? ─NO──▶ [error_no_spec]
                                      │YES
                                      ▼
                               [budget_check]
                                      │
                              budget ok? ─WARN──▶ [user_confirms_budget]
                                      │YES              │YES
                                      ▼                 ▼
                               [check_existing_page]
                                      │
                              page exists? ─YES──▶ [resume_or_overwrite]
                                      │NO               │
                                      ▼                 ▼
                               [discovering_ds]
                                      │
                              mapping done? ──▶ [awaiting_confirmation]
                                                       │
                                              user confirms? ─NO──▶ [aborted]
                                                       │YES
                                                       ▼
                                              [creating_page]
                                                       │
                                                       ▼
                                              [building_zones] ◀──┐
                                                    │    │        │
                                              zone ok?   fail?    │
                                                │        │retry   │
                                                │        │        │
                                                ▼        ▼        │
                                           [next_zone]  [skip]────┘
                                                │
                                          all done? ─NO──┘
                                                │YES
                                                ▼
                                         [validating]
                                                │
                                      screenshot ok? ─NO──▶ [partial]
                                                │YES
                                                ▼
                                           [complete]
```

States: idle, loading_spec, error_no_spec, budget_check, user_confirms_budget, check_existing_page, resume_or_overwrite, discovering_ds, awaiting_confirmation, aborted, creating_page, building_zones, next_zone, skip, validating, partial, complete
Transitions: Sequential, user-gated at confirmation + budget warning steps
Invalid transitions: Cannot discover before budget check, cannot build zones before confirmation, cannot validate before all zones attempted
Race conditions: None — all `use_figma` calls are sequential (MCP constraint)

## Analysis

### Assumptions Challenged

| Assumption | Evidence For | Evidence Against | Verdict |
|------------|-------------|-----------------|---------|
| Figma MCP `use_figma` can create component instances from DS | Figma MCP docs list `create_component_instance` as capability | Beta quality, complex component trees may not render correctly | RISKY — test with real DS components early |
| `search_design_system` returns usable component keys | Documented Figma MCP tool, returns component metadata | Only works if Figma file has a published library; scope may be file-level (cross-file libraries common in mature DS setups) | RISKY — must handle "no library" and "wrong file" cases |
| 80px/line heuristic produces usable zone placement | Defined with min/max bounds, canvas width fixed | Arbitrary constant — actual Figma component intrinsic heights differ wildly (NavBar 56px, DataTable 400px). Heuristic heights ≠ real component heights. | RISKY — overlap is expected on complex layouts. AC-3 updated to reflect this. Manual cleanup is part of the workflow. |
| `search_design_system` can be reliably batched | Reduces rate limit consumption | A broad query returns only matching components, not all components. A search for "Button" won't return "DataTable". Per-component calls are required for reliable mapping. | WRONG → RESOLVED: replaced "batch ONCE" with per-component calls + budget check |
| Starter tier (6 reads/month) is viable for craft | Feature is optional | 6+ components + 1 screenshot = 7+ reads per craft run. Starter budget exhausted in one session. | WRONG → RESOLVED: added budget check (Phase 0), documented Org plan as recommended minimum |
| Output constitutes "visual validation" | Produces screenshot of placed components | Absolute positioning, no auto-layout, heuristic heights, beta tool quality. Output is a structural draft, not a design-ready artifact. | WRONG → RESOLVED: reframed as "component placement draft" / "structural layout check" |
| Users have Full seat in Figma | Required for `use_figma` write access | Dev seats are dominant for engineers who also code — the primary target persona may not have write access | RISKY — detect early, fail fast with clear message |
| `use_figma` supports variant property overrides on instances | Variant selection strategy depends on this | Beta API — may only create base component, not specific variant | RISKY — explore before implementation |

### Blind Spots

1. **[Integration]** No strategy for Figma auto-layout vs absolute positioning. Figma MCP `use_figma` may not support auto-layout natively — absolute positioning will look rigid.
   Why it matters: Without auto-layout, the generated design won't resize properly when the user edits it in Figma. Accepted limitation for v1 — documented in output as known limitation.

2. **[API Contract]** `search_design_system` response shape → `use_figma` input format was unspecified.
   Why it matters: Agent would guess which field to pass, causing silent wrong-component placement.
   **RESOLVED:** Added field contract to Design Decisions section.

3. **[Agent Logic]** No resume state for interrupted craft sessions.
   Why it matters: Re-run from scratch wastes rate-limited reads and user time.
   **RESOLVED:** Added resume state detection section and AC-E6.

4. **[Agent Logic]** No evaluation criteria for what a "passing" screenshot looks like.
   Why it matters: Agent declares success on blank/broken canvas.
   **RESOLVED:** Added screenshot evaluation step (blank canvas → PARTIAL exit state).

5. **[Data]** Fuzzy-match (60%) had no defined algorithm.
   Why it matters: Different agents produce different mapping tables.
   **RESOLVED:** Defined as substring match OR edit distance ≤ 3.

6. **[Workflow]** Multi-file Figma libraries — `search_design_system` may be file-scoped.
   Why it matters: Discovery misses cross-file library components silently. Accepted risk — E3 triggers as fallback.

7. **[Maintenance]** Craft couples skill to Figma MCP beta API — maintenance cost for OSS project.
   Why it matters: API changes break craft silently, erodes trust in whole skill. Accepted risk — craft is optional.

### Failure Hypotheses

| IF | THEN | BECAUSE | Severity | Mitigation |
|----|------|---------|----------|------------|
| Craft runs on Starter tier with 6+ components | User's monthly Figma MCP read budget exhausted in one session | 1 detection + N discovery + 1 screenshot > 6 reads | **CRITICAL** | **ADDED:** Phase 0 budget check, Org plan recommended, explicit warning |
| Figma DS library is unpublished or private | `search_design_system` returns empty results for all components | Only published libraries are searchable via MCP | HIGH | Check library availability first, fall back to creating rectangles with component names as labels |
| `use_figma` produces overlapping/broken layouts >30% of the time | Users abandon craft after first run — dead weight | Beta tool + absolute positioning + arbitrary height heuristic = high variance | HIGH | **ADDED:** Reframed as "placement draft", screenshot evaluation, PARTIAL exit state, manual verification checklist |
| DS uses multi-variant components (Size/State/Theme) | Wrong variant placed, design looks wrong | `use_figma` may not support variant property overrides | HIGH | Explore before implementation; fallback: place base component, document in output |
| Agent reads "batch ONCE" and sends broad query | Gets 0-5 results for a 50+ component DS | Broad query ≠ dump all | HIGH | **RESOLVED:** Replaced with per-component calls + budget awareness |
| `use_figma` succeeds but places elements off-canvas | Screenshot shows nothing, agent declares success | Beta tool silent failure mode | MED | **ADDED:** Screenshot evaluation criteria, PARTIAL exit state |
| Zone-by-zone build creates overlapping elements | Figma page needs manual cleanup | Heuristic heights ≠ actual component heights | MED | Accepted — documented as known limitation, spacing buffer mitigates |
| Craft becomes maintenance burden as Figma MCP evolves | Breaking changes in `use_figma` API break craft | Beta API = unstable contract | LOW | Keep logic simple (thin wrapper), craft is optional |

### The Real Question

**Consensus from 4 perspectives:** The problem is real — the gap between ASCII spec and code is a source of late-stage issues. But craft v1 with Figma MCP beta cannot deliver "visual validation" — it delivers **component selection confirmation** and **structural layout check**.

**Reframed value prop:** Craft v1 answers "did I pick the right DS components for each zone?" and "is the rough layout structure correct?", not "does this screen design work visually?" The absolute positioning, no auto-layout, and height heuristic limitations make real design validation impossible in v1.

**Recommendation:** Ship craft v1 with honest framing. "Component placement draft, not design-ready." This sets correct expectations and is still genuinely useful. v2 can expand when Figma MCP matures.

**Spike recommendation (from 2/4 perspectives):** Run a 1-2h spike with real Figma MCP on a real DS before writing craft.md. Zero end-to-end validation has been done. The 4h estimate assumes Markdown writing but doesn't include integration discovery. **Decision: spike before ship recommended but not blocking.**

### Open Items

- [risk] `use_figma` auto-layout support unknown → explore (spike with real Figma file)
- [risk] `use_figma` variant property overrides unknown → explore (spike will validate)
- [risk] Large specs (20+ zones) performance → no action (warn in output, user decides)
- [resolved] Variant selection strategy → default variant, user overrides in mapping step
- [resolved] Design token mapping → DS components carry their own colors; custom = hex→0-1
- [resolved] Coordinate strategy → 1440px canvas, top-to-bottom stacking, 80px/line heuristic (overlap documented as expected)
- [resolved] Batch discovery → replaced with per-component calls + budget check
- [resolved] Read budget → Phase 0 budget check, Org plan recommended
- [resolved] Field contract → documented search_design_system → use_figma data flow
- [resolved] Fuzzy-match algorithm → substring OR edit distance ≤ 3
- [resolved] Resume state → added detection + AC-E6
- [resolved] Screenshot evaluation → blank canvas = PARTIAL exit
- [resolved] Existing page detection → promoted to Must Have (AC-8)
- [resolved] Value prop framing → "component placement draft" not "visual validation"
- [deferred] Scan F (Figma Discovery) + conventions.md mapping → v2
- [deferred] Page states + responsive variants → v2
- [question] Should craft update the spec's Progress section? → update spec (yes — add "crafted" status column)

## Notes

- Spec review applied: 2026-03-27 (4 perspectives: Figma MCP Expert, Skill Author, Design Engineer, Skeptic)
- Key reframe: v1 is "component placement draft" not "visual validation" — honest about Figma MCP beta limitations
- Spike recommended before implementation: 1-2h with real Figma MCP + real DS to validate use_figma behavior, variant overrides, and search_design_system response shape

## Progress

| # | Scope Item | Status | Iteration |
|---|-----------|--------|-----------|
| 1 | Create craft.md action file (full logic + budget check + resume + field contract + fuzzy algo + screenshot eval) | [x] Complete | 1 |
| 2 | Create craft-output.md template (mapping table + known limitations + verification checklist) | [x] Complete | 1 |
| 3 | Update SKILL.md with craft (10 locations) | [x] Complete | 1 |
| 4 | Update README.md (skill) | [x] Complete | 1 |
| 5 | Update spec-output.md | [x] Complete | 1 |
| 6 | Update root README.md | [x] Complete | 1 |
| 7 | Error handling in craft.md (E1-E6 + resume) | [x] Complete | 1 |

## Timeline

| Action | Timestamp | Duration | Notes |
|--------|-----------|----------|-------|
| plan | 2026-03-27T14:30:00Z | - | Created |
| spec-review (internal) | 2026-03-27T15:00:00Z | 30min | Deferred v2 items, added coordinate/variant/color strategies, fixed ACs, reduced scope 11→7 |
| spec-review (4 perspectives) | 2026-03-27T15:30:00Z | 45min | Figma MCP Expert + Skill Author + Design Engineer + Skeptic. Merged: budget check, field contract, fuzzy algo, resume state, screenshot eval, value prop reframe, AC-8 promoted, AC-9/AC-E6 added. 1 CRITICAL fixed (Starter budget), 4 HIGH addressed. |
| spike (Figma MCP) | 2026-03-27T16:00:00Z | 30min | Validated: search_design_system, importComponentSetByKeyAsync, defaultVariant, auto-layout zones, get_screenshot. GO. |
| ship | 2026-03-27T16:30:00Z | 1h | All 7 scope items. 2 files created, 5 modified. |
| review (3 perspectives) | 2026-03-28T09:00:00Z | 30min | 3 BLOCKING fixed (coordinate strategy, conventions prerequisite, E4/E6 output templates). 6 WARN deferred to v1.1. |
| done | 2026-03-28T09:30:00Z | - | Shipped. README updated with non-dev onboarding. |
