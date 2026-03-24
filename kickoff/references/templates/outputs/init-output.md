# Init Output Template

> **Agent:** Show this message after `init` completes successfully.

## Template

```markdown
## Context Kickoff Complete

**Project:** {project name}
**Axes:** {N} active | **Docs:** {N} files | **Decisions:** {N} captured

| Axis | Status | Docs |
|------|--------|------|
| Product | {complete/partial} | vision.md, users.md, features.md |
| Engineering | {complete/partial} | stack.md, architecture.md, conventions.md |
| Decisions | {N} records | {list} |
{other axes if active}

{If TODO.md has questions:}
**Open questions ({N}):** saved to `.context/TODO.md`
- [{axis}] {question} ({N} more...)
- [{axis}] {question}

**Saved to:** `.context/`

Next:
  -> `kickoff generate`              Generate CLAUDE.md from context
  -> `kickoff todo`                  Answer open questions to enrich context
  -> `kickoff analyze`               Challenge context for gaps
  -> `kickoff update {axis}`         Refine a specific axis
```

## Error states

```markdown
## Context Kickoff — Interrupted

**Completed axes:** {list}
**Remaining:** {list}

Saved progress to `.context/`. Resume with `kickoff init` — completed axes will be skipped.
```
