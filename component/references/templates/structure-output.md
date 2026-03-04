# Structure Output Template

Edit this file to customize what the agent returns in chat after `structure`. The spec file itself is the detailed output — this is just the status message.

---

## Success

```markdown
## Structure: {ComponentName}

**Spec:** `ds/specs/active/{component-name}-spec.md`
**Atomic level:** {atom | molecule | organism}
**Props:** {N} | **Slots:** {N} | **Events:** {N}
**Tokens:** {N} mapped | {N} missing
**Blockers:** {N} open | {N} resolved

{If blockers > 0:}
**Blocked:** {blocker 1} ({type}), {blocker 2} ({type})

Next:
  → /component doc {name}     (generate documentation)
  → /component dev {name}     (start implementation — requires 0 open blockers)
  → Edit the spec manually    (adjust before proceeding)
```

---

## Overwrite/Update (existing spec)

```markdown
## Structure: {ComponentName} (updated)

**Spec:** `ds/specs/active/{component-name}-spec.md`
**Changes:** {what changed vs previous version}

Next: same as above
```

---

## Error

```markdown
## Structure: Failed

**Reason:** {No context available / Figma MCP unavailable / ...}
**Action:** {Provide a Figma link, or describe the component manually}
```
