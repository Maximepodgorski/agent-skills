# Changelog

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
