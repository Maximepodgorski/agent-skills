# Spec-Review Output Template

Edit this file to customize what the agent returns in chat after `spec-review`. This is the consolidated summary — individual perspective details are in the report.

---

## Success

```markdown
## Spec-Review: {ComponentName}

**Verdict:** {APPROVED | APPROVED WITH NOTES | NEEDS REVISION | BLOCKED}

| Perspective | Verdict | Critical | Warning | Info |
|-------------|---------|----------|---------|------|
| Front Engineer | {READY/NEEDS WORK/BLOCKED} | {N} | {N} | {N} |
| DS Manager | {CONSISTENT/NEEDS ALIGNMENT/INCONSISTENT} | {N} | {N} | {N} |
| Accessibility | {ACCESSIBLE/GAPS/NON-COMPLIANT} | {N} | {N} | {N} |
| Product Designer | {FAITHFUL/DRIFT/MISSING INTENT} | {N} | {N} | {N} |

**Findings:** {N} critical | {N} warnings | {N} info

{If critical > 0, list all critical:}
**Critical:**
  1. [{perspective}] {finding} → {suggestion}
  2. [{perspective}] {finding} → {suggestion}

{If conflicts > 0:}
**Conflicts:**
  - {perspective A} vs {perspective B}: {description}

Next:
  → Fix critical issues in the spec
  → /component spec {name}     (regenerate spec)
  → /component doc {name}      (generate documentation)
  → /component dev {name}      (start implementation)
```

---

## Clean (all 4 positive)

```markdown
## Spec-Review: {ComponentName} — APPROVED

All 4 perspectives passing. 0 critical, {N} warnings, {N} info.

{If warnings > 0:}
**Warnings:** {list one-liners}

Next:
  → /component doc {name}      (generate documentation)
  → /component dev {name}      (start implementation)
```

---

## Error

```markdown
## Spec-Review: Failed

**Reason:** {No spec found / ...}
**Action:** Run /component spec {name} first
```

---

## Output Rules

- **Chat = consolidated summary only** — individual perspective details stay in the action logic
- **All critical findings listed** — don't truncate critical issues
- **Conflicts always surfaced** — user must resolve, not the agent
- **Verdict is the headline** — user should know pass/fail in 1 second
