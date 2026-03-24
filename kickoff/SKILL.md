---
name: kickoff
description: |
  Project context kickoff and maintenance. Conversational onboarding that generates structured
  context docs (.context/) and produces a CLAUDE.md for the project.
  5 actions: init, update, analyze, review, generate.
  Auto-activates on: "kickoff", "kickoff init", "kickoff update", "kickoff analyze",
  "kickoff review", "kickoff generate", "project kickoff", "project setup", "project context",
  "onboard project", "setup context".
license: MIT
compatibility: "Agent-agnostic. Works with Claude Code, Cursor, Windsurf, Codex, Aider, or any agent supporting SKILL.md."
metadata:
  version: "1.0"
---

# Kickoff

Project context kickoff. Conversational interview that builds structured docs for AI agents.

## Agent Capabilities

| Capability | Used For | Required | Fallback |
|------------|----------|----------|----------|
| File read/write | `.context/` docs, CLAUDE.md | Yes | -- |
| Code search (grep/glob) | Bootstrap scan, drift detection | Recommended | User describes project manually |
| User interaction | Conversational interview, confirmations | Yes | -- |
| Task/subagent | `analyze` action (parallel reviewers) | Recommended | Single-agent analysis |
| Shell/command execution | Git log, package.json read | Recommended | User provides info manually |

**Fallback rule:** If codebase access is limited, the skill works from conversation alone. Warn the user and proceed.

## Actions

| Action | Input | Output | Reference |
|--------|-------|--------|-----------|
| `init` | (none) or project path | `.context/` folder with axis docs | [init.md](references/actions/init.md) |
| `update {axis}` | Axis name (product, engineering, etc.) | Updated axis docs | [update.md](references/actions/update.md) |
| `analyze` | (none) — reads existing `.context/` | Analysis report with findings | [analyze.md](references/actions/analyze.md) |
| `review` | (none) — reads `.context/` + codebase | Drift report | [review.md](references/actions/review.md) |
| `generate` | (none) — reads `.context/` | CLAUDE.md (or agent-specific config) | [generate.md](references/actions/generate.md) |

## Flow

```
First time (kickoff):
  init → generate

With existing codebase:
  init (bootstraps from artifacts) → generate

Maintenance:
  update {axis} → generate

Health check:
  review → update (if drift detected) → generate

Strategic challenge:
  analyze → update (if insights) → generate

Quick regeneration:
  generate (reads existing .context/)
```

Each action is **standalone** — usable independently without requiring prior actions.

## Philosophy

- **Kickoff, not questionnaire**: The interview is a conversation. Agent adapts to answers, challenges vague responses, and knows when to stop.
- **Objectives over scripts**: Axis templates define WHAT to know, not WHAT to ask. The agent decides HOW based on the conversation.
- **Bootstrap first**: Read existing artifacts (package.json, README, git log, file structure) BEFORE asking questions. Interview fills gaps, not blanks.
- **Checkpoint per axis**: Write docs immediately after each axis completes. Session can be interrupted and resumed.
- **TBD is a valid answer**: Early-stage projects can't answer everything. "Not decided yet" is captured explicitly, never fabricated.
- **Intention over description**: Capture WHY decisions were made, not just WHAT exists. Agents can infer the "what" from code — they can't infer the "why".
- **Merge, never overwrite**: CLAUDE.md generation always diffs against existing content. Never silently destroy user's custom sections.
- **Adapt to project**: Not every project needs 7 axes. Detect project type and propose relevant axes.

## Axes

| Axis | Always? | Content | Depth |
|------|---------|---------|-------|
| **Product** | Yes | Vision, users, features, roadmap | Deep — agent challenges |
| **Engineering** | Yes | Stack, architecture, conventions | Deep — agent challenges |
| **Decisions** | Yes | ADR-lite records of key choices | Medium |
| **Design** | If UI | Principles, tokens | Standard |
| **Business** | If commercial | Model, landscape | Standard |
| **Marketing** | If PLG/content | Positioning | Light |
| **Team** | If >1 person | Roles, process, tools | Light |

## Interview Conduct

The agent follows the rules in [interview.md](references/principles/interview.md). Key rules:

- Max 2 questions per message
- Max 3 follow-ups per objective before accepting TBD
- Product and Engineering: insist and challenge vague answers
- Other axes: accept "not defined yet" and move on
- Reformulate understanding before moving to next axis
- Generate axis docs immediately when axis is complete (checkpoint)
- Never invent information — if it wasn't said, it's not written

## Bootstrap Protocol

Before starting the interview, scan the project for existing artifacts:

```
1. SCAN sources (in order):
   - package.json / Cargo.toml / pyproject.toml → stack, deps, scripts
   - README.md → project description, setup instructions
   - Git log (last 20 commits) → recent activity, conventions
   - File structure (top 2 levels) → architecture patterns
   - Existing CLAUDE.md → current context, conventions
   - .env.example → services, integrations
   - CI config (.github/workflows, etc.) → infrastructure

2. STORE scan results as bootstrap_context (do NOT write files yet):
   - Pre-fill data for stack, architecture, conventions
   - Note what was auto-detected vs what needs confirmation
   - Files are written only at checkpoint time (after axis interview + user confirmation)

3. INTERVIEW fills gaps:
   - Show what was auto-detected: "I found X — is this accurate?"
   - Ask only about what couldn't be inferred
   - Product axis is always fully conversational (can't be inferred from code)
```

