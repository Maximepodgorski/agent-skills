# Dev Output Template

Edit this file to customize what the agent returns in chat during and after `dev`. This is the most complex output — it has iteration updates and exit states.

---

## Iteration Output

After each iteration of the ship loop:

```markdown
## Iteration {N}/{max}

**Item:** {spec item name} → {DONE | IN_PROGRESS | BLOCKED}
**Quality:** lint {OK/FAIL} | typecheck {OK/FAIL}
**Progress:** {completed}/{total} items ({percent}%)
**Tokens:** {N} used | {N} created (user-approved) | 0 hardcoded

{If review ran this iteration:}
**Review:** {N} critical | {N} warnings

**Next:** {what happens next iteration}
```

---

## Exit: Complete

```markdown
## Dev: {ComponentName} — Complete

**Files:**
  - {ComponentName}.{ext}
  - index.{ext}
  {- {SubComponent}.{ext}}

**Tokens:** {N} from project | {N} created (user-approved) | 0 hardcoded
**Quality:** lint OK | typecheck OK
**Progress:** {N}/{N} spec items

Next:
  → /component review {path}   (DS compliance check)
  → /component doc {path}      (generate/update documentation)
```

---

## Exit: Partial

```markdown
## Dev: {ComponentName} — Partial

**Progress:** {N}/{total} spec items ({percent}%)
**Remaining:**
  - [ ] {incomplete item 1}
  - [ ] {incomplete item 2}
**Quality:** lint {OK/FAIL} | typecheck {OK/FAIL}

Re-run `/component dev {name}` to resume.
```

---

## Exit: Stuck

```markdown
## Dev: {ComponentName} — Stuck

**Progress:** {N}/{total} items ({percent}%) | Iteration {N}/{max}
**Blocked on:** {what's blocking progress}

Options:
  1. Continue with different approach
  2. Reduce scope
  3. Defer
```

---

## Exit: Blocked (open blockers in spec)

```markdown
## Dev: {ComponentName} — Blocked

Cannot start. {N} open blocker(s) in spec:
  - {blocker 1} ({type})
  - {blocker 2} ({type})

Resolve blockers first, then re-run `/component dev {name}`.
```
