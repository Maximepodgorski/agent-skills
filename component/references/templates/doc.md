# Component Documentation Template

> **Agent:** Use this template when generating output for the `doc` action. Adapt code examples to the project's framework. Output file: `doc.md` co-located in the component directory. Omit sections that don't apply (e.g., Slots if none, Expose if none, Controlled if component is stateless). Never leave empty tables — omit the section entirely.

---

```markdown
# {ComponentName}

{One-line description of what this component does and its purpose.}

## When to use

Use `{ComponentName}` when:
- {Use case 1 — context where this component is appropriate}
- {Use case 2}
- {Use case 3}

Do NOT use `{ComponentName}` when:
- {Anti-pattern 1} → use `{Alternative}` instead
- {Anti-pattern 2} → use `{Alternative}` instead

## How to use

### Basic

<!-- Minimal working example — the simplest usage -->

{code block with framework-appropriate syntax}

### With variants

<!-- Show different visual variants -->

{code block showing variant prop usage}

### With slots

<!-- Show slot/children composition -->

{code block showing slot usage}

### Controlled

<!-- Show controlled state if applicable -->

{code block showing v-model or controlled state}

## API

### Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
|      |      |         |             |

### Slots

| Slot | Description |
|------|-------------|
|      |             |

### Events

| Event | Payload | Description |
|-------|---------|-------------|
|       |         |             |

### Expose

| Method | Description |
|--------|-------------|
|        |             |

## Examples

### {Example name — e.g., "In a form"}

<!-- Show the component in a real-world context -->

{code block}

### {Example name — e.g., "With custom content"}

{code block}

### {Example name — e.g., "Compound usage"}

<!-- For compound components, show the full composition -->

{code block}

## Do / Don't

| Do | Don't |
|----|-------|
| {Concrete good usage with brief code if helpful} | {Concrete bad usage with brief code if helpful} |
| {Another good pattern} | {Another anti-pattern} |
| {Another good pattern} | {Another anti-pattern} |

## Accessibility

- **Keyboard:** {How to navigate and interact with keyboard}
- **Screen reader:** {What is announced to screen reader users}
- **ARIA:** {Required ARIA attributes for proper usage}
```

---

## Generation Rules

- **When to use** comes BEFORE **How to use** — context before code
- All code examples use the project's actual framework (detect from codebase)
- Props table matches the actual component interface (read the code)
- If generating from spec (pre-implementation), mark examples as `<!-- DRAFT — update after implementation -->`
- Do/Don't uses concrete examples, not abstract advice
- Accessibility section covers keyboard, screen reader, and ARIA — not just "it's accessible"
- No internal implementation details — doc is for consumers
