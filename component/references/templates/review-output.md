# Review Output Template

Edit this file to customize what the agent returns in chat after `review`. This is the status summary — not the full findings report.

---

## Success

```markdown
## Review: {ComponentName}

**Verdict:** {COMPLIANT | NEEDS WORK | NON-COMPLIANT}

| Principle | Result | Issues |
|-----------|--------|--------|
| Composition | {PASS/WARN/FAIL/N/A} | {N} |
| Naming | {PASS/WARN/FAIL/N/A} | {N} |
| Tokens | {PASS/WARN/FAIL/N/A} | {N} |
| Props API | {PASS/WARN/FAIL/N/A} | {N} |
| Accessibility | {PASS/WARN/FAIL/N/A} | {N} |

**Findings:** {N} critical | {N} warnings | {N} info

{If critical > 0, list top 3:}
**Top critical:**
  1. `{file}:{line}` — {finding} → Fix: {fix}
  2. `{file}:{line}` — {finding} → Fix: {fix}
  3. `{file}:{line}` — {finding} → Fix: {fix}

Next:
  → Fix {N} critical issues
  → /component dev {path}    (re-implement with fixes)
  → /component audit {dir}   (system-wide check)
```

---

## Clean

```markdown
## Review: {ComponentName} — COMPLIANT

All 5 principles passing. 0 critical, {N} warnings, {N} info.

{If warnings > 0:}
**Warnings:** {list one-liners}
```

---

## Output Rules

- **Chat = summary only** — the full findings table stays in the review action logic, not in the chat output
- **Top 3 critical** — don't dump all findings, show the most impactful
- **Verdict is the headline** — the user should know pass/fail in 1 second
- **file:line always** — user must be able to jump to the location
