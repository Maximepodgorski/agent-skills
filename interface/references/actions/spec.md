# Spec Action

> **Agent:** Load this file when `spec` triggers. Also load all files in `references/principles/` and `references/templates/spec.md`.

The core action of the interface skill. Two phases: **Phase 1 (compose)** proposes ASCII wireframe layout options. **Phase 2 (spec)** generates the full interface spec from the chosen layout.

**Triggers:** "spec", "interface spec", "compose", "design screen"

---

## Input Detection

```
Input contains figma.com URL?
  YES → Figma mode (extract design context first)
  NO  → Text mode (compose from description)

Input contains both text + Figma URL?
  → Figma takes priority, text refines intent

ds/conventions.md exists?
  NO → Auto-trigger init (see init.md), then continue
  YES → Load conventions
```

## Step 0: Load Context

### Load Conventions

```
1. Read ds/conventions.md → project DNA (stack, naming, tokens, patterns)
2. Extract: component root path, naming convention, import pattern, layout patterns
```

### Live Scan: Component Inventory

```
1. Glob component directories (from ALL paths in conventions.md — may be multiple roots)
   EXCLUDE: node_modules/, dist/, .next/, .nuxt/, .output/, build/
2. Guard: if 0 components found → error:
   "No components found in project. The interface skill composes from existing components.
    Create components first (manually or via /component), then retry."
3. For each component:
   - Name (from file/folder name)
   - Type (atom/molecule/organism — inferred from imports)
   - Key props (read file header / interface / defineProps)
   - Slots (if framework supports them)
   - Variant system (CVA variants, if detected in conventions)
4. Store in memory (not persisted) for Phase 1 + Phase 2
```

### Figma Context (if URL provided)

```
1. Parse Figma URL → extract fileKey + nodeId
   - figma.com/design/:fileKey/:fileName?node-id=:nodeId → convert "-" to ":" in nodeId
   - figma.com/design/:fileKey/branch/:branchKey/:fileName → use branchKey as fileKey
2. Call get_design_context(fileKey, nodeId) → code + screenshot + hints
3. Extract from response:
   - Layout structure (hierarchy, zones)
   - Components used in the design
   - Spacing, sizing hints
4. Map Figma components to project inventory
5. If Figma MCP unavailable → warn and continue in text mode
```

### Existing Spec Check

```
ds/interfaces/active/{screen-name}*.md exists?
  YES → Ask user: "Spec already exists. Overwrite, update, or cancel?"
    Overwrite → Delete existing, continue
    Update → Read existing, merge with new compose
    Cancel → Stop, return existing spec path
  NO → Continue
```

### Existing Page Check

```
A page/route for this screen already exists in the codebase?
  YES → Note in compose context:
    - Record existing page path
    - Analyze its current components and layout
    - Phase 1 options should propose UPGRADES (not greenfield replacements)
    - Coverage % = (reusable zones / total zones), where existing code counts as reusable
  NO → Continue (greenfield compose)
```

## Phase 1: COMPOSE

Iterative layout proposal loop. Max 3 rounds + free-form fallback.

### Round Logic

```
round = 0
max_rounds = 3

WHILE round < max_rounds:
  round++

  # 1. GENERATE OPTIONS
  Load principles: layout-patterns.md + composition.md + responsive.md
  Select 2-3 layout patterns that fit the screen description
  For each pattern:
    - Draw ASCII wireframe
    - Map zones to components from inventory
    - Calculate coverage % (mapped / total zones)
    - Flag missing components
    - Include responsive collapse notes

  # 2. PRESENT to user
  Follow spec-output.md "Phase 1: Compose Round" template
  Show: wireframe + coverage + components + gaps for each option

  # 3. WAIT for user response
  Parse response:
    - "A" / "B" / "C" / "option 2" / specific pick
      → Capture compose-block → EXIT to Phase 2
    - "mix A+C" / "A but with tabs" / variant request
      → Count as same round (not a new round)
      → Generate variant and re-present
    - "more compact" / "different approach" / rejection
      → round++ → Generate new options
    - (after round 3) → Free-form fallback

  # 4. FREE-FORM FALLBACK (after max_rounds)
  Follow spec-output.md "Phase 1: Free-Form Fallback"
  User describes exact layout
  → Build compose-block from description → EXIT to Phase 2
```

### Compose Rules

- **Between rounds:** Refine based on user feedback. Do NOT re-scan components (inventory stays in memory)
- **Variant = same round:** "A but with sidebar collapsed" is a variant, not a new round
- **3 options per round:** Propose 2-3 structurally different options (not 3 tweaks of the same layout)
- **Coverage honesty:** Report real coverage. Don't inflate by counting wrappers or divs
- **ASCII quality:** Clean box-drawing characters. Label every zone. Max 15 lines, 60 chars wide

