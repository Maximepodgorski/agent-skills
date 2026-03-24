# Team Axis Template

> **Agent:** Load this during `init` or `update` for the Team axis. Only if >1 person on the project.

## Depth: Light (1-2 questions, accept TBD)

## Objectives

- [ ] **Size**: How many people
- [ ] **Roles**: Who does what (dev, design, product, etc.)
- [ ] **Review process**: PR reviews? Who reviews what?
- [ ] **Communication tools**: Slack, Linear, Notion, GitHub, etc.
- [ ] **Rituals**: Standups, sprint planning, retros (if any)

## Opening

```
"How is your team organized? Who does what, and how do you communicate?"
```

## Strategies

```
IF user describes roles:
  -> "How does code review work? PRs required? Who reviews?"

IF user describes tools:
  -> Note them. These affect how the agent structures outputs (e.g., Linear ticket references).

IF solo recently became a team:
  -> "Any processes you've established, or still figuring it out?"
  -> Accept "still figuring it out" as TBD.
```

## Output docs

| File | Content |
|------|---------|
| `team/team.md` | Size, roles (who does what specifically), review process (PR rules, who reviews), communication tools (with how each is used), rituals (with frequency + format) |

**All output docs must include an Open Questions section** (2-5 specific questions for future context improvement).
