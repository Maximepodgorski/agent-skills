# Ship Output Template

Edit this file to customize what the agent returns in chat during and after `ship`. Outputs are aligned with the writing-plans / executing-plans pattern: per-task progress + per-step checkboxes.

Outputs apply to both execution modes (Inline and Subagent-Driven). For Subagent-Driven, prefix the per-task update with `[subagent {N}]` so the user can see who did what.

---

## Per-Task Update

Output once per task as it starts:

```markdown
## Task {N}/{total}: {task subject}

**Files:**
- {Create | Modify | Test}: `{path}`

**Steps:**
- [x] Write failing test
- [x] Run — fails as expected ({assertion message})
- [x] Implement
- [x] Run — passes
- [x] Commit ({commit sha or message})

**Quality:** lint {OK/FAIL} | typecheck {OK/FAIL}
**Tokens used:** {N} project | 0 hardcoded | {N} created (user-approved)
```

---

## Checkpoint (every 2-3 tasks)

```markdown
## Checkpoint after Task {N}

**Progress:** {N}/{total} tasks complete ({percent}%)
**DS Review:** {N} critical | {N} warnings
{- {issue 1} → fixed in task {M}}
{- {issue 2} → logged in Notes}

**Next:** Task {N+1}: {subject}
```

---

## Exit: Complete

```markdown
## Ship: {ComponentName} — Complete

**Files:**
  - {ComponentName}.{ext}
  - index.{ext}
  {- {SubComponent}.{ext}}

**Tasks:** {N}/{N} complete
**Tokens:** {N} from project | {N} created (user-approved) | 0 hardcoded
**Quality:** lint OK | typecheck OK
**Commits:** {N} (one per task step group)

Next:
  → /component review {path}   (DS compliance check)
  → /component doc {path}      (generate/update documentation)
```

---

## Exit: Partial

```markdown
## Ship: {ComponentName} — Partial

**Tasks:** {N}/{total} complete ({percent}%)
**Remaining:**
  - [ ] Task {N+1}: {subject} — {first unchecked step}
  - [ ] Task {N+2}: {subject}
**Quality:** lint {OK/FAIL} | typecheck {OK/FAIL}

Spec is updated with checkbox state. Re-run `/component ship {name}` to resume from the next unchecked step.
```

---

## Exit: Stuck

```markdown
## Ship: {ComponentName} — Stuck

**Progress:** {N}/{total} tasks ({percent}%)
**Stuck on:** Task {N}, Step {M} — {what's blocking}
**Attempts:** {N} (test won't pass | review violation recurring | spec drift)

Options:
  1. Continue with different approach: {alternative}
  2. Reduce scope: drop Task {N}, mark as spec follow-up
  3. Defer: commit current progress, resume later
```

---

## Exit: Blocked (open blockers in spec)

```markdown
## Ship: {ComponentName} — Blocked

Cannot start. {N} open blocker(s) in spec:
  - {blocker 1} ({type})
  - {blocker 2} ({type})

Resolve blockers (update spec Blockers section to RESOLVED), then re-run `/component ship {name}`.
```

---

## Exit: Spec Missing Tasks

```markdown
## Ship: {ComponentName} — Spec Incomplete

The spec at `ds/specs/active/{name}-spec.md` has no Implementation Tasks section (or it's empty).

Options:
  1. Run `/component spec {name}` to regenerate the spec with tasks
  2. Continue in ONE-SHOT mode (inline mini-plan, no audit trail)

Which?
```
