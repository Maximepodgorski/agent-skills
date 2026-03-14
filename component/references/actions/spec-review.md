# Spec-Review Action

> **Agent:** Load this file when `spec-review` triggers. Launch 4 parallel subagents, one per perspective. Consolidate results into a single report.

Review a component spec from 4 expert perspectives in parallel. Input: spec path or component name. Output: consolidated multi-perspective review.

---

## Input Detection

```
Input is a file path to a spec?
  YES → Read spec directly
  NO  → Search ds/specs/active/{name}-spec.md
    FOUND → Read spec
    NOT FOUND → Error: "No spec found. Run /component spec {name} first."
```

## Execution Model

**Launch 4 subagents in parallel** (use Task tool with `run_in_background` or parallel calls). Each subagent receives the full spec content and reviews from its assigned perspective.

```
┌─────────────────────────────────────────────────────────────┐
│                    Spec-Review                              │
│                                                             │
│  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────────┐   │
│  │  Front    │ │   DS     │ │  A11y    │ │  Product     │   │
│  │ Engineer  │ │ Manager  │ │Specialist│ │  Designer    │   │
│  └─────┬────┘ └─────┬────┘ └─────┬────┘ └──────┬───────┘   │
│        │            │            │              │           │
│        └────────────┴────────────┴──────────────┘           │
│                         │                                   │
│                   Consolidation                             │
└─────────────────────────────────────────────────────────────┘
```

## Perspective 1: Front Engineer

**Persona:** Senior frontend engineer who will implement this spec.

**Review focus:**
- [ ] Is the spec implementable as-is? Any ambiguity that would block dev?
- [ ] Are props types precise enough? (no `string` when a union type is needed)
- [ ] Are defaults sensible for the 80% use case?
- [ ] Is the component tree realistic? (no over-nesting, no missing wrappers)
- [ ] Are edge cases covered? (empty, overflow, loading, error, rapid interaction)
- [ ] Is the file structure consistent with project conventions?
- [ ] Are events and expose minimal and well-defined?
- [ ] Is the spec testable? Can acceptance criteria be turned into unit/integration tests?

**Output format:**
```
### Front Engineer

**Verdict:** READY / NEEDS WORK / BLOCKED

**Strengths:**
- {what's well-specified}

**Issues:**
| # | Severity | Finding | Suggestion |
|---|----------|---------|------------|
| 1 | {CRITICAL/WARNING/INFO} | {issue} | {fix} |

**Questions for spec author:**
- {open question that blocks implementation}
```

## Perspective 2: Design System Manager

**Persona:** DS lead responsible for consistency, naming, tokens, and composition across the entire system.

**Review focus:**
- [ ] Does the atomic level make sense? (not an organism disguised as an atom)
- [ ] Is naming consistent with existing DS components?
- [ ] Are all tokens mapped? Any MISSING tokens flagged?
- [ ] Does the component compose well with existing DS components?
- [ ] Is the API surface consistent with similar components in the DS?
- [ ] Is the component pattern (single/compound) appropriate for its complexity?
- [ ] Are there decisions that should be system-level, not component-level?
- [ ] Will this component create naming conflicts or API inconsistencies?

**Output format:**
```
### Design System Manager

**Verdict:** CONSISTENT / NEEDS ALIGNMENT / INCONSISTENT

**DS compliance:**
- Atomic level: {CORRECT / WRONG — should be {level}}
- Naming: {CONSISTENT / INCONSISTENT — {details}}
- Tokens: {ALL MAPPED / {N} MISSING}
- Composition: {GOOD / CONCERNS — {details}}

**Issues:**
| # | Severity | Finding | Suggestion |
|---|----------|---------|------------|
| 1 | {CRITICAL/WARNING/INFO} | {issue} | {fix} |

**DS-level recommendations:**
- {recommendations that affect the system, not just this component}
```

## Perspective 3: Accessibility Specialist

**Persona:** a11y expert ensuring WCAG 2.1 AA compliance and inclusive UX.

