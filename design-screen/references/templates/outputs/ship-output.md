# Ship Output Template

> Chat messages returned during and after `/design-screen ship`.

---

## Iteration Progress

```markdown
## Ship: {screen name} — Iteration {N}/{max}

**Scope item:** {item description}
**Status:** {implementing | validating | reviewing}
**Progress:** {completed}/{total} items ({N}%)

{Brief description of what was done this iteration}

{If issues found:}
**Issues:** {N} blocking | {N} warnings
  - {issue 1} (blocking) → fixing
  - {issue 2} (warning) → logged
```

---

## Exit: Clean

```markdown
## Ship: {screen name} — Complete

**Iterations:** {N}
**Scope items:** {completed}/{total} ({percentage}%)
**Quality:** all checks passed

### Implemented

| # | Scope Item | Iteration |
|---|-----------|-----------|
| 1 | {item} | {N} |
| 2 | {item} | {N} |

{If warnings logged:}
### Warnings (non-blocking)
  - {warning 1}
  - {warning 2}

{If bugs encountered:}
### Bugs
  - BUG-{N}: {summary} — {Fixed/Deferred}

Next:
  /design-screen review    (compliance check)
  /workflow done       (validate + archive)
```

---

## Exit: Partial

```markdown
## Ship: {screen name} — Partial

**Iterations:** {N}
**Scope items:** {completed}/{total} ({percentage}%)
**Remaining:** {list of uncompleted items}
**Reason:** {ACs failing / quality gate issues / component gap}

### Completed
{list}

### Remaining
{list with blockers}

Next:
  /design-screen ship      (resume — picks up from last checkpoint)
  Reduce scope         (cut remaining items)
```

---

## Exit: Stuck

```markdown
## Ship: {screen name} — Stuck

**Iterations:** {N} (threshold: {stuck_threshold})
**Progress:** {completed}/{total} ({percentage}%)
**Blocker:** {description of what's blocking progress}

Options:
  1. Continue with different approach
  2. Reduce scope (cut {N} items)
  3. Defer to next session
  4. Drop and rethink
```

---

## Exit: Hard Stop

```markdown
## Ship: {screen name} — Max Iterations

**Iterations:** {max_iterations} reached
**Progress:** {completed}/{total} ({percentage}%)

### Completed
{list}

### Not Completed
{list}

### Recommendation
{assessment of what to do next}
```
