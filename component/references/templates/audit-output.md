# Audit Output Template

Edit this file to customize what the agent returns in chat after `audit`. The full report is saved to `ds/audits/active/` — this is just the status summary.

---

## Codebase Audit

```markdown
## Audit: {scope}

**Verdict:** {CLEAN | NEEDS WORK | NON-COMPLIANT}
**Report:** `ds/audits/active/{filename}.md`

| Metric | Value |
|--------|-------|
| Components scanned | {N} |
| Critical | {N} |
| Warnings | {N} |
| Info | {N} |
| Compliance | {N}% ({compliant}/{total}) |

{If critical > 0, list top 3:}
**Top critical:**
  1. {component} — {finding} → {suggested token}
  2. {component} — {finding} → {suggested token}
  3. {component} — {finding} → {suggested token}

Next:
  → Review full report: `ds/audits/active/{filename}.md`
  → /component review {path}   (detailed check on critical components)
```

---

## Figma Audit

```markdown
## Audit: {ComponentName} (Figma)

**Verdict:** {CLEAN | NEEDS WORK | NON-COMPLIANT}
**Report:** `ds/audits/active/{filename}.md`

**Naming:** {N} issues | **Tokens:** {N} issues | **Values:** {N} hardcoded

{If non-compliant:}
**Top issues:**
  1. {issue} ({severity})
  2. {issue} ({severity})
  3. {issue} ({severity})

Next:
  → Share report with designer for alignment
  → /component structure {name}   (create spec from corrected design)
```

---

## Output Rules

- **Chat = verdict + counts** — the full report is in the file
- **Top 3 only** — don't dump all findings in chat
- **Report path always visible** — user needs to find the full report
- **Mode-specific next steps** — Figma → designer, Codebase → review
