# Update Action

> **Agent:** Load this file when `update` triggers. Also load the relevant axis template from `references/templates/axes/{axis}.md`.

Update a specific axis or doc in `.context/`. Surgical, fast, conversational.

---

## Input Detection

```
Input specifies an axis? (e.g., "update engineering", "update product")
  YES -> Load that axis
  NO  -> "Which axis do you want to update? [list active axes from _meta.md]"

.context/ exists?
  NO -> "No context found. Run 'kickoff init' first."
  YES -> Continue
```

## Step 0: Load Current State

1. Read `.context/_meta.md` -> get active axes, last update dates
2. Read all docs for the target axis -> current content
3. Read `.context/decisions/` -> get existing decision records and highest ID (needed if new decisions are captured)
4. Run a quick bootstrap scan for the axis (re-read package.json, lock file, configs for engineering). If artifact not found, skip silently.

## Step 1: Show Current State

Present the current docs to the user:

```
"Here's what I have for [axis]:

[Summary of current content — key bullets, not full docs]

Last updated: [date from _meta.md]

What's changed?"
```

Single open question. Let the user tell you what changed.

## Step 2: Adaptive Follow-up

Based on the user's response:

```
IF user gives a specific change:
  -> "Got it. I'll update [specific doc] with [change]. Anything else?"
  -> Apply change, confirm, write

IF user gives a broad change ("we pivoted", "new stack"):
  -> Load the axis template objectives
  -> Run a focused mini-interview on the changed objectives only
  -> Don't re-ask objectives that haven't changed

IF user says "just refresh from codebase":
  -> Run bootstrap scan for the axis
  -> Compare with current docs
  -> Present diffs individually: "I found these changes:
       1. [change 1] — apply? (y/n)
       2. [change 2] — apply? (y/n)
       ...
     Or: apply all / skip all"
```

## Step 3: Write Updated Docs

1. Update the specific files in `.context/{axis}/`
2. Update `_meta.md` with new timestamp for the axis
3. Follow the schema in `references/templates/context-file.md`
4. Sync `.context/TODO.md`:
   - Remove questions that were answered by the update
   - Add new Open Questions from updated docs
   - Keep questions from other axes untouched

## Step 4: Offer Regeneration

```
"Updated .context/{axis}/. Want me to regenerate CLAUDE.md?
  -> Yes: Run generate action
  -> Not now: Skip"
```

## Output

Follow the output template in `references/templates/outputs/update-output.md`.

## Rules

1. NEVER re-run the full interview — update is surgical
2. ALWAYS show current state before asking what changed
3. ALWAYS update _meta.md timestamp after writing
4. Preserve information the user didn't mention changing — don't wipe sections
5. If the change qualifies as a **decision**, create a new decision record in `.context/decisions/`:
   - A change qualifies as a decision if it: replaces one technology with another (not just adding a library),
     affects how all developers work, or reverses a previous documented decision.
   - Examples: package manager swap YES, CSS framework replacement YES, adding a utility library NO, config tweak NO.
   - Number the new record sequentially after the highest existing ID found in Step 0.
6. Max 2 follow-up questions — this should be fast (2-5 min)
7. If the user wants to update multiple axes, handle one at a time sequentially
8. **Escalation to init** — if the change is fundamental (pivot, new product, full stack rewrite, >2 axes affected), propose: "This sounds like a major change. Want to re-run `kickoff init` instead of patching?"
9. **Cross-axis signals** — when a change in one axis implies changes in another, flag it:
   - Product change mentions tech ("we added real-time") → "This may affect engineering/stack.md — want to update engineering too?"
   - Engineering change impacts product ("we dropped feature X") → "This may affect product/features.md — want to update product too?"
   - Business change impacts product ("new pricing tier") → "This may affect product/users.md — want to update product too?"