## Action Router

```
User input
  |
  |- "init", "kickoff", "kickoff init",
  |  "project setup", "onboard project"       -> Load references/actions/init.md
  |
  |- "update product", "update engineering",
  |  "kickoff update {axis}"                  -> Load references/actions/update.md
  |
  |- "analyze context", "challenge context",
  |  "kickoff analyze"                        -> Load references/actions/analyze.md
  |
  |- "review context", "check context",
  |  "kickoff review"                         -> Load references/actions/review.md
  |
  |- "generate", "generate claude",
     "kickoff generate"                       -> Load references/actions/generate.md
```

**Loading rule:** Read the action file BEFORE executing. The action file contains all logic, references, and templates needed.

## Output Structure (in user's project)

```
.context/
|-- product/
|   |-- vision.md
|   |-- users.md
|   |-- features.md
|   +-- roadmap.md
|-- engineering/
|   |-- stack.md
|   |-- architecture.md
|   +-- conventions.md
|-- decisions/
|   |-- 001-{decision-slug}.md
|   +-- ...
|-- design/                   (if UI)
|   |-- principles.md
|   +-- tokens.md
|-- business/                 (if commercial)
|   |-- model.md
|   +-- landscape.md
|-- marketing/                (if PLG/content)
|   +-- positioning.md
|-- team/                     (if >1 person)
|   +-- team.md
+-- _meta.md
```

## Principles

| Principle | Purpose | Reference |
|-----------|---------|-----------|
| Interview conduct | How to run the conversational interview | [interview.md](references/principles/interview.md) |

## Templates

### Axis Templates (interview objectives + strategies)

| Template | Used by | Reference |
|----------|---------|-----------|
| Product | `init`, `update` | [product.md](references/templates/axes/product.md) |
| Engineering | `init`, `update` | [engineering.md](references/templates/axes/engineering.md) |
| Decisions | `init`, `update` | [decisions.md](references/templates/axes/decisions.md) |
| Design | `init`, `update` | [design.md](references/templates/axes/design.md) |
| Business | `init`, `update` | [business.md](references/templates/axes/business.md) |
| Marketing | `init`, `update` | [marketing.md](references/templates/axes/marketing.md) |
| Team | `init`, `update` | [team.md](references/templates/axes/team.md) |

### File Templates (generated output files)

| Template | Used by | Reference |
|----------|---------|-----------|
| Context file schema | `init`, `update` | [context-file.md](references/templates/context-file.md) |
| CLAUDE.md | `generate` | [claude-md.md](references/templates/claude-md.md) |
| Meta file | `init` | [meta.md](references/templates/meta.md) |
| Decision record | `init`, `update` | [decision-record.md](references/templates/decision-record.md) |

### Output Templates (chat status messages)

| Template | Used by | Reference |
|----------|---------|-----------|
| Init output | `init` | [init-output.md](references/templates/outputs/init-output.md) |
| Update output | `update` | [update-output.md](references/templates/outputs/update-output.md) |
| Analyze output | `analyze` | [analyze-output.md](references/templates/outputs/analyze-output.md) |
| Review output | `review` | [review-output.md](references/templates/outputs/review-output.md) |
| Generate output | `generate` | [generate-output.md](references/templates/outputs/generate-output.md) |

## Configuration

All behavior is configurable by editing the skill files directly.

| What to change | Edit |
|----------------|------|
| Action logic | `references/actions/{action}.md` |
| Interview rules | `references/principles/interview.md` |
| Axis objectives | `references/templates/axes/{axis}.md` |
| Output formats | `references/templates/{template}.md` |

## References

Actions:
- [Init](references/actions/init.md) | [Update](references/actions/update.md) | [Analyze](references/actions/analyze.md) | [Review](references/actions/review.md) | [Generate](references/actions/generate.md)

Principles:
- [Interview Conduct](references/principles/interview.md)

Templates (axes):
- [Product](references/templates/axes/product.md) | [Engineering](references/templates/axes/engineering.md) | [Decisions](references/templates/axes/decisions.md) | [Design](references/templates/axes/design.md) | [Business](references/templates/axes/business.md) | [Marketing](references/templates/axes/marketing.md) | [Team](references/templates/axes/team.md)

Templates (files):
- [Context File](references/templates/context-file.md) | [CLAUDE.md](references/templates/claude-md.md) | [Meta](references/templates/meta.md) | [Decision Record](references/templates/decision-record.md)

Output (chat):
- [Init](references/templates/outputs/init-output.md) | [Update](references/templates/outputs/update-output.md) | [Analyze](references/templates/outputs/analyze-output.md) | [Review](references/templates/outputs/review-output.md) | [Generate](references/templates/outputs/generate-output.md)
