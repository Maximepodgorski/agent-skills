# Spec Output Template

> Chat messages returned during and after `/interface spec`. Covers Phase 1 (compose) round outputs and Phase 2 (spec) completion.

---

## Phase 1: Compose Round

```markdown
## Compose: {screen name} (Round {N}/3)

**Components scanned:** {N}
**Layout patterns considered:** {N}

### Option A — {pattern name}

{ASCII wireframe}

**Coverage:** {N}% | **Mapped:** {N} | **Gaps:** {N}
**Components:** {list of key components used}
{If gaps:} **Missing:** {component1}, {component2}

---

### Option B — {pattern name}

{ASCII wireframe}

**Coverage:** {N}% | **Mapped:** {N} | **Gaps:** {N}
**Components:** {list}
{If gaps:} **Missing:** {list}

---

### Option C — {pattern name}

{ASCII wireframe}

**Coverage:** {N}% | **Mapped:** {N} | **Gaps:** {N}
**Components:** {list}
{If gaps:} **Missing:** {list}

---

Pick an option: "A", "B", "C", "mix A+C", or describe what you want.
```

---

## Phase 1: Free-Form Fallback (after 3 rounds)

```markdown
## Compose: {screen name}

3 rounds didn't hit the mark. Describe the exact layout you want:
- Which components to use
- How they should be arranged
- Any specific behavior

I'll build the spec from your description.
```

---

## Phase 2: Spec Generated

```markdown
## Spec: {screen name}

**Spec:** `ds/interfaces/active/{filename}.md`
**Pattern:** {layout pattern}
**Coverage:** {N}%
**Mapped:** {N} components | **Gaps:** {N}
**Tier:** {mini | standard}

{If gaps > 0:}
**Missing components:**
  - {component1} → {proposed solution}
  - {component2} → {proposed solution}

Next:
  /interface spec-review     (multi-perspective review)
  /interface ship            (start implementation)
  Edit the spec manually     (adjust before building)
```

---

## Error: No Conventions

```markdown
## Spec: Setup Required

No `ds/conventions.md` found. Running init first...
```

---

## Error: No Components

```markdown
## Spec: {screen name}

No components found in the codebase. This skill works best with an existing component library.

Options:
  /component spec {name}   (create components first)
  Continue anyway           (all zones will be flagged as gaps)
```
