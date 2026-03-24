# Review Output Template

> **Agent:** Show this message after `review` completes.

## Template

```markdown
## Context Review

**Checks:** {N} passed | {N} drift | {N} skipped
**Freshness:** {N} fresh | {N} aging | {N} stale
**CLAUDE.md sync:** {OK | DESYNC | SKIP}

### Drift Detected
{If any:}
| Severity | Doc | States | Actual | Evidence |
|----------|-----|--------|--------|----------|
| {CRITICAL/HIGH/MEDIUM} | {doc}.md | "{stated value}" | {actual value} | `{file path}` |

{If DESYNC:}
### CLAUDE.md Desync

`.context/` was updated on {date} but CLAUDE.md was last generated on {date}.
Run `kickoff generate` to sync.

### Freshness
| Axis | Last Updated | Status | Commits Since |
|------|-------------|--------|---------------|
| {axis} | {date} | {FRESH/AGING/STALE} | {N} |

{If git activity warnings:}
**Git activity:** {N} commits and {N} files changed since last context update.

{If drift found:}
**Recommendation:**
- `kickoff update engineering` — stack.md has {N} drift issues
- `kickoff update product` — features.md is {N} days old

{If no drift:}
**All clear.** Context docs match codebase. No action needed.

Next:
  -> `kickoff update {axis}`    Fix drift in specific axis
  -> `kickoff generate`         Regenerate CLAUDE.md after fixes
```
