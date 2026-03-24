# Decisions Axis Template

> **Agent:** Load this during `init` or `update` for the Decisions axis. Decisions are captured INLINE during other axes, not as a separate interview.

## Depth: Medium (captured passively, confirmed at end)

## How Decisions Are Captured

Decisions are NOT interviewed separately. During the Product and Engineering interviews, listen for:

```
Signals that a decision was made:
  - "We chose X because..."
  - "We went with X over Y..."
  - "We decided to..."
  - "We tried X but switched to Y..."
  - "We considered X but..."
  - "The reason we use X is..."
```

When you detect a decision signal, capture it as a decision record.

## At End of Interview

After all axes are complete, present captured decisions:

```
"I captured these decisions along the way:

  001: [decision title] — [1-line rationale]
  002: [decision title] — [1-line rationale]
  ...

Any other key decisions I should record? Think about:
  - Why you chose your stack (if not already captured)
  - Architecture choices (monorepo, patterns, etc.)
  - What you explicitly decided NOT to do
  - Tradeoffs you made (speed vs quality, etc.)"
```

## Decision Record Format

Each decision is a separate file: `.context/decisions/NNN-{slug}.md`

Follow the template in `references/templates/decision-record.md`.

## Output

| File | Content |
|------|---------|
| `decisions/001-{slug}.md` | Individual decision record (ADR-lite) |
| `decisions/002-{slug}.md` | ... |

Numbered sequentially. Slug is kebab-case of the decision title.
