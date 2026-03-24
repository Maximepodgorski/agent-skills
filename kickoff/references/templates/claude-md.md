# CLAUDE.md Template

> **Agent:** Follow this template when generating CLAUDE.md from `.context/` docs. No line limit — include all actionable context.

## Template

```markdown
<!-- context:generated -->
# {Project Name}

{1-sentence description from product/vision.md}

## Stack

{From engineering/stack.md — languages, frameworks, versions, DB, hosting}

## Architecture

{From engineering/architecture.md — repo structure, layers}

{ASCII diagram if available}

## Conventions

{From engineering/conventions.md — naming, lint, commits, commands}

### Key Commands

{Dev, test, build, deploy commands}

## Decisions

{From decisions/*.md — 1 line per decision: "title — rationale"}

## Product

### Vision

{From product/vision.md — problem, solution, differentiation in 3-5 bullets}

### Users

{From product/users.md — personas in 1-2 bullets each}

### Features

{From product/features.md — current features with status and user stories}

### Roadmap

{From product/roadmap.md — current sprint + next milestones with exit criteria}

## Design

{From design/ — principles + key tokens. Omit section if axis doesn't exist}

## Business

{From business/ — model + positioning in 2-3 bullets. Omit if doesn't exist}

## Marketing

{From marketing/ — positioning + tone in 1-2 bullets. Omit if doesn't exist}

## Team

{From team/ — roles + process in 2-3 bullets. Omit if doesn't exist}
<!-- /context:generated -->
```

## Layout vs Priority

The template above defines **reading order** (Stack → Architecture → Conventions → ... → Team) — optimized for human/agent readability by grouping related sections.

## Content Depth per Section

How much detail to include from each axis:

```
FULL DETAIL:    Engineering conventions (agents use this every task)
                Stack + architecture (agents need this for every file)
                Decisions (prevents bad suggestions)
                Features + roadmap (current state + what's next)
                Product vision (intent alignment)

MODERATE:       Design (principles + key tokens)
                Business (model + positioning)

SUMMARY:        Marketing (core message + tone)
                Team (roles + process)
```

## Merge Markers

When generating into an existing CLAUDE.md:

1. Look for `<!-- context:generated -->` and `<!-- /context:generated -->` markers
2. Replace ONLY the content between these markers
3. Preserve everything outside the markers (user's custom sections)
4. If no markers exist (first generation into existing file):
   - Add generated content at the TOP of the file
   - Wrap in markers
   - Preserve all existing content below

## Section Omission

- If an axis has no docs or only TBD content: omit the section entirely
- If a section would be < 2 lines: merge it into the nearest related section
- Never include empty headers
