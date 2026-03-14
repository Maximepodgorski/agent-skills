# Agent Skills

Reusable knowledge base for AI coding agents. Skills = packaged domain expertise (standards, conventions, patterns) that agents absorb and follow.

**Pure Markdown. No build. No runtime. Agent-agnostic.**

## Project Structure

```
agent-skills/
├── CLAUDE.md
├── README.md
├── LICENSE              (MIT)
└── component/           ← First skill (Design System workflow)
    ├── README.md
    ├── SKILL.md          ← Entry point + action router
    └── references/
        ├── actions/      ← 5 action implementations (.md)
        ├── principles/   ← 5 DS standards (.md)
        └── templates/    ← Output formats (files + chat messages)
```

## Conventions

- **Language:** Markdown + YAML frontmatter only — no code files
- **Commits:** Conventional Commits (`feat:`, `fix:`, `docs:`, `style:`)
- **Branch:** `main` only for now
- **Skill structure:** Each skill gets its own top-level directory with `SKILL.md` as entry point

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

## Key Design Decisions

- **Figma-first** for the component skill — design context drives everything
- **Human-in-the-loop** — skills never silently create tokens or make irreversible choices
- **Reuse > Create** — scan existing codebase before generating anything new
- **Stack-agnostic** — adapt to the project's conventions, never impose
- **Principles over opinions** — grounded in established standards (Atomic Design, BEM, DTCG, WCAG)

## Installation (for users)

```bash
npx skills add Maximepodgorski/agent-skills                          # all skills
npx skills add Maximepodgorski/agent-skills --skill component        # single skill
npx skills add Maximepodgorski/agent-skills --skill component -g     # global
```
