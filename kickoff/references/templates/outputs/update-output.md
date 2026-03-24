# Update Output Template

> **Agent:** Show this message after `update` completes.

## Template

```markdown
## Context Updated: {Axis}

**Changed:** {list of docs updated}
**Date:** {today}

Changes:
- {change 1}
- {change 2}
{If new decision captured:}
- New decision: {NNN} — {title}

Next:
  -> `kickoff generate`     Regenerate CLAUDE.md with updated context
  -> `kickoff review`       Check all axes for drift
```
