# Todo Action

> **Agent:** Load this file when `todo` triggers. This action reads `.context/TODO.md` and walks the user through unanswered questions axis by axis.

Review and answer open questions from the context docs. Interactive, one axis at a time.

---

## Input Detection

```
.context/TODO.md exists?
  NO  -> "No open questions found. Your context is complete — or run 'kickoff init' if you haven't started."
  YES -> Continue

.context/TODO.md has unanswered questions?
  NO  -> "All questions have been answered. Nothing to do."
  YES -> Continue
```

## Step 0: Load TODO

1. Read `.context/TODO.md`
2. Parse questions grouped by axis
3. Count total questions and questions per axis
4. Present overview:

```
"You have {N} open questions across {M} axes:

  Product ({N} questions)
  Engineering ({N} questions)
  {other axes...}

  Want to go through them all, or pick an axis?"
```

Options:
- All → go axis by axis in order
- Specific axis → jump to that axis
- Skip → stop

## Step 1: Walk Through Questions

For each axis (or selected axis):

```
1. Present the questions for this axis:
   "Open questions for [axis]:
     1. {question} (from {source file})
     2. {question} (from {source file})
     ...

   Let's go through them. {First question}?"

2. User answers:
   -> Update the relevant .context/{axis}/{file}.md with the new information
   -> Remove the question from .context/TODO.md
   -> If the answer reveals a new question, add it to TODO.md

3. If user says "skip" or "don't know":
   -> Keep the question in TODO.md
   -> Move to next question

4. After all questions for this axis:
   -> "Done with [axis]. {N} questions answered, {M} remaining."
   -> Update _meta.md timestamp for the axis
```

### Interview rules during todo

- **Max 1 follow-up per question** — this is a quick pass, not a deep interview
- **Accept "skip" gracefully** — the question stays in TODO.md for later
- **If an answer is thin**, push back once: "That's a bit vague — can you be more specific? An agent needs [X] to act on this." If still thin, accept and move on.
- **If an answer changes existing context**, show the diff: "This changes what I had before: [old] → [new]. Correct?"

## Step 2: Update TODO.md

After processing all selected questions:

1. Rewrite `.context/TODO.md` with remaining unanswered questions
2. If all questions are answered, keep the file with a header and empty sections:

```markdown
# TODO — Open Questions

No open questions. Context is complete.

Last reviewed: {YYYY-MM-DD}
```

3. Update `_meta.md` timestamps for all affected axes

## Step 3: Offer Next Steps

```
"Done. {N} questions answered, {M} remaining.

  {If questions remain:}
  -> `kickoff todo`                  Come back to remaining questions later
  {If all answered:}
  -> `kickoff generate`              Regenerate CLAUDE.md with enriched context
  -> `kickoff analyze`               Challenge your context for gaps"
```

## Output

Show a summary of what was updated:

```markdown
## Todo Review Complete

**Answered:** {N} questions across {M} axes
**Remaining:** {N} questions
**Files updated:** {list of .context/ files modified}

{If remaining > 0:}
Run `kickoff todo` to continue later.

{If remaining == 0:}
All questions answered. Run `kickoff generate` to regenerate CLAUDE.md.
```

## Rules

1. NEVER invent answers — only write what the user said
2. ALWAYS update the source context file, not just TODO.md
3. ALWAYS remove answered questions from TODO.md immediately
4. If an answer generates a new question, add it to TODO.md
5. Keep questions in TODO.md grouped by axis
6. Max 1 follow-up per question — this should be fast
7. If user wants to stop mid-axis, save progress (answered questions are already removed from TODO.md)
