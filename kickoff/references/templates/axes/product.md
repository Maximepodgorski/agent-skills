# Product Axis Template

> **Agent:** Load this during `init` or `update` for the Product axis. This defines WHAT to know, not WHAT to ask.

## Depth: Deep (challenge vague answers — this is the most important axis)

## Objectives

At the end of this axis, you must know:

- [ ] **Problem**: What specific problem the product solves (not vague — concrete, observable, with real-world consequences)
- [ ] **Users**: At least 1 persona with role, pain point, and job-to-be-done (ideally 2-3). Real enough to name their job title.
- [ ] **Differentiation**: Why this is better than current alternatives (including "do nothing"). Must be SPECIFIC, not "better UX."
- [ ] **Features**: Main features that exist today (shipped, not planned). With enough detail to know what screens/APIs exist.
- [ ] **Planned**: What's coming next (short-term priorities)
- [ ] **Roadmap**: Concrete week-by-week plan for the next 1-3 months + directional vision for 3-6 months. What gets built first, why, and what unlocks what.
- [ ] **North star**: The single metric that indicates success. ONE number, not a dashboard.
- [ ] **Stage**: Where the product is (idea / MVP / early users / revenue / scale)

## Opening

```
"Tell me about your product. What does it do, who is it for,
and what specific problem does it solve?

Be concrete — I want to understand the REAL problem, not the pitch."
```

One open question. The user can answer in 2 lines or 20. Adapt. But if it's 2 thin lines, push back hard.

## Strategies (adaptive follow-up)

### Level 1 — Detect and challenge thin answers

```
IF problem is vague ("helps people manage X", "a platform for Y"):
  -> "That's too generic — every competitor could say the same thing.
      Describe a CONCRETE moment: a real person, in their real job,
      hits this problem. What are they doing? What goes wrong?
      What do they feel?"

IF no users/personas mentioned:
  -> "Who uses this TODAY? Not 'businesses' — give me a specific person.
      What's their job title? What do they do before they open your product?
      Why do they NEED it?"

IF only 1 persona:
  -> "Is there really only one type of user? What about the person who
      BUYS vs the person who USES? Admin vs end-user? Power user vs casual?"

IF no differentiation:
  -> "What do people do today WITHOUT your product? And be honest —
      why would they switch? Switching has a cost. What makes it worth it?"

IF differentiation is weak ("better UX", "simpler", "AI-powered"):
  -> "Every startup says that. I need something SPECIFIC.
      Give me one thing someone can do with your product that they
      literally CANNOT do with [alternative]. Not 'easier' — can't."

IF no features listed:
  -> "Walk me through the product. A user logs in — then what?
      What are the 3-5 main things they can DO right now?"

IF no roadmap / only vague plans:
  -> "Let's build a concrete roadmap. Forget long-term vision for now.
      What's the ONE thing you're building this week?
      Then: what MUST be shipped in the next 2-4 weeks?
      And what's the big milestone for month 2-3?"

IF roadmap is just a feature list with no sequencing:
  -> "I need to understand the ORDER and the WHY.
      What gets built FIRST? What does it unlock?
      What blocks what? What can you ship independently?"

IF no north star:
  -> "What's THE metric that tells you it's working?
      Not 5 KPIs — the one number you'd check every morning.
      If that number goes up, you're winning. What is it?"

IF stage unclear:
  -> "Where are you at? Do you have real users paying money?
      How many? Or are you still building V1?"
```

### Level 2 — Dig deeper on confirmed but shallow objectives

```
IF problem is stated but not specific enough to build a test case:
  -> "I need to be able to describe this problem to an engineer.
      'Users waste time on X' isn't enough. HOW MUCH time?
      On WHAT specifically? What's the current workaround?"

IF users are mentioned but lack depth:
  -> "Tell me more about [persona]. What's their technical level?
      What tools do they use today? What frustrates them most
      about those tools?"

IF features are listed but too high-level ("dashboard", "reports"):
  -> "When you say 'dashboard' — what's ON it? What data?
      What actions can the user take from there?"

IF differentiation exists but isn't defensible:
  -> "If [competitor] shipped this same feature tomorrow,
      what would you still have that they don't?"

IF roadmap lacks specificity:
  -> "You said '[feature]' for week 2-4 — break it down for me.
      What's the user story? What does success look like?
      If I'm an agent implementing this, what do I build?"

IF roadmap has no exit criteria:
  -> "How do you know when [milestone] is DONE?
      What's the user-visible result? What metric moves?"
```

### Level 3 — Final push before TBD

```
IF still thin after 2 attempts:
  -> "I'm going to mark this as [TBD] for now. But I want you to know —
      this is the kind of context that makes or breaks the agent's output.
      When you have a clearer answer, run 'kickoff update product'."
```

## Completion signals

```
All 8 objectives checked with RICH answers  -> "Product axis is solid. Moving on."
>= 6 objectives with rich answers           -> "I have the essentials. Let me show you what I'll write."
< 6 after all follow-ups                    -> Mark missing as TBD with context.
                                                "Some product questions are still open —
                                                 marked as TBD. Run 'kickoff update product' when ready."
```

**Important:** >= 6 is the MINIMUM. If answers are thin, don't count them as checked. "We use React" does not check the Stack objective.

## Output docs

This axis produces 4 files:

| File | Content |
|------|---------|
| `product/vision.md` | Problem (concrete with real-world impact), solution in 1 sentence, differentiation (specific and defensible), north star (one metric with target), stage |
| `product/users.md` | Personas with: job title, daily workflow, pain point (with severity 1-10), JTBD, current workaround, technical level, tools they use today. Plus ICP definition |
| `product/features.md` | Features shipped (with user story + current status) + planned with priority (high/medium/low) |
| `product/roadmap.md` | Week 1 (immediate), Week 2-4 (short-term), Month 2-3 (medium-term), Month 3-6 (directional vision). Each item: what, why, exit criteria, dependencies |

**All output docs must include an Open Questions section** — see `references/templates/context-file.md` for the schema and rich examples.

### Output quality checklist

Before writing each product doc, verify:

```
vision.md:
  ✓ Problem describes a REAL moment (who, what, when, consequence)
  ✓ Differentiation is defensible (not just "better UX")
  ✓ North star has a numeric TARGET, not just a metric name
  ✓ Stage includes evidence (user count, revenue, or concrete milestone)

users.md:
  ✓ Each persona has a job title, not a generic role
  ✓ Pain point has a severity score (1-10)
  ✓ Current workaround is described (what they do TODAY without you)
  ✓ Switching trigger is explicit (what makes them adopt)
  ✓ ICP is defined (company size, tech stack, buying signal)

features.md:
  ✓ Each feature describes what a USER can DO, not technical implementation
  ✓ Shipped features have current status (stable, beta, experimental)
  ✓ Planned features have priority AND reason for priority

roadmap.md:
  ✓ Week 1 items are specific tasks with exit criteria
  ✓ Week 2-4 items explain WHY they're sequenced that way
  ✓ Each milestone has an exit criteria (how you know it's done)
  ✓ Dependencies between items are explicit
```
