# Init Output Template

> Chat message returned after `/interface init` completes.

---

## Success

```markdown
## Init: {project-name}

**Conventions:** `ds/conventions.md`
**Stack:** {framework} + {styling} + {meta-framework}
**Components:** {N} detected in {path}
**Tokens:** {N} variables ({categories})
**Layout patterns:** {N} detected

Detected conventions saved. The skill will load this file on every action.

Next:
  /interface spec "{screen name}"   (compose + spec a screen)
```

---

## First Run (no components)

```markdown
## Init: {project-name}

**Conventions:** `ds/conventions.md`
**Stack:** {framework} + {styling}
**Components:** 0 detected

This skill works best with an existing component library. Consider:
  /component spec {name}   (create your first components)
  /interface spec "{name}"  (proceed anyway — gaps will be flagged)
```

---

## Already Exists

```markdown
## Init: {project-name}

**Conventions:** `ds/conventions.md` already exists (created {date})

{If stale > 30 days:}
Last updated {N} days ago. Re-scanning recommended.
  → Re-run `/interface init` to refresh

{If recent:}
Conventions are current. No action needed.

Next:
  /interface spec "{screen name}"
```

---

## Error

```markdown
## Init: Failed

**Reason:** {Could not detect project structure / No source files found / ...}
**Action:** Ensure you're in a project root with source files.
```
