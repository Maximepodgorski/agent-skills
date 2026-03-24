# Review Action

> **Agent:** Load this file when `review` triggers. Scans the codebase to detect drift between `.context/` docs and project reality.

Evidence-based drift detection. Every finding includes file path + specific discrepancy.

---

## Input Detection

```
.context/ exists?
  NO  -> "No context found. Run 'kickoff init' first."
  YES -> Continue

Project has a codebase? (src/, lib/, app/, or similar directories exist?)
  NO  -> "No codebase to compare against. Review is most useful after code exists.
          Want to run 'kickoff analyze' instead? (challenges docs without codebase comparison)"
  YES -> Continue
```

## Step 0: Load Context Docs

Read all `.context/` files and `_meta.md`.
Note last update date per axis.

## Step 1: Engineering Drift Detection

Compare `engineering/` docs against codebase:

```
stack.md checks:
  [ ] Stated language matches file extensions found? (.ts, .vue, .rs, .py)
  [ ] Stated framework matches dependencies? (check package.json, Cargo.toml, etc.)
  [ ] Stated versions match actual versions in lock files?
  [ ] Stated DB matches connection configs or ORM configs?
  [ ] Stated package manager matches lock file type? (package-lock, pnpm-lock, yarn.lock)

architecture.md checks:
  [ ] Stated directory structure matches actual top-level dirs?
  [ ] Stated layers/patterns match actual file organization?
  [ ] Stated monorepo/multi-repo matches reality?

conventions.md checks:
  [ ] Stated naming convention matches actual file names? (sample 10 files)
  [ ] Stated commit convention matches recent git log? (sample 10 commits)
  [ ] Stated lint config matches actual lint config files?
  [ ] Stated test framework matches test file imports?
  [ ] Stated dev commands match package.json scripts?
```

### Output format per finding

```
[DRIFT:CRITICAL] {doc}.md states "{stated value}" -- actual: {actual value}
                 Evidence: {file path or command that proves it}
                 Suggestion: Update {doc}.md
                 Use CRITICAL when: stated framework/language/DB is wrong

[DRIFT:HIGH]     {doc}.md states "{stated value}" -- actual: {actual value}
                 Evidence: {file path or command that proves it}
                 Suggestion: Update {doc}.md
                 Use HIGH when: version mismatch, missing dependency, wrong command

[DRIFT:MEDIUM]   {doc}.md states "{stated value}" -- actual: {actual value}
                 Evidence: {file path or command that proves it}
                 Suggestion: Update {doc}.md
                 Use MEDIUM when: naming drift, convention partially followed, minor mismatch

[OK]    {doc}.md -- {check description} -- consistent

[SKIP]  {check description} -- cannot verify (no data available)
```

### Severity guide

```
Engineering severity:
  CRITICAL: Application framework, language, or DB is wrong (Vue→React, Postgres→MongoDB)
            → Agent will generate completely wrong code
  HIGH:     Tooling change, version mismatch, wrong command (Jest→Vitest, webpack→Vite, npm→pnpm)
            → Agent will generate broken code
  MEDIUM:   Convention drift, loosely followed patterns (naming, lint rules, commit style)
            → Agent will generate inconsistent code

Product severity:
  CRITICAL: Feature marked "existing" is completely gone from codebase
            → Agent references non-existent functionality
  HIGH:     Feature partially removed, renamed, or user role changed
            → Agent makes incorrect assumptions
  MEDIUM:   Feature status outdated (planned → exists, or exists but renamed)
            → Agent has stale understanding

Design severity:
  HIGH:     Token values are wrong (colors, fonts, breakpoints don't match actual config)
            → Agent generates wrong visual code
  MEDIUM:   Token naming drift (tokens exist but naming diverged)
            → Agent generates inconsistent token references
```

## Step 2: Product Drift Detection

Compare `product/` docs against codebase (lighter — product context is harder to verify from code):

```
features.md checks:
  [ ] Features marked "planned" — do they exist in code now? (search for routes, components, APIs)
  [ ] Features marked "existing" — do they still exist? (not deleted or renamed)

users.md checks:
  [ ] Stated user types — do roles/permissions in code match? (search for role enums, auth guards)
```

## Step 3: Design Drift Detection (if design axis exists)

```
tokens.md checks:
  [ ] Stated colors match actual CSS vars / Tailwind config / theme files?
  [ ] Stated fonts match actual font imports or CSS?
  [ ] Stated breakpoints match actual media queries or Tailwind config?
```

## Step 4: CLAUDE.md Sync Check

```
_meta.md has last_generated date?
  YES -> Compare last_generated vs most recent axis update date
         IF any axis updated AFTER last_generated:
           -> [DESYNC] CLAUDE.md is stale — .context/ was updated on {date} but CLAUDE.md was last generated on {date}
              Suggestion: Run 'kickoff generate' to sync
         IF last_generated is more recent:
           -> [OK] CLAUDE.md is in sync with .context/
  NO  -> [SKIP] No generation date found — run 'kickoff generate' to create CLAUDE.md
```

## Step 5: Freshness Check

For each axis in `_meta.md`:

```
Last updated < 30 days ago   -> [FRESH] {axis} -- updated {N} days ago
Last updated 30-90 days ago  -> [AGING] {axis} -- updated {N} days ago, consider reviewing
Last updated > 90 days ago   -> [STALE] {axis} -- updated {N} days ago, likely outdated
```

Cross-reference with git activity:

```
Commits since last context update > 20 -> [WARNING] {N} commits since last update
Files changed since last update > 30   -> [WARNING] significant codebase changes detected
```

## Step 6: Report

Compile all findings into a report:

```
Summary:
  - {N} drift issues found ({N} critical, {N} high, {N} medium)
  - {N} axes fresh, {N} aging, {N} stale
  - CLAUDE.md sync: {OK / DESYNC / SKIP}
  - {N} checks passed, {N} skipped

Drift findings (sorted by severity):
  [list of DRIFT findings]

Freshness:
  [list of axis freshness status]

Recommendation:
  -> "Run 'kickoff update engineering' to fix stack.md drift"
  -> "Run 'kickoff update product' — features.md is 45 days old with 30+ commits since"
```

## Output

Follow the output template in `references/templates/outputs/review-output.md`.

## Rules

1. EVERY finding must include evidence — file path, command output, or specific comparison
2. NEVER produce vague findings like "docs may be stale" — always be specific
3. SKIP checks that can't be verified (e.g., no package.json means skip stack version checks)
4. ALWAYS include the freshness check — even if zero drift is found
5. Don't auto-fix — present findings and suggest which `update` commands to run
6. Business, Marketing, Team axes: freshness check only (can't verify from codebase)
7. Engineering axis: full drift detection (most verifiable from code)
8. Run efficiently — minimize redundant reads, batch file reads where possible. Prioritize engineering drift (highest signal) if constrained
