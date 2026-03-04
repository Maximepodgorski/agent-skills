# Accessibility — WCAG 2.1 Component Patterns

> Based on **WCAG 2.1 AA** and **WAI-ARIA Authoring Practices**.

## Core Principle

**Accessibility is not optional.** Every component must be usable by keyboard, screen reader, and assistive technology. It's not an add-on — it's built in from the start.

## Semantic HTML First

Use the right HTML element. ARIA is a supplement, not a replacement.

| Do | Don't |
|----|-------|
| `<button>Click me</button>` | `<div role="button" tabindex="0" @click>Click me</div>` |
| `<a href="/page">Link</a>` | `<span @click="navigate" class="link">Link</span>` |
| `<input type="checkbox">` | `<div role="checkbox" aria-checked>` |
| `<dialog>` | `<div class="modal">` |
| `<nav>` | `<div class="nav">` |
| `<ul><li>` for lists | `<div><div>` for lists |

**Rule:** If a native HTML element does what you need, use it. Only reach for ARIA when no semantic element exists.

## Keyboard Navigation Patterns

Every interactive component must be keyboard accessible:

### Focus Management

| Pattern | Keys | Behavior |
|---------|------|----------|
| **Focusable** | `Tab` | Component receives focus in tab order |
| **Activatable** | `Enter`, `Space` | Triggers the component's primary action |
| **Dismissible** | `Escape` | Closes overlay/popup/modal |
| **Navigable** | `Arrow keys` | Moves within a group (menu, tabs, listbox) |
| **Focus trap** | `Tab` cycles within | Modal, dialog — focus stays inside when open |

### Component-Specific Patterns

| Component | Tab | Enter/Space | Escape | Arrows |
|-----------|-----|-------------|--------|--------|
| Button | Focus | Activate | — | — |
| Link | Focus | Navigate | — | — |
| Input | Focus | — | — | — |
| Checkbox | Focus | Toggle | — | — |
| Radio group | Focus group | Select | — | Up/Down cycle |
| Select/Dropdown | Focus trigger | Open | Close | Up/Down navigate items |
| Menu | Focus trigger | Open / Select item | Close | Up/Down navigate items |
| Tabs | Focus tab list | Select tab | — | Left/Right cycle tabs |
| Modal/Dialog | Focus first element | — | Close | — |
| Tooltip | Focus trigger | — | Dismiss | — |
| Accordion | Focus header | Toggle panel | — | — |

## ARIA Attributes

### Essential ARIA by Component

| Component | Required ARIA |
|-----------|--------------|
| Button (if not `<button>`) | `role="button"`, `tabindex="0"` |
| Toggle button | `aria-pressed="true/false"` |
| Link (if not `<a>`) | `role="link"`, `tabindex="0"` |
| Dropdown trigger | `aria-expanded="true/false"`, `aria-haspopup="true"` |
| Dropdown menu | `role="menu"`, items: `role="menuitem"` |
| Modal | `role="dialog"`, `aria-modal="true"`, `aria-labelledby` |
| Tabs | `role="tablist"`, tabs: `role="tab"`, panels: `role="tabpanel"`, `aria-selected` |
| Accordion | `aria-expanded`, header: `role="button"` (if not button) |
| Tooltip | `role="tooltip"`, trigger: `aria-describedby` pointing to tooltip |
| Alert | `role="alert"` (assertive) or `role="status"` (polite) |
| Loading | `aria-busy="true"`, `aria-live="polite"` |
| Form field | `aria-required`, `aria-invalid`, `aria-describedby` (error message) |
| Icon-only button | `aria-label="Description"` |

### Live Regions

For dynamic content updates:

```
polite (non-urgent):   aria-live="polite"    → toast, status update
assertive (urgent):    aria-live="assertive" → error, alert
off (no announce):     aria-live="off"       → default
```

## Color and Contrast

| Requirement | WCAG Level | Ratio |
|-------------|-----------|-------|
| Normal text (< 18px) | AA | 4.5:1 minimum |
| Large text (>= 18px or 14px bold) | AA | 3:1 minimum |
| UI components and graphics | AA | 3:1 minimum |
| Focus indicator | AA | 3:1 against adjacent colors |

**Rule:** Never rely on color alone to convey information. Always pair with icon, text, or pattern.

| Do | Don't |
|----|-------|
| Error: red border + error icon + text message | Error: red border only |
| Status: colored badge + text label | Status: colored dot only |
| Required: asterisk + "Required" text | Required: red color only |

## Focus Visibility

```css
/* GOOD — visible, accessible focus */
:focus-visible {
  outline: 2px solid var(--color-focus);
  outline-offset: 2px;
}

/* GOOD — hide focus for mouse users, show for keyboard */
:focus:not(:focus-visible) {
  outline: none;
}

/* BAD — removes ALL focus indication */
:focus {
  outline: none;
}
```

**Rule:** Never remove focus styles without providing an alternative. `outline: none` without `:focus-visible` replacement is a CRITICAL violation.

## Motion

```css
/* Respect user preference */
@media (prefers-reduced-motion: reduce) {
  * {
    animation-duration: 0.01ms !important;
    transition-duration: 0.01ms !important;
  }
}
```

**Rule:** All animations must respect `prefers-reduced-motion`. No exceptions.

## Review Checklist

When reviewing a component for accessibility:

```
[ ] Semantic HTML used (correct elements, not div soup)
[ ] Keyboard navigable (Tab, Enter, Escape, Arrows as appropriate)
[ ] Focus visible and styled
[ ] Focus trapped in modals/overlays
[ ] ARIA attributes present and correct
[ ] aria-label on icon-only buttons
[ ] aria-expanded on toggles/dropdowns
[ ] aria-live on dynamic content
[ ] Color contrast passes AA (4.5:1 text, 3:1 UI)
[ ] No color-only information
[ ] Motion respects prefers-reduced-motion
[ ] Screen reader tested (logical reading order, meaningful announcements)
```
