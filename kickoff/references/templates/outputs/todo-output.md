# Todo Output Template

> **Agent:** Show this message after `todo` completes.

## Template

```markdown
## Todo Review Complete

**Answered:** {N} questions across {M} axes
**Remaining:** {N} questions
**Files updated:** {list of .context/ files modified}

{If remaining > 0:}
Run `kickoff todo` to continue later.

{If remaining == 0:}
All questions answered. Run `kickoff generate` to regenerate CLAUDE.md.

Next:
  -> `kickoff todo`                  Continue answering open questions
  -> `kickoff generate`              Regenerate CLAUDE.md with enriched context
  -> `kickoff analyze`               Challenge context quality
```
