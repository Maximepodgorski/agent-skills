# Init Action

> **Agent:** Load this file when `init` triggers. Also load `references/principles/interview.md` for interview conduct rules.

First-time project context kickoff. Conversational interview that produces structured docs in `.context/`.

---

## Input Detection

```
.context/ exists?
  NO  -> Continue (fresh init)
  YES -> Check completeness:
    All axes from _meta.md have docs?
      YES (fully populated) ->
        "You already have context docs. Want to redo from scratch, or update a specific axis?"
          Redo -> Delete .context/, continue with init
          Update -> Redirect to update action
          Cancel -> Stop
      NO (partially populated) ->
        "I found an incomplete context. Completed: [list]. Missing: [list].
         Want to resume from where we left off, or start over?"
          Resume -> Skip completed axes, start from first missing
          Start over -> Delete .context/, continue with init
          Cancel -> Stop
```

## Step 0: Bootstrap Scan

Before asking ANY question, scan the project for existing artifacts.

```
1. Check for package manager config:
   - package.json, Cargo.toml, pyproject.toml, go.mod, composer.json
   -> Extract: language, framework, dependencies, scripts, version

2. Check for README.md:
   -> Extract: project description, setup instructions

3. Check git log (last 20 commits):
   -> Extract: commit convention, recent activity, contributors

4. Check file structure (top 2 levels):
   -> Extract: architecture pattern (monorepo, flat, layered)

5. Check existing CLAUDE.md:
   -> Extract: any existing conventions, stack info, rules

6. Check .env.example:
   -> Extract: services, integrations, third-party dependencies

7. Check lint/format configs:
   -> Extract: eslint, prettier, biome, rustfmt, etc.

8. Check CI configs:
   -> Extract: .github/workflows, .gitlab-ci.yml, etc.
```

Store scan results as `bootstrap_context`. This pre-fills Engineering axis and informs questions.

## Step 1: Project Type Detection

Ask 2 questions maximum to determine which axes apply:

```
Question 1: "What are you building? (app, library, API, CLI, site, other)"
Question 2: "What stage? (idea, MVP, early users, revenue, scale)"
```

From the answers + bootstrap scan, determine the axis map:

```
Axis map rules:
  Product     -> ALWAYS mandatory
  Engineering -> ALWAYS mandatory
  Decisions   -> ALWAYS mandatory (starts empty, populated during interview)
  Design      -> IF project has UI (app, site, dashboard)
  Business    -> IF commercial product (not library, not internal tool)
  Marketing   -> IF PLG, content-driven, or public-facing product
  Team        -> IF >1 person (detected from git contributors or user answer)
```

Present the axis map to the user:

```
"Based on what I see, here's what I'd cover:
  - Product (mandatory)
  - Engineering (mandatory)
  - Decisions (captured along the way)
  - Design (you have UI components)
  - Team (I see 3 contributors in git)

  Skip: Business, Marketing

  Sound right? Want to add or remove any?"
```

User confirms. Proceed with confirmed axes.

## Step 2: Interview — Axis by Axis

For each confirmed axis (in order):

```
1. Load the axis template: references/templates/axes/{axis}.md
2. If bootstrap_context has data for this axis:
   -> Present: "I found [X]. Is this accurate?"
   -> Confirm or correct
   -> Mark confirmed objectives as done
3. For remaining uncovered objectives:
   -> Ask the opening question from the axis template
   -> Analyze response -> check objectives covered
   -> For uncovered objectives -> follow relance strategies from template
4. When the axis template's completion signals are met OR all relances exhausted:
   -> Reformulate: "Here's what I've captured for [axis]: [summary]. Anything to add or correct?"
   -> User confirms
5. CHECKPOINT: Write .context/{axis}/*.md files immediately
   -> Follow the schema in references/templates/context-file.md
   -> Log in console: "Saved .context/{axis}/ -- [N] docs written"
```

### Axis interview order

```
1. Product     (deep -- challenge vague answers)
2. Engineering (deep -- challenge, cross-reference with bootstrap)
3. Design      (standard -- if applicable)
4. Business    (standard -- if applicable)
5. Marketing   (light -- if applicable)
6. Team        (light -- if applicable)
```

### Decisions axis — captured inline

The Decisions axis is NOT interviewed separately. Instead:

```
During Product and Engineering interviews, when the user mentions a choice:
  "We chose Postgres because..."
  "We went with monorepo because..."
  "We considered X but decided Y because..."

-> Buffer the decision internally (do NOT write files yet)
-> Acknowledge briefly at end of your message: "Noted: [decision title]"
   (this acknowledgment does NOT count as a question)
```

At the end of ALL axis interviews, present buffered decisions and write files:

```
"I captured these decisions along the way:
  - 001: Postgres over MongoDB (performance + relational data)
  - 002: Monorepo with Nx (shared code between apps)
  - 003: Supabase for auth (speed to market)

  Any other key decisions I should record?"

-> User confirms or adds more
-> Assign sequential numbers (001, 002, ...) based on final list
-> Write ALL decision files to .context/decisions/NNN-{slug}.md at once
-> Follow the template in references/templates/decision-record.md
```

## Step 3: Generate _meta.md

After all axes are complete, write `.context/_meta.md`:

```
Follow the template in references/templates/meta.md
Include:
  - Active axes
  - Date of init
  - Completion status per axis (complete / partial / TBD)
  - Bootstrap sources detected
```

## Step 4: Offer CLAUDE.md Generation

```
"Context docs are ready. Want me to generate a CLAUDE.md for this project?
  -> Yes: Run generate action
  -> Not now: Skip (user can run 'kickoff generate' later)
```

## Output

Follow the output template in `references/templates/outputs/init-output.md`.

## Rules

1. NEVER invent information — if the user didn't say it, don't write it
2. NEVER skip the bootstrap scan — always check artifacts before asking
3. ALWAYS write checkpoint files after each axis — session can be interrupted
4. ALWAYS present what was captured and ask for confirmation before writing
5. Product and Engineering: challenge vague answers (follow interview.md rules)
6. Other axes: accept TBD gracefully, don't pressure
7. Mark TBD items explicitly in docs — never leave blanks that look like omissions
8. Decisions are captured INLINE during other axes, not as a separate interview
9. Max 2 questions per message — never overwhelm
10. If resuming (partial .context/): handled by Input Detection — skip completed axes, start from first missing
