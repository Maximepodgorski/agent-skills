# Init Action

> **Agent:** Load this file when `init` triggers. Also load `references/principles/layout-patterns.md` and `references/templates/conventions.md`.

Onboard a project by scanning the codebase and generating `ds/conventions.md`. This file captures stable project DNA — naming, structure, tokens, patterns. Component inventory is always scanned live (never persisted).

**Triggers:** "init", "setup", first-time use of any interface action (auto-trigger).

---

## Step 0: Check Existing

```
ds/conventions.md exists?
  YES → Check staleness
    Created > 30 days ago?
      YES → Warn: "Conventions may be stale. Re-scanning."
      NO  → "Conventions are current. Skipping init."
            → Return early (follow init-output.md "Already Exists")
  NO → Continue with full scan
```

## Step 1: Parallel Scan

Launch these scans in parallel (single message, multiple tool calls):

### Scan A: Stack Detection

```
Detect from project files:
  1. package.json / Cargo.toml / pyproject.toml → framework, dependencies
  2. tsconfig.json / jsconfig.json → TypeScript config
  3. nuxt.config.* / next.config.* / svelte.config.* → meta-framework
  4. tailwind.config.* / postcss.config.* → styling approach
  5. vite.config.* / webpack.config.* → build tool
  6. vitest.config.* / jest.config.* / playwright.config.* → test runner
  7. CLAUDE.md / .cursorrules / AGENTS.md → project conventions
```

### Scan B: Component Structure

```
Detect from codebase:
  1. Find component directories (EXCLUDE: node_modules, dist, .next, .nuxt, .output, build):
     - Glob: **/components/**/*.{vue,tsx,jsx,svelte}  --ignore node_modules
     - Glob: **/ui/**/*.{vue,tsx,jsx,svelte}           --ignore node_modules
     - Glob: **/lib/**/*.{vue,tsx,jsx,svelte}           --ignore node_modules
     - Glob: **/registry/**/*.{vue,tsx,jsx,svelte}      --ignore node_modules
  2. Detect ALL component locations (projects may have 2+ roots):
     - Group results by parent directory
     - Record each unique root path (e.g., "components/ui/" AND "registry/default/")
     - Note if components are direct-import or copy-paste registry (shadcn pattern)
  3. Detect structure pattern:
     - Single file? (Button.vue in flat directory)
     - Directory? (Button/Button.vue + index.ts)
     - Compound? (Select/Select.vue + SelectItem.vue)
     - Barrel? (components/index.ts re-exports)
     - Registry? (component files meant to be copied, not imported directly)
  4. Detect naming convention from 5+ component samples
  5. Detect variant system if present:
     - CVA (class-variance-authority): grep "cva(" or import from "class-variance-authority"
     - Tailwind variants: grep "tv(" or import from "tailwind-variants"
     - Note variant pattern (variants object structure)
  6. Count total components (number only — not inventory)
  7. Record all component root paths (may be multiple)
```

### Scan C: Token Structure

```
Detect from codebase (EXCLUDE: node_modules, dist):
  1. CSS custom properties: grep "var(--" across CSS/SCSS files (exclude node_modules)
  2. SCSS/LESS variables: grep for $variable or @variable
  3. Tailwind config: read theme.extend if exists
     - Tailwind v4: also check CSS @theme blocks in global CSS
  4. Token files: glob *.tokens.*, tokens.json, tokens.yaml
  5. Detect naming pattern from 10+ token samples:
     - Prefix convention (--color-*, --bg-*, $spacing-*)
     - Category grouping (color, spacing, typography, radius, shadow)
     - Tier structure (primitive → semantic → component / flat)
     - Bridge layers (e.g., shadcn HSL vars → Tailwind classes)
  6. Detect token source (CSS vars, SCSS, Tailwind, JSON)
  7. Detect if multiple CSS strategies coexist:
     - Tailwind utilities + custom CSS vars
     - Global CSS + component-scoped CSS
     - Note the dominant strategy
```

### Scan D: Layout Patterns

```
Detect from codebase (EXCLUDE: node_modules, dist):
  1. Find layout components:
     - Glob: **/*Layout*.{vue,tsx,jsx,svelte}  --ignore node_modules
     - Glob: **/*layout*.{vue,tsx,jsx,svelte}  --ignore node_modules
     - Grep: "defineLayout" or "getLayout" or "layout:" in route/page files
  2. FILTER framework artifacts:
     - Next.js: layout.tsx / layout.js are App Router artifacts, NOT reusable layout components
       → Record as "App Router layout" but don't count as a composable layout component
     - Nuxt: layouts/ directory contains framework layouts → record as-is
     - SvelteKit: +layout.svelte → record as-is
  3. Find navigation components:
     - Glob: **/*Nav*.{vue,tsx,jsx} or **/*Sidebar*.{vue,tsx,jsx}  --ignore node_modules
  4. Cross-reference with layout-patterns.md:
     - Does the project use Sidebar + Content? Top Nav? Tabs?
  5. Note which pages use which layouts
```

### Scan E: Import Patterns

```
Detect from codebase:
  1. Read 5-10 page/component files
  2. Extract import patterns:
     - Alias: @/ or ~/ or $/
     - Relative: ../
     - Barrel: import { X } from '@/components'
     - Direct: import X from '@/components/X/X.vue'
  3. Detect routing pattern:
     - File-based (pages/ directory)
     - Config-based (router.ts or routes.ts)
```

## Step 2: Generate Conventions File

1. Merge all scan results
2. Fill `references/templates/conventions.md` template
3. Save to `ds/conventions.md`

**Init folders if needed:**
```
ds/ exists?
  NO → Create ds/
ds/interfaces/active/ exists?
  NO → Create ds/interfaces/active/, ds/interfaces/shipped/, ds/interfaces/dropped/
```

## Step 3: Output

Follow `references/templates/outputs/init-output.md`.

## Rules

- **Parallel execution:** All 5 scans run simultaneously — never sequential
- **Convention detection, not opinion:** Report what the project uses, don't recommend changes
- **Stable DNA only:** If something could change weekly (component count, route list), don't persist it
- **Fallback:** If a scan finds nothing (e.g., no tokens), note "none detected" — don't skip the section
- **Existing conventions:** If re-running, overwrite with fresh scan results (previous file is replaced)
- **Speed:** Target < 30 seconds. Filter scans by file type to avoid scanning node_modules, dist, etc.
- **Exclusion list:** ALWAYS exclude from all globs/greps: `node_modules/`, `dist/`, `.next/`, `.nuxt/`, `.output/`, `build/`, `.cache/`, `coverage/`
- **Multi-root components:** If components live in 2+ directories, record ALL roots — never flatten to a single path
