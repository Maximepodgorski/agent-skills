# Spec-Review Action

> **Agent:** Load this file when `spec-review` triggers. Also load the active interface spec from `ds/interfaces/active/`.

Deep adversarial analysis of an interface spec. Dynamic perspective selection based on screen type, parallel sub-agents, action-driven findings.

**Triggers:** "spec-review", "review spec", "challenge spec"

---

## Step 1: Load Spec + Context

```
1. Find active spec in ds/interfaces/active/
   - If multiple: ask user which one
   - If none: error → "No active spec found. Run /interface spec first."
2. Read the full spec
3. Read ds/conventions.md (project DNA)
4. Identify screen type from spec's Context section
```

## Step 2: Select Perspectives (Dynamic)

Read the spec's screen type, component coverage, layout complexity, and data flow. Autonomously select 3-6 expert perspectives most relevant to THIS spec.

### Selection by Screen Type

| Screen Type | Perspectives |
|-------------|-------------|
| Dashboard | Data Visualization Expert, UX Designer (information density), Performance Engineer, Skeptic |
| Form / Settings | Form UX Expert (validation, progressive disclosure), DS Consistency Reviewer, Frontend Engineer, Skeptic |
| List / Table | Data UX Expert (pagination, filtering, empty states), Performance Engineer, Accessibility Specialist, Skeptic |
| Mobile screen | Mobile Specialist (touch targets, gestures, safe areas), UX Designer (thumb zones), Frontend Engineer, Skeptic |
| Landing / Marketing | UX Designer (visual hierarchy, CTA), Performance Engineer (LCP, above-the-fold), Conversion Specialist, Skeptic |
| Auth / Onboarding | UX Designer (friction, drop-off), Security Engineer (input validation), Accessibility Specialist, Skeptic |

### Selection Rules

- Min 3, max 6 perspectives
- Always include **1 Skeptic** (domain-agnostic adversarial thinker)
- Prefer specialists over generalists
- If screen type is ambiguous, add perspectives for both interpretations
- Present selected perspectives to user before launching

## Step 3: Launch Parallel Analysis

**Launch ALL perspectives as parallel sub-agents** (single message, multiple Task tool calls).

Each sub-agent receives a role-specific brief + the shared prompt below.

### Role-Specific Briefs

| Role | Brief Additions |
|------|----------------|
| Data Visualization Expert | Focus on: chart type appropriateness, data density, update frequency, render performance, accessibility of data (alt text, ARIA), color-blind safe palettes |
| UX Designer | Focus on: information hierarchy, cognitive load, Fitts's law (target sizes), Hick's law (choice count), progressive disclosure, error recovery paths |
| Form UX Expert | Focus on: validation timing (inline vs submit), field grouping, progressive disclosure, autofill support, error message placement, multi-step form state |
| Performance Engineer | Focus on: LCP elements, bundle size impact, lazy loading opportunities, render-blocking resources, data fetching waterfalls, list virtualization |
| DS Consistency Reviewer | Focus on: naming consistency with existing components, token usage, spacing scale adherence, variant completeness, compound component patterns |
| Frontend Engineer | Focus on: implementation feasibility, state management complexity, prop drilling depth, component composition, edge cases in data flow |
| Accessibility Specialist | Focus on: WCAG 2.1 AA, keyboard nav, screen reader announcement order, focus trapping in modals, dynamic content live regions, heading hierarchy |
| Mobile Specialist | Focus on: touch targets (44x44px min), thumb zones, safe areas, gesture conflicts, offline capability, viewport units |
| Security Engineer | Focus on: input sanitization, XSS vectors, CSRF on forms, auth state handling, sensitive data in URL params |
| Skeptic | Focus on: over-engineering, unnecessary complexity, simpler alternatives, scope creep, assumptions treated as facts |
| Conversion Specialist | Focus on: CTA hierarchy, friction points, above-the-fold content, loading speed impact on conversion, trust signals |

### Shared Prompt

```
You are a {Role}. Review this interface spec adversarially.

{Role-specific brief from table above}

SPEC:
{full spec content}

CONTEXT:
{conventions.md content}

Answer these 4 questions:

1. ASSUMPTIONS: What's assumed that could be wrong?
   | Assumption | Evidence For | Evidence Against | Verdict |
   |------------|-------------|------------------|---------|
   (min 2 per perspective)

2. BLIND SPOTS: What hasn't been considered?
   - 2-3 items with [category] + why it matters

3. FAILURE HYPOTHESES:
   | IF | THEN | BECAUSE | Severity |
   (1-3 per perspective)

4. THE REAL QUESTION: Is this solving the right problem from your perspective?

Every finding must end with action: → update spec / → explore / → question / → no action
```

## Step 4: Synthesize

1. Collect all perspective outputs
2. Deduplicate findings:
   - Same finding from 2+ perspectives → merge, keep highest severity
   - Conflicting severity → use the higher one + note the disagreement
   - Same theme, different specifics → group under theme, list specifics
3. Rank by severity: CRITICAL → HIGH → MEDIUM → LOW
4. Aggregate into structured report

### Severity Classification

| Severity | Meaning | Action Required |
|----------|---------|-----------------|
| CRITICAL | Spec is fundamentally flawed or missing critical aspect | Must fix before ship |
| HIGH | Significant gap that will cause issues | Should fix before ship |
| MEDIUM | Non-trivial concern worth addressing | Fix during ship or note |
| LOW | Minor observation | Log for future reference |

## Step 5: Verdict

```
ALL perspectives positive, no HIGH severity findings
  → APPROVED

Any CRITICAL finding
  → BLOCKED (resolve before ship)

No CRITICAL but >3 HIGH findings
  → NEEDS REVISION (fix spec, re-review max 2 rounds)

Otherwise
  → APPROVED WITH NOTES
```

## Step 6: Output

Follow `references/templates/outputs/spec-review-output.md`.

## Step 7: Merge (Optional)

If user says "merge" or "apply":

```
1. Update the spec's Decisions section with review insights
2. Add new ACs if findings warrant them
3. Add edge cases surfaced by review
4. Update Missing Components if new gaps found
5. Add note: "Spec review applied: {date}"
```

## Rules

- **Standalone:** No dependency on analyze skill or any external tool
- **Action-driven:** Every finding must force a decision (update spec / explore / question / no action)
- **Parallel execution:** All perspectives run simultaneously (single message, multiple Task calls)
- **No passive observations:** "This could be a problem" is not allowed. "IF X THEN Y → update spec" is.
- **Max 2 re-review rounds:** If NEEDS REVISION, fix and re-review max twice. After that, user decides.
- **Present perspectives first:** User sees which perspectives were selected before agents launch
