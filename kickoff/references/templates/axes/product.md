# Product Axis Template

> **Agent:** Load this during `init` or `update` for the Product axis. This defines WHAT to know, not WHAT to ask.

## Depth: Deep (challenge vague answers)

## Objectives

At the end of this axis, you must know:

- [ ] **Problem**: What specific problem the product solves (not vague — concrete, observable)
- [ ] **Users**: At least 1 persona with role, pain point, and job-to-be-done (ideally 2-3)
- [ ] **Differentiation**: Why this is better than current alternatives (including "do nothing")
- [ ] **Features**: Main features that exist today (shipped, not planned)
- [ ] **Planned**: What's coming next (short-term priorities)
- [ ] **North star**: The single metric that indicates success
- [ ] **Stage**: Where the product is (idea / MVP / early users / revenue / scale)

## Opening

```
"Tell me about your product. What does it do, who is it for, and what problem does it solve?"
```

One open question. The user can answer in 2 lines or 20. Adapt.

## Strategies (adaptive follow-up)

```
IF problem is vague ("helps people manage X"):
  -> "Can you describe a concrete moment when someone has this problem?
      What are they doing, and what goes wrong?"

IF no users/personas mentioned:
  -> "Who uses this today? Describe a real user — their role, what they
      do day-to-day, why they need your product."

IF only 1 persona:
  -> "Are there other types of users, or is it really one profile?"

IF no differentiation:
  -> "What do people do today without your product? Why would they switch?"

IF differentiation is weak ("better UX", "simpler"):
  -> "Can you be more specific? What exactly makes it better?
      What can someone do with your product that they can't do elsewhere?"

IF no features listed:
  -> "What are the main things someone can do with your product right now?"

IF no north star:
  -> "What's THE metric that tells you it's working?
      Not 5 KPIs — the one number you'd check every morning."

IF stage unclear:
  -> "Where are you at? Do you have users? Revenue? Or still building?"
```

## Completion signals

```
All 7 objectives checked     -> "Product axis is solid. Moving on."
>= 5 objectives checked      -> "I have the essentials. Anything else to add before we move on?"
< 5 after all follow-ups     -> Mark missing as TBD. "Some product questions are still open —
                                 marked as TBD. You can refine later with 'kickoff update product'."
```

## Output docs

This axis produces 3 files:

| File | Content |
|------|---------|
| `product/vision.md` | Problem, solution in 1 sentence, differentiation, north star, stage |
| `product/users.md` | Personas with pain points, JTBD, technical level |
| `product/features.md` | Features shipped + planned with priority (high/medium/low) |
