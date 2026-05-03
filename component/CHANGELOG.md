# Changelog

## v1.2 — 2026-05-03

### Renamed: `dev` → `ship`

The `dev` action is now `ship`. Same intent, clearer name — it ships the component from spec to production-ready code.

**Migration:** Replace `component dev` with `component ship` in your workflows. All internal references updated. Files renamed: `references/actions/dev.md` → `ship.md`, `references/templates/dev-output.md` → `ship-output.md`.

### Spec & Ship: writing-plans inspired

Spec and ship now follow the **writing-plans / executing-plans** pattern from superpowers. The spec doubles as an executable implementation plan; ship executes it task-by-task in TDD discipline.

**Spec template additions:**
- Header: `Goal` (1 sentence), `Architecture` (2-3 sentences), `Tech Stack` (detected from codebase)
- File Structure now uses `Create / Modify / Test` exact paths
- New **Implementation Tasks** section: bite-sized TDD tasks (write failing test → run → implement → run → commit)
- No-placeholders rule enforced (no "TBD", no `// ...`, no "similar to Task N")
- Self-review checklist before saving (coverage, placeholders, type consistency, tokens, commands, granularity)

**Spec action:**
- Generates Implementation Tasks based on detected stack (test runner, lint cmd, file extensions)
- Runs the self-review checklist inline before writing the file

**Ship action:**
- Renamed internal flow: `LOOP` → `PLAN mode` (executes Implementation Tasks one by one)
- New execution mode choice: **Subagent-Driven** (fresh agent per task, recommended for >5 tasks) or **Inline** (in main thread, recommended for ≤5 tasks)
- Each step is 2-5 minutes: write failing test, run, implement, run, commit
- Spec is the source of truth for resumability — checkboxes persist across sessions
- Spec drift handling: deviations logged in Notes, structural drift requires user input
- TDD discipline: never skip the failing-test step, never amend commits, never rewrite the spec silently

**Ship output:**
- Per-task update with checked steps + commit reference
- Checkpoint format every 2-3 tasks
- Subagent-Driven runs prefix updates with `[subagent N]`
- New "Spec Missing Tasks" exit state for legacy specs without an Implementation Tasks section

### Migration

Existing specs (v1.1 format) still work in ONE-SHOT mode. To upgrade an existing spec, re-run `/component spec {name}` — the action detects the existing spec and offers Overwrite / Update / Cancel.

---

## v1.1 — 2026-03-14

### Renamed: `structure` → `spec`

The `structure` action is now `spec`. Same behavior, clearer name — it generates a component architecture spec from Figma or manual input.

**Migration:** Replace `component structure` with `component spec` in your workflows. All internal references updated.

### New: `spec-review` action

Multi-perspective spec review that launches **4 expert reviewers in parallel** for fast, thorough validation before implementation:

| Perspective | What it checks |
|-------------|----------------|
| **Front Engineer** | Implementability, prop types, edge cases, testability |
| **DS Manager** | Naming consistency, token coverage, composition, API surface |
| **Accessibility Specialist** | WCAG 2.1 AA, keyboard nav, ARIA, screen reader, contrast |
| **Product Designer** | Design intent fidelity, visual states, responsive, content flexibility |

Each perspective produces its own verdict. Results are consolidated into a single report:

- **APPROVED** — all 4 perspectives positive
- **APPROVED WITH NOTES** — no critical issues, minor warnings
- **NEEDS REVISION** — no critical but >3 warnings
- **BLOCKED** — any critical finding

Cross-perspective conflicts are flagged, not auto-resolved — the user decides.

### Updated workflow

```
Before:  structure → doc → dev → review
After:   spec → spec-review → doc → dev → review
```

### Summary

- 5 → 6 actions
- `structure` renamed to `spec`
- New `spec-review` action with 4 parallel perspectives
- All references updated across actions, templates, and outputs

---

## v1.0 — 2026-02-22

Initial release. 5 actions: structure, doc, dev, review, audit.
