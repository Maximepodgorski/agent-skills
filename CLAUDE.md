# Agent Skills

Reusable knowledge base for AI coding agents. Skills = packaged domain expertise (standards, conventions, patterns) that agents absorb and follow.

**Pure Markdown. No build. No runtime. Agent-agnostic.**

## Project Structure

```
agent-skills/
├── CLAUDE.md
├── README.md
├── LICENSE              (MIT)
├── component/           ← Design System workflow (6 actions, 5 principles)
│   ├── README.md
│   ├── SKILL.md
│   ├── CHANGELOG.md
│   └── references/
│       ├── actions/      ← spec, doc, dev, review, spec-review, audit
│       ├── principles/   ← 5 DS standards
│       └── templates/    ← Output formats (files + chat messages)
├── design-screen/       ← Screen composition workflow (6 actions, 4 principles)
│   ├── README.md
│   ├── SKILL.md
│   └── references/
│       ├── actions/      ← init, spec, spec-review, craft, ship, review
│       ├── principles/   ← 4 screen standards
│       └── templates/    ← Output formats (files + chat messages)
└── kickoff/             ← Project context onboarding (6 actions, 7 axes)
    ├── README.md
    ├── SKILL.md
    └── references/
        ├── actions/      ← init, update, todo, analyze, review, generate
        ├── principles/   ← Interview conduct rules
        └── templates/
            ├── axes/     ← 7 axis interview guides
            ├── outputs/  ← 6 chat message formats
            └── *.md      ← File schemas (context-file, claude-md, meta, decision-record)
```

## Skills

| Skill | Actions | What it does |
|-------|---------|--------------|
| **component** | spec, doc, dev, review, spec-review, audit | Design System component workflow — Figma-first, from spec to audit |
| **design-screen** | init, spec, spec-review, craft, ship, review | Screen composition — ASCII wireframes, Figma draft, ship loop |
| **kickoff** | init, update, todo, analyze, review, generate | Project context onboarding — conversational interview → `.context/` docs → CLAUDE.md |

## Conventions

- **Language:** Markdown + YAML frontmatter only — no code files
- **Commits:** Conventional Commits (`feat:`, `fix:`, `docs:`, `style:`)
- **Branch:** `main` only for now
- **Skill structure:** Each skill gets its own top-level directory with `SKILL.md` as entry point
- **Versioning:** SemVer (`vMAJOR.MINOR.PATCH`) — tag before release, verify tag on publish

## Skill Anatomy

```
skill-name/
├── README.md             ← Quick start for humans
├── SKILL.md              ← Agent entry point (triggers, routing, loading)
└── references/
    ├── actions/           ← What the skill can do
    ├── principles/        ← Domain knowledge / standards
    └── templates/         ← Output format definitions
```

## Adding a New Skill

1. Create `skill-name/` directory at root
2. Add `SKILL.md` with: description, triggers, action router, reference loading instructions
3. Add `references/actions/` with one `.md` per action
4. Add `references/principles/` for domain standards
5. Add `references/templates/` for output formats
6. Add `skill-name/README.md` for human-readable quick start
7. Update root `README.md` to list the new skill
8. Update this `CLAUDE.md` project structure and skills table

## Key Design Decisions

- **Figma-first** for the component skill — design context drives everything
- **Bootstrap-first** for the kickoff skill — scan artifacts before asking questions
- **Human-in-the-loop** — skills never silently create tokens or make irreversible choices
- **Reuse > Create** — scan existing codebase before generating anything new
- **Stack-agnostic** — adapt to the project's conventions, never impose
- **Principles over opinions** — grounded in established standards (Atomic Design, BEM, DTCG, WCAG)
- **No artificial limits** — context files and CLAUDE.md have no line caps, only quality filters ("Can an agent act on this?")
- **Checkpoint per axis** — kickoff writes docs after each axis, session can be interrupted and resumed
- **Merge, never overwrite** — CLAUDE.md generation preserves custom sections

## Installation (for users)

```bash
npx skills add Maximepodgorski/agent-skills                          # all skills
npx skills add Maximepodgorski/agent-skills --skill component        # single skill
npx skills add Maximepodgorski/agent-skills --skill kickoff          # single skill
npx skills add Maximepodgorski/agent-skills --skill component -g     # global
```
