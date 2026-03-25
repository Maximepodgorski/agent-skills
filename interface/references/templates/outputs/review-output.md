# Review Output Template

> Chat message returned after `/interface review` completes.

---

## Review Complete

```markdown
## Review: {screen name}

**Verdict: {COMPLIANT | NEEDS WORK | NON-COMPLIANT}**

### Summary

| Principle | Result | Critical | Warning |
|-----------|--------|----------|---------|
| Reuse compliance | {PASS/WARN/FAIL} | {N} | {N} |
| Layout consistency | {PASS/WARN/FAIL} | {N} | {N} |
| Component usage | {PASS/WARN/FAIL} | {N} | {N} |
| Page states | {PASS/WARN/FAIL/N/A} | {N} | {N} |
| Responsive | {PASS/WARN/FAIL} | {N} | {N} |
| Accessibility | {PASS/WARN/FAIL} | {N} | {N} |

{If CRITICAL findings > 0:}
### Critical Findings

| # | Principle | Finding | Location | Fix |
|---|-----------|---------|----------|-----|
| 1 | {principle} | {finding} | {file:line} | {fix} |

{If WARNING findings > 0:}
### Warnings

| # | Principle | Finding | Location | Fix |
|---|-----------|---------|----------|-----|

Next:
  {If COMPLIANT:}       /workflow done    (validate + archive)
  {If NEEDS WORK:}      Fix {N} warnings, then /interface review
  {If NON-COMPLIANT:}   Fix {N} critical issues, then /interface review
```

---

## Error

```markdown
## Review: Failed

**Reason:** {No built interface found / No spec to compare against / ...}
**Action:** Run `/interface ship` first.
```
