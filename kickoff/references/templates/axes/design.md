# Design Axis Template

> **Agent:** Load this during `init` or `update` for the Design axis. Only if the project has a UI.

## Depth: Standard (don't insist — accept TBD)

## Objectives

- [ ] **Design system**: Does one exist? (Figma, Storybook, custom)
- [ ] **Visual identity**: Look & feel in 2-3 adjectives
- [ ] **Colors**: Primary palette (or reference to token system)
- [ ] **Typography**: Font(s), scale
- [ ] **Spacing**: Base unit, scale (or "follows framework defaults")
- [ ] **Responsive**: Mobile-first or desktop-first
- [ ] **Accessibility**: Target level (WCAG AA, custom, none defined)

## Opening

```
"Do you have a design system or visual guidelines for this project?"
```

## Strategies

```
IF design system exists:
  -> "Where is it? (Figma link, Storybook URL, code reference)
      What are the main tokens — colors, fonts, spacing?"

IF no design system:
  -> "That's fine. 3 adjectives for the look & feel of your product?"
  -> "Main colors? (even rough — 'blue and white', 'dark theme')"
  -> "Any font preference, or default system font?"

IF "not decided yet":
  -> Mark all as TBD. Move on.

Mobile/desktop:
  -> "Is it mobile-first or desktop-first? Or responsive equally?"
```

## Output docs

| File | Content |
|------|---------|
| `design/principles.md` | Design system reference (URL/location), visual identity (adjectives + examples), responsive strategy, accessibility target (WCAG level), component library status |
| `design/tokens.md` | Colors (primary, secondary, semantic with hex/variable names), typography (font family, scale, weight usage), spacing (base unit, scale), breakpoints (values + names) |

**All output docs must include an Open Questions section** (2-5 specific questions for future context improvement).