**Review focus:**
- [ ] Is semantic HTML specified? (no `div` when `button`, `nav`, `dialog` is needed)
- [ ] Is keyboard navigation fully defined? (Tab, Enter, Escape, Arrows)
- [ ] Is focus management specified? (focus trap for overlays, focus restoration)
- [ ] Are ARIA attributes listed? (role, aria-label, aria-expanded, aria-controls)
- [ ] Are screen reader announcements described?
- [ ] Is color contrast addressed? (WCAG 2.1 AA: 4.5:1 text, 3:1 large/UI)
- [ ] Is motion/animation handled? (prefers-reduced-motion)
- [ ] Are touch targets sufficient? (44x44px minimum)
- [ ] Are disabled/loading/error states accessible?

**Output format:**
```
### Accessibility Specialist

**Verdict:** ACCESSIBLE / GAPS / NON-COMPLIANT

**WCAG coverage:**
- Keyboard: {COMPLETE / INCOMPLETE — {missing}}
- ARIA: {COMPLETE / INCOMPLETE — {missing}}
- Screen reader: {SPECIFIED / UNSPECIFIED}
- Contrast: {ADDRESSED / NOT ADDRESSED}
- Motion: {ADDRESSED / NOT ADDRESSED}

**Issues:**
| # | Severity | WCAG Criterion | Finding | Suggestion |
|---|----------|----------------|---------|------------|
| 1 | {CRITICAL/WARNING/INFO} | {e.g. 2.1.1 Keyboard} | {issue} | {fix} |

**Missing from spec:**
- {a11y requirements that should be added to the spec}
```

## Perspective 4: Product Designer

**Persona:** UX designer who created the Figma design, reviewing whether the spec captures design intent.

**Review focus:**
- [ ] Does the user story capture the right user need?
- [ ] Are all visual states covered? (default, hover, focus, active, disabled, loading, error)
- [ ] Are transitions/animations specified where the design implies them?
- [ ] Are responsive behaviors defined? (mobile, tablet, desktop)
- [ ] Does the component handle real content? (long text, truncation, empty states)
- [ ] Is the component flexible enough for different contexts?
- [ ] Are spacing and layout relationships clear? (not just values, but relationships)
- [ ] Does the spec preserve the design intent or lose nuance?

**Output format:**
```
### Product Designer

**Verdict:** FAITHFUL / DRIFT / MISSING INTENT

**Design coverage:**
- States: {ALL / MISSING: {list}}
- Responsive: {SPECIFIED / UNSPECIFIED}
- Content flexibility: {GOOD / RIGID — {details}}
- Transitions: {SPECIFIED / UNSPECIFIED}

**Issues:**
| # | Severity | Finding | Suggestion |
|---|----------|---------|------------|
| 1 | {CRITICAL/WARNING/INFO} | {issue} | {fix} |

**Design intent at risk:**
- {nuances from the design that the spec might lose}
```

## Consolidation

After all 4 perspectives return, consolidate:

### Step 1: Merge findings

Combine all issues from 4 perspectives. Deduplicate (same issue flagged by multiple perspectives → keep highest severity, note all perspectives that flagged it).

### Step 2: Cross-perspective conflicts

```
Any two perspectives contradict?
  YES → Flag as "CONFLICT: {perspective A} says {X}, {perspective B} says {Y}"
        → Do NOT resolve — present both to user
  NO  → Continue
```

### Step 3: Generate consolidated report

```
Overall verdict:
  ALL 4 positive (READY + CONSISTENT + ACCESSIBLE + FAITHFUL) → APPROVED
  Any CRITICAL finding                                         → BLOCKED
  No CRITICAL but > 3 WARNING                                  → NEEDS REVISION
  Otherwise                                                    → APPROVED WITH NOTES
```

## Output

Follow the output template in `references/templates/spec-review-output.md`.

## Rules

- All 4 perspectives are MANDATORY — never skip one
- Launch perspectives in PARALLEL for speed — do not run sequentially
- Each perspective reviews independently — no cross-contamination
- Conflicts between perspectives are flagged, not resolved by the agent
- The consolidated verdict is mechanical (based on finding counts), not subjective
- Spec-review does NOT modify the spec — it produces a review report only
