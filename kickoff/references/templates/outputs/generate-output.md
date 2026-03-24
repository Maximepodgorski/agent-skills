# Generate Output Template

> **Agent:** Show this message after `generate` completes.

## Template

```markdown
## CLAUDE.md Generated

**Target:** {CLAUDE.md | .cursorrules | .windsurfrules}
**Lines:** {N} / 200
**Sections:** {list of sections included}
**Mode:** {create | merge}

{If merge mode:}
**Preserved:** {N} custom sections from existing file
**Updated:** {N} generated sections replaced

{If TBDs exist in context:}
**Note:** {N} items marked [TBD] in generated file. Run `kickoff update {axis}` to resolve.

{If context is thin:}
**Warning:** Context is minimal ({N} axes, {N} docs). Consider `kickoff init` for a full kickoff.

Next:
  -> `kickoff review`           Check for drift periodically
  -> `kickoff update {axis}`    Refine specific context
  -> `kickoff analyze`          Challenge context quality
```
