# Review Action

> **Agent:** Load this file when `review` triggers. Also load all files in `references/principles/` for compliance checks.

Review a component against DS principles. Input: file path or Figma link. Output: compliance report.

---

## Input Detection

```
Input contains figma.com URL?
  YES → Figma review mode (design compliance)
  NO  → Code review mode (implementation compliance)
  BOTH (path + figma link) → Full review (code vs design)
```

## Step 0: Load Component

### Code Review Mode
1. Read all files in the component directory
2. Extract: props, slots, events, expose, styles, tokens used
3. Read related test file (if exists)
4. Read `doc.md` (if exists)

### Figma Review Mode
1. Call `get_design_context(fileKey, nodeId)`
2. Extract: component hierarchy, props, variants, tokens, styling

### Full Review Mode
1. Load both code and Figma context
2. Prepare comparison: design intent vs implementation

## Step 1: Scan Project Tokens

Run the token scan protocol (see SKILL.md → Token Scan Protocol) to know what tokens exist in the project.

## Step 2: Run Compliance Checks

Check the component against all 5 principles. Load each principle file for detailed rules.

### Check 1: Composition (references/principles/composition.md)

```
[ ] Component classified by Atomic Design level (atom/molecule/organism)
[ ] Level is appropriate for component's complexity
[ ] Compound components use correct pattern (if applicable)
[ ] Children are composed via slots/children, not hardcoded
[ ] Component is reusable — no business logic embedded
[ ] Single responsibility — does one thing well
```

### Check 2: Naming (references/principles/naming.md)

```
[ ] Component name: PascalCase, descriptive, no abbreviations
[ ] CSS classes: follow project's convention (BEM or project pattern)
[ ] CSS variables: follow project's token naming
[ ] Props: camelCase, boolean props are adjectives (disabled, loading)
[ ] Events: past tense or present (changed, update:modelValue)
[ ] File names: match project convention
```

### Check 3: Tokens (references/principles/tokens.md)

```
[ ] ZERO hardcoded color values (no #hex, no rgb(), no hsl())
[ ] ZERO hardcoded spacing values (no px, no rem, no em literals)
[ ] ZERO hardcoded typography values (no font-size: 14px)
[ ] ZERO hardcoded radius values (no border-radius: 4px)
[ ] ZERO hardcoded shadow values
[ ] All values reference project tokens
[ ] Token naming matches project convention
[ ] Semantic tokens used over primitive (--bg-surface not --gray-100)
```

**Any hardcoded value = CRITICAL finding.**

### Check 4: Props API (references/principles/props-api.md)

```
[ ] Minimal props — no prop does what a slot could do
[ ] Smart defaults — 80% use case works with defaults
[ ] Strict types — union types over generic string
[ ] Required props are truly required (no sensible default possible)
[ ] No boolean traps (props that change meaning based on combination)
[ ] Events are predictable (consistent payload shape)
[ ] Expose is minimal (only what consumers need)
```

### Check 5: Accessibility (references/principles/accessibility.md)

```
[ ] Semantic HTML (button for button, not div with onClick)
[ ] Keyboard navigable (Tab, Enter, Escape, Arrow keys as appropriate)
[ ] Focus management (focus visible, focus trap for modals/dropdowns)
[ ] ARIA attributes (role, aria-label, aria-expanded, etc.)
[ ] Screen reader tested (meaningful announcements)
[ ] Color contrast (WCAG 2.1 AA — 4.5:1 text, 3:1 large text)
[ ] Motion respects prefers-reduced-motion
```

## Step 3: Figma Comparison (Full Review Mode only)

When both code and Figma context are available:

```
[ ] Component hierarchy matches Figma structure
[ ] All Figma variants are implemented
[ ] Figma prop names match code prop names (or mapping is documented)
[ ] Figma token values map to project tokens used in code
[ ] No Figma values lost in translation (every design decision reflected)
```

## Step 4: Generate Report

### Severity Levels

| Level | Meaning | Example |
|-------|---------|---------|
| CRITICAL | Must fix — blocks DS compliance | Hardcoded hex value, missing keyboard nav |
| WARNING | Should fix — impacts quality | Naming inconsistency, missing default |
| INFO | Suggestion — improves quality | Could use semantic token instead of primitive |

### Report Format

```
## Component Review: {ComponentName}

**Input:** {file path} {+ figma link if provided}
**Stack:** {detected framework + styling approach}
**Tokens scanned:** {N} tokens in project

### Summary

| Principle | Result | Critical | Warning | Info |
|-----------|--------|----------|---------|------|
| Composition | PASS/WARN/FAIL/N/A | {N} | {N} | {N} |
| Naming | PASS/WARN/FAIL/N/A | {N} | {N} | {N} |
| Tokens | PASS/WARN/FAIL/N/A | {N} | {N} | {N} |
| Props API | PASS/WARN/FAIL/N/A | {N} | {N} | {N} |
| Accessibility | PASS/WARN/FAIL/N/A | {N} | {N} | {N} |

**Verdict:** {COMPLIANT / NEEDS WORK / NON-COMPLIANT}

### Findings

#### Critical
| # | Principle | Finding | Location | Fix |
|---|-----------|---------|----------|-----|
| 1 | Tokens | Hardcoded #1A1A1A | line 42 | Use --color-neutral-900 |

#### Warning
| # | Principle | Finding | Location | Fix |
|---|-----------|---------|----------|-----|

#### Info
| # | Principle | Finding | Location | Fix |
|---|-----------|---------|----------|-----|

### Next
  → Fix {N} critical issues
  → /component dev {path}   (re-implement with fixes)
  → /component audit {dir}  (system-wide check)
```

### Chat Output

The report format above is the internal structure. For the chat message returned to the user, follow `references/templates/review-output.md` — a concise summary with verdict, counts, and top 3 critical findings only.

## Verdict Rules

```
N/A principles are excluded from verdict calculation.

CRITICAL count = 0 AND WARNING count <= 2  → COMPLIANT
CRITICAL count = 0 AND WARNING count > 2   → NEEDS WORK
CRITICAL count > 0                         → NON-COMPLIANT
```

**N/A rule:** Mark a principle N/A when the component genuinely doesn't involve that concern (e.g., Tokens N/A for a pure logic wrapper, Accessibility N/A for a non-rendered utility). Never use N/A to skip a principle that's hard to check.

## Rules

- Load ALL 5 principle files before reviewing — never skip a principle (mark N/A only if genuinely not applicable)
- Every finding must include: principle violated, location (file:line), concrete fix
- Hardcoded values are ALWAYS CRITICAL — no exceptions
- Don't review business logic — this is DS compliance only
- If comparing against Figma, flag design-to-code drift specifically