### ASCII Wireframe Standards

```
Rules:
  - Use ┌ ─ ┐ │ └ ┘ ├ ┤ ┬ ┴ ┼ for structure
  - Label every zone with its component name
  - Show size hints: (fixed), (scrollable), (flex)
  - Keep wireframes compact (max 15 lines, 60 chars wide)
  - Each option must look structurally different

Good:
  ┌──────┬──────────────────────────┐
  │ Nav  │  PageHeader              │
  │(fixed)├──────────────────────────┤
  │      │  TabBar                  │
  │      ├──────────────────────────┤
  │      │  FormSection (scrollable)│
  │      │                          │
  └──────┴──────────────────────────┘

Bad:
  [sidebar] [content area]      ← Too vague, no labels, no structure
```

### Coverage % Formula

```
coverage = (zones with source="existing") / (total zones) × 100

What counts as a zone:
  - Each labeled area in the ASCII wireframe
  - NOT wrappers/divs/layout containers (unless they ARE a project component)
  - NOT repeated instances (a list of Cards = 1 zone, not N zones)

What counts as "existing":
  - Component exists in project inventory
  - Component can be adapted with minor prop additions (note in props_hint)
  - NOT: "a div with className" or generic HTML

Round to nearest integer. Never inflate.
```

### Compose-Block Capture

When user picks an option, capture this data for Phase 2:

```
compose_block = {
  pattern: "{layout pattern name}",
  coverage: N,
  zones: [
    { name, component, source: "existing"|"gap", props_hint, data_source },
    ...
  ],
  component_tree: "{ASCII tree}",
  missing: [
    { component, zone, proposed_solution },
    ...
  ],
  user_intent: "{original description}",
  figma_context: {if available},
  responsive_notes: "{collapse patterns}",
  existing_page: "{path if upgrading an existing page, null if greenfield}"
}
```

## Phase 2: SPEC

Generate the full interface spec from the compose-block. No re-scan, no re-analysis.

### Generation Order

```
1. Context (from compose-block metadata)
2. Component Coverage (from compose-block zones)
3. Layout Architecture (from compose-block tree + wireframe)
4. Page States (from page-states.md, skip if stateless)
5. Data Flow (from compose-block data_source entries, skip if static)
6. Responsive Behavior (from responsive.md + compose-block notes)
7. Screen Journey (derive from layout + data flow)
8. Acceptance Criteria (derive from journey + edge cases)
9. Accessibility (from layout zones + component types)
10. Decisions (architectural choices made during compose)
```

### Data Source Rules

```
data_source values:
  "static"   → Hardcoded / imported constant. No fetch.
  "prop"     → Passed from parent component/page.
  "store"    → From state management (Pinia, Redux, Zustand, context).
  "api"      → Fetched from API endpoint. Specify endpoint if known.
  "route"    → Derived from route params (e.g., /settings/:tab).
  "computed" → Derived from other data sources.

In TEXT mode (no Figma): data_source can be "unknown" if the agent can't
determine the source. Flag as a decision for the user in the Decisions table.
Never leave data_source blank — use "unknown" + flag it.
```

### Tier Detection

```
IF compose_block.coverage >= 90
   AND compose_block.zones.length <= 8
   AND compose_block.zones.every(z => z.data_source NOT IN ["api", "unknown"])
THEN tier = "mini"
ELSE tier = "standard"

Note: mini tier still requires Page States section if ANY zone has data_source = "api" or "store".
The skip-if rule for Page States is "screen is stateless" — not "tier = mini".
```

### Spec Validation (BLOCKING)

Run all 10 validation rules from `references/templates/spec.md` before saving. If any fail → fix, don't save broken spec.

### Save

```
1. Generate spec content following references/templates/spec.md
2. Save to ds/interfaces/active/{YYYY-MM-DD}-{screen-name}.md
3. Init folders if needed:
   - ds/interfaces/active/
   - ds/interfaces/shipped/
   - ds/interfaces/dropped/
```

## Output

Follow `references/templates/outputs/spec-output.md`:
- During Phase 1: compose round templates
- After Phase 2: spec generated template

## Rules

- **Never re-scan between compose rounds** — component inventory stays in memory
- **Never propose identical layouts** — each option must be structurally different
- **Coverage must be honest** — count real component-to-zone mappings only
- **All gaps must have proposed solutions** — never leave "TBD"
- **ASCII wireframes must be clean** — box-drawing characters, labeled zones, compact
- **Phase 2 is one-shot** — no iteration, no re-analysis; compose-block has everything needed
- **Spec must pass validation rules** — don't save invalid specs
- **Figma fallback is graceful** — MCP unavailable = text mode, not error
