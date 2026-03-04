# Props API — Component Interface Design

## Core Principle

**A component's API is its contract.** Minimal, predictable, type-safe. If 80% of use cases work with defaults, the API is good.

## Props Design Rules

### 1. Minimal Props

Every prop must justify its existence. If removing it doesn't break the 80% use case, remove it.

| Do | Don't |
|----|-------|
| `<Tooltip content="Help text">` (1 prop) | `<Tooltip content="Help text" showArrow={true} arrowSize={8} arrowOffset={4}>` (4 props for arrow config) |
| `<Button variant="primary">` | `<Button color="blue" hoverColor="darkblue" borderColor="blue" textColor="white">` |

**Test:** Count your props. If > 7, the component is doing too much. Split it.

### 2. Smart Defaults

The default configuration should cover the most common use case.

```
GOOD:
  <Tooltip placement="top">    ← default, most common
  <Tooltip placement="bottom"> ← explicit when needed

BAD:
  <Tooltip placement={undefined}> ← no default, always required
```

| Rule | Example |
|------|---------|
| Boolean defaults to `false` | `disabled` defaults to not disabled |
| Size defaults to `md` | The most common size is the default |
| Placement defaults to `top` or the most intuitive position | Most tooltips appear above |
| Variant defaults to the most used variant | Usually `primary` or `default` |

### 3. Strict Types

Never accept `string` when you mean a specific set of values.

| Do | Don't |
|----|-------|
| `size: 'sm' \| 'md' \| 'lg'` | `size: string` |
| `placement: 'top' \| 'bottom' \| 'left' \| 'right'` | `placement: string` |
| `variant: 'primary' \| 'secondary' \| 'ghost'` | `variant: string` |
| `count: number` | `count: string \| number` |

### 4. No Boolean Traps

A boolean prop should not change the component's fundamental behavior.

| Do | Don't |
|----|-------|
| `<Button variant="ghost">` | `<Button ghost>` (what does ghost + primary mean?) |
| `<Input type="password">` | `<Input isPassword secure hideCharacters>` (3 booleans for 1 behavior) |
| `<Dialog size="fullscreen">` | `<Dialog fullscreen>` (conflicts with size prop) |

**Rule:** If a boolean conflicts with another prop, use an enum instead.

### 5. Slots > Props for Content

If a prop accepts renderable content (JSX, VNode, HTML string), it should be a slot.

| Do (slot) | Don't (prop) |
|-----------|-------------|
| `<Card><template #header>Title</template></Card>` | `<Card header="Title" />` |
| `<Alert><WarningIcon /> Message</Alert>` | `<Alert icon="warning" message="Message" />` |
| `<EmptyState><template #action><Button>Retry</Button></template></EmptyState>` | `<EmptyState actionLabel="Retry" onAction={fn} />` |

**Exception:** Simple string content can be a prop when it's always text: `<Tooltip content="Help text">`.

### 6. Predictable Events

| Rule | Example |
|------|---------|
| Name describes what happened | `change`, `close`, `select` |
| Payload is consistent | Always `(value: T)` or `(event: { value: T, ... })` |
| One event per action | `select` not `select` + `change` + `update` for the same action |
| Two-way binding support | Framework convention (v-model, controlled props, bind:value) |

### 7. Minimal Expose

Only expose methods that consumers genuinely need. Internal state stays internal.

| Do (expose) | Don't (expose) |
|-------------|----------------|
| `dialog.open()`, `dialog.close()` | `dialog.setState()`, `dialog.internalRef` |
| `input.focus()`, `input.blur()` | `input.validate()`, `input.setError()` (use props) |
| `tooltip.show()`, `tooltip.hide()` | `tooltip.position`, `tooltip.isVisible` (use events) |

## API Consistency Across Components

All components in a DS should follow the same patterns:

| Pattern | Convention | Applied to |
|---------|-----------|------------|
| Size scale | `xs`, `sm`, `md`, `lg`, `xl` | All components with size |
| Variant scale | `primary`, `secondary`, `tertiary`, `ghost` | All actionable components |
| Status scale | `info`, `success`, `warning`, `error` | All feedback components |
| Disabled pattern | `disabled: boolean` (not `isDisabled`) | All interactive components |
| Loading pattern | `loading: boolean` | All async components |

**Rule:** If Button uses `size="sm"`, then Input, Select, Badge must also use `size="sm"` — not `size="small"` or `small={true}`.

## Prop Documentation Pattern

Every prop should be documentable in this table:

| Prop | Type | Default | Required | Description |
|------|------|---------|----------|-------------|
| `placement` | `'top' \| 'bottom' \| 'left' \| 'right'` | `'top'` | No | Where the tooltip appears relative to trigger |
| `delay` | `number` | `200` | No | Delay in ms before showing |
| `disabled` | `boolean` | `false` | No | Prevents tooltip from showing |
| `content` | `string` | — | Yes | Text content of the tooltip |

If a prop can't be described in one line, it's too complex. Split it.
