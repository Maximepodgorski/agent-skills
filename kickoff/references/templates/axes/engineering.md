# Engineering Axis Template

> **Agent:** Load this during `init` or `update` for the Engineering axis. This defines WHAT to know, not WHAT to ask.

## Depth: Deep (challenge vague answers, cross-reference with bootstrap scan)

## Objectives

At the end of this axis, you must know:

- [ ] **Language(s)**: Primary language(s) with versions
- [ ] **Framework(s)**: Frontend, backend, with versions
- [ ] **Database**: Type, provider, ORM/client
- [ ] **Hosting/Infra**: Where it runs (Vercel, AWS, self-hosted, etc.)
- [ ] **Package manager**: npm, pnpm, yarn, bun, cargo, pip, etc.
- [ ] **Repo structure**: Monorepo vs multi-repo, main directories, what lives where
- [ ] **Architecture pattern**: Layers (frontend/API/DB), patterns (MVC, composables, services)
- [ ] **Data flow**: How data moves through the system (can be simple ASCII)
- [ ] **Naming conventions**: Files (kebab-case, PascalCase), variables, components, commits
- [ ] **Lint/format**: Tools and configs (ESLint, Prettier, Biome, etc.)
- [ ] **Test setup**: Framework, patterns, coverage expectations
- [ ] **Dev commands**: How to start, test, build, deploy
- [ ] **Key constraints**: Performance budgets, browser support, known tech debt

## Opening

```
"Describe your technical stack and how the project is organized."
```

If bootstrap scan found artifacts, start with:

```
"I scanned your project and found:
  - [language] with [framework] (from package.json)
  - [package manager] (from lock file)
  - [structure] (from directory scan)
  - [lint tools] (from config files)

Is this accurate? What am I missing?"
```

## Strategies (adaptive follow-up)

```
IF stack is vague ("it's React"):
  -> "Which React version? Next.js, Remix, Vite, CRA?
      TypeScript or JavaScript? What state management?"

IF no backend mentioned:
  -> "Is there a backend? API routes in the framework, separate service, BaaS?"

IF no database mentioned:
  -> "Where does data live? Postgres, MongoDB, Supabase, Firebase, local files?"

IF repo structure unclear:
  -> "Can you describe the main folders? Or is it a standard [framework] structure?"

IF architecture is vague:
  -> "How do the pieces connect? Where does business logic live?
      Do you have services, composables, repositories, or is it all in components?"

IF no conventions mentioned:
  -> "How do you name files? Components? What commit convention do you use?"

IF bootstrap found lint configs but user hasn't mentioned:
  -> "I see [ESLint config]. Are you actively enforcing this?
      Any custom rules I should know about?"

IF no test setup:
  -> "Do you have tests? What framework? What do you test — unit, integration, e2e?"

IF no dev commands:
  -> "How do you start the dev server? Run tests? Build for production?"

IF tech debt or constraints mentioned:
  -> "Can you elaborate? What's the impact? Are there areas I should avoid touching?"
```

## Completion signals

```
All 13 objectives checked       -> "Engineering context is thorough. Moving on."
>= 9 objectives checked         -> "Good coverage. Anything else before we move on?"
< 9 after all follow-ups        -> Mark missing as TBD. Note which are most impactful to fill later.
```

## Output docs

This axis produces 3 files:

| File | Content |
|------|---------|
| `engineering/stack.md` | Languages, frameworks, versions, DB, hosting, package manager |
| `engineering/architecture.md` | Repo structure, layers, data flow (ASCII diagram if possible), key patterns |
| `engineering/conventions.md` | Naming, lint/format, commits, test setup, dev commands, constraints |
