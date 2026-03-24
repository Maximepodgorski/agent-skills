# Analyze Output Template

> **Agent:** Show this message after `analyze` completes.

## Template

```markdown
## Context Analysis

**Verdict:** {COHERENT | COHERENT WITH NOTES | REVIEW RECOMMENDED | NEEDS ATTENTION}
**Perspectives:** 4 | **Findings:** {N} total ({N} critical, {N} high, {N} medium)

### Critical Findings
{If any:}
| # | Finding | Axis | Consensus |
|---|---------|------|-----------|
| 1 | {finding} | {axis} | {Y/N — flagged by 2+ perspectives} |
| 2 | {finding} | {axis} | {Y/N} |

### High Findings
{If any:}
| # | Finding | Axis | Suggested Action |
|---|---------|------|------------------|
| 1 | {finding} | {axis} | {action} |

{If NEEDS ATTENTION:}
**Recommendation:** Address critical findings before building. Run `kickoff update {axis}` for affected axes.

{If COHERENT:}
**Context is solid.** No critical gaps found.

Next:
  -> `kickoff update {axis}`    Address specific findings
  -> `kickoff review`           Check codebase drift
  -> `kickoff generate`         Regenerate CLAUDE.md
```
