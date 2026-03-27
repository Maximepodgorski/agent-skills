# Spec-Review Output Template

> Chat message returned after `/design-screen spec-review` completes.

---

## Perspectives Selected

```markdown
## Spec Review: {screen name}

**Perspectives selected ({N}):**
{For each:}
  - {Role} — {focus area}

Launching {N} parallel reviewers...
```

---

## Review Complete

```markdown
## Spec Review: {screen name}

**Verdict: {APPROVED | APPROVED WITH NOTES | NEEDS REVISION | BLOCKED}**
**Perspectives:** {N} | **Findings:** {N} total
**Critical:** {N} | **High:** {N} | **Medium:** {N} | **Low:** {N}

### Top Findings

{For each CRITICAL + HIGH finding:}
| # | Perspective | Finding | Action |
|---|------------|---------|--------|
| 1 | {role} | {finding} | → {update spec / explore / question} |

{If APPROVED or APPROVED WITH NOTES:}
### Notes
{List of non-blocking observations}

{If NEEDS REVISION:}
### Required Changes
{List of changes needed before ship}
Recommended: fix these, then re-run `/design-screen spec-review`

{If BLOCKED:}
### Blockers
{List of critical issues that must be resolved}
Action: resolve blockers, update spec, re-run `/design-screen spec-review`

---

Next:
  {If APPROVED:}     /design-screen ship           (start implementation)
  {If WITH NOTES:}   /design-screen ship           (start implementation — notes logged)
  {If NEEDS REVISION:} Edit spec, then /design-screen spec-review
  {If BLOCKED:}      Resolve blockers, then /design-screen spec-review

  → "merge" or "apply" to update the spec with findings
```

---

## Error

```markdown
## Spec Review: Failed

**Reason:** {No active spec found / Spec path invalid / ...}
**Action:** Run `/design-screen spec "{name}"` first.
```
