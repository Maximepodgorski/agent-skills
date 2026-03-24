# Business Axis Template

> **Agent:** Load this during `init` or `update` for the Business axis. Only if commercial product.

## Depth: Standard (don't insist — accept TBD)

## Objectives

- [ ] **Revenue model**: How it makes money (or will)
- [ ] **Pricing**: Current or planned pricing structure
- [ ] **Target market**: Who pays, segment, estimated size
- [ ] **Competitors**: 2-5 alternatives (including "do nothing")
- [ ] **Positioning**: Why this wins against alternatives
- [ ] **Moat**: What's hard to replicate

## Opening

```
"How does your product make money, and who are your main competitors?"
```

## Strategies

```
IF no revenue model:
  -> "That's fine — is there a plan? Freemium, subscription, usage-based, one-time?"
  -> If still TBD: mark it, move on.

IF no competitors mentioned:
  -> "What do people use today instead? Even 'spreadsheets' or 'manual process' counts."

IF moat is unclear:
  -> "What would be hardest for a competitor to copy about your product?"
  -> If user can't answer: mark TBD, note this is a strategic gap.
```

## Output docs

| File | Content |
|------|---------|
| `business/model.md` | Revenue model (how money flows), pricing (tiers + amounts if known), target market (segment + size estimate), unit economics (if known) |
| `business/landscape.md` | Competitors/alternatives (with what they do well + where they fall short), positioning statement, moat (what's hard to replicate + why) |

**All output docs must include an Open Questions section** (2-5 specific questions for future context improvement).
