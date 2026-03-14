# Doc Output Template

Edit this file to customize what the agent returns in chat after `doc`. The doc.md file is the detailed output — this is just the status message.

---

## Success

```markdown
## Doc: {ComponentName}

**File:** `{path}/doc.md`
**Sections:** When to use, How to use, API ({N} props, {N} slots, {N} events), {N} examples, Do/Don't, Accessibility
**Source:** {from spec | from code | from Figma}

{If generated from spec (pre-implementation):}
**Note:** Examples marked as DRAFT — update after implementation.

Next:
  → /component dev {name}       (implement the component)
  → /component review {path}    (review existing implementation)
```

---

## Merge (existing doc)

```markdown
## Doc: {ComponentName} (merged)

**File:** `{path}/doc.md`
**Updated:** {sections that changed}
**Preserved:** {custom sections kept from existing doc}
```

---

## Error

```markdown
## Doc: Failed

**Reason:** {Component not found in codebase or specs}
**Action:** Run `/component spec {name}` first, or provide a file path.
```
