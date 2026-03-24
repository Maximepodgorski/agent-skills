# Engineering Axis Template

> **Agent:** Load this during `init` or `update` for the Engineering axis. This defines WHAT to know, not WHAT to ask.

## Depth: Deep (challenge vague answers, cross-reference with bootstrap scan)

## Objectives

At the end of this axis, you must know:

- [ ] **Language(s)**: Primary language(s) with EXACT versions
- [ ] **Framework(s)**: Frontend, backend, with EXACT versions and configuration (e.g., Next.js App Router vs Pages Router)
- [ ] **Database**: Type, provider, ORM/client, version
- [ ] **Hosting/Infra**: Where it runs (Vercel, AWS, self-hosted, etc.)
- [ ] **Package manager**: npm, pnpm, yarn, bun (with workspace config if monorepo)
- [ ] **Repo structure**: Monorepo vs multi-repo, main directories, what lives where
- [ ] **Architecture pattern**: Layers (frontend/API/DB), patterns (MVC, composables, services, etc.)
- [ ] **Data flow**: How data moves through the system (e.g., "user action → API route → DB → revalidate")
- [ ] **Naming conventions**: Files (kebab-case, PascalCase), variables, components, commits
- [ ] **Lint/format**: Tools and exact configs (ESLint flat config, Prettier, Biome, etc.)
- [ ] **Test setup**: Framework, patterns (unit/integration/e2e), coverage expectations
- [ ] **Dev commands**: EXACT commands to start, test, build, deploy
- [ ] **Key constraints**: Performance budgets, browser support, known tech debt

## Opening

If bootstrap scan found artifacts (PREFERRED — always start here):

```
"I scanned your project. Here's what I found:

  Stack: [language] [version] + [framework] [version]
  Package manager: [pm] (from [lock file])
  Structure: [monorepo/flat] — [top-level dirs]
  Lint: [tools] (from config files)
  Tests: [framework] (from deps)
  Scripts: [dev command], [build command], [test command]

What's wrong or missing? And tell me about your architecture —
how do the pieces connect?"
```

If NO bootstrap data (rare):

```
"I couldn't find package.json or similar config files.
Walk me through your stack from scratch:

What language? What framework? What version?
How is the project structured? What's the dev workflow?"
```

**Never start with "Describe your technical stack."** That's too vague and produces generic answers. Either present bootstrap findings for confirmation, or ask specific targeted questions.

## Strategies (adaptive follow-up)

### Level 1 — Challenge thin stack answers

```
IF stack is vague ("it's React" / "we use Python"):
  -> "I need specifics for the agent to generate correct code:
      - React: which version? Next.js, Remix, Vite, or CRA?
      - TypeScript or JavaScript? Strict mode?
      - State management: Redux, Zustand, Jotai, Context, none?
      - Styling: Tailwind, CSS Modules, styled-components, SCSS?"

IF no backend mentioned:
  -> "Where does business logic run?
      - API routes in [framework]? (Next.js API routes, Nuxt server/)
      - Separate backend service? (Express, Fastify, Django, Rails)
      - BaaS? (Supabase, Firebase, Convex)"

IF no database mentioned:
  -> "Where does data live?
      - SQL: Postgres, MySQL, SQLite?
      - NoSQL: MongoDB, DynamoDB?
      - BaaS: Supabase, Firebase, PlanetScale?
      - ORM: Prisma, Drizzle, TypeORM, raw SQL?"

IF repo structure unclear:
  -> "What are the top-level folders? I'll compare with what I scanned.
      Is there a clear separation between frontend/backend/shared?"
```

### Level 2 — Dig deeper on architecture and conventions

```
IF architecture is vague ("standard Next.js structure"):
  -> "Where does business logic live? Not in components I hope.
      Do you have services? Composables? A repository pattern?
      How does data flow from user action to database and back?"

IF conventions not mentioned:
  -> "How do you name things? I need to match your patterns:
      - Files: kebab-case, PascalCase, camelCase?
      - Components: Button.tsx or button.tsx? Index barrels?
      - Commits: conventional commits? Or free-form?"

IF bootstrap found lint configs but user hasn't mentioned:
  -> "I see [ESLint/Prettier/Biome config]. Is it actively enforced?
      Any custom rules? Is there a pre-commit hook?"

IF no test setup:
  -> "Do you have tests? Be honest — no tests is valid context.
      If yes: what framework? What do you test? What's coverage like?
      If no: is there a plan to add them?"

IF no dev commands:
  -> "Give me the EXACT commands. Not 'npm start' — the actual commands
      from package.json. Dev server, tests, build, lint, typecheck.
      These go directly into CLAUDE.md."

IF tech debt or constraints:
  -> "Any areas of the codebase I should know are messy?
      Performance constraints? Browser support requirements?
      Things you'd warn a new developer about?"
```

### Level 3 — Cross-reference with bootstrap

```
IF bootstrap found something user didn't mention:
  -> "I noticed [X] in your project but you didn't mention it:
      - [dependency] in package.json — are you actively using it?
      - [config file] exists — is this current?
      - [folder structure] suggests [pattern] — is that intentional?"

IF user's answer contradicts bootstrap:
  -> "You said [X] but I found [Y] in [file]. Which is correct?
      Did something change recently?"
```

## Completion signals

```
All 13 objectives checked with specifics    -> "Engineering context is thorough. Moving on."
>= 9 objectives with specifics              -> "Good coverage. Let me show you what I'll write."
< 9 after all follow-ups                    -> Mark missing as TBD. "These gaps matter — the agent
                                                will guess wrong without them. Fill them when you can
                                                with 'kickoff update engineering'."
```

**Important:** An objective is only "checked" when the answer is specific enough for an agent to act on. "React" alone does NOT check the Framework objective. "React 18 + Next.js 14 App Router + TypeScript strict" does.

## Output docs

This axis produces 3 files:

| File | Content |
|------|---------|
| `engineering/stack.md` | Languages + exact versions, frameworks + versions + config details, DB + provider + ORM + client version, hosting per service, package manager + lock file, key dependencies with versions |
| `engineering/architecture.md` | Repo structure (actual dirs as tree), layers with responsibility, data flow (ASCII diagram), key patterns used, feature folder conventions |
| `engineering/conventions.md` | Naming (files, components, variables, commits) with examples, lint/format tools + config file paths, test setup + coverage expectations, EXACT dev commands (copy-pasteable), known constraints + tech debt areas |

**All output docs must include an Open Questions section** — see `references/templates/context-file.md` for the schema and rich examples.

### Output quality checklist

Before writing each engineering doc, verify:

```
stack.md:
  ✓ Every language/framework has an EXACT version number
  ✓ Framework config is specified (e.g., "App Router" not just "Next.js")
  ✓ DB section includes client library AND how complex queries are handled
  ✓ Key dependencies include version AND what they're used for

architecture.md:
  ✓ Repo structure uses actual directory tree (verified from scan)
  ✓ Each layer has a clear responsibility statement
  ✓ Data flow shows the full path from user action to DB and back
  ✓ Patterns are described with WHERE they apply, not just named

conventions.md:
  ✓ Every naming convention has a concrete example
  ✓ Dev commands are EXACT (copy-paste from terminal, not approximations)
  ✓ Test setup describes WHAT gets tested, not just "we use Vitest"
  ✓ Tech debt items describe WHERE and WHY (not just "some debt exists")
```
