# Audit Report Template

> **Agent:** Use this template when generating output for the `audit` action. Output file: `ds/audits/active/{YYYY-MM-DD}-audit-{scope}.md`

---

```markdown
# Audit — {Scope}

> {YYYY-MM-DD} | Mode: {Codebase | Figma} | Source: {directory path or Figma link}

## Summary

| Metric | Value |
|--------|-------|
| Components scanned | {N} |
| Total findings | {N} |
| Critical | {N} |
| Warning | {N} |
| Info | {N} |
| Compliance score | {N}% ({compliant components} / {total components}) |

### Verdict: {CLEAN | NEEDS WORK | NON-COMPLIANT}

<!-- Verdict calculated per audit.md verdict algorithm. Do not redefine thresholds here. -->

## Hardcoded Values (Zero Tolerance)

<!-- CRITICAL — Every single hardcoded value listed -->

| # | Component | File:Line | Value | Category | Suggested Token |
|---|-----------|-----------|-------|----------|-----------------|
| 1 | {name} | {path}:{line} | {#hex / 16px / ...} | {color/spacing/...} | {--token-name} |

**Total hardcoded values: {N}**
{N = 0: "None found. Clean."}
{N > 0: "All values above MUST be replaced with tokens."}

## Naming Standardization

<!-- Figma mode: component + prop + value naming -->
<!-- Codebase mode: CSS class + variable + file naming -->

### {Figma: "Component & Prop Naming" | Codebase: "CSS & Variable Naming"}

| # | Component | Issue | Current | Expected | Severity |
|---|-----------|-------|---------|----------|----------|
| 1 | {name} | {non-standard prop name} | {current} | {expected} | WARNING |

## Token Usage

| # | Component | Issue | Detail | Severity |
|---|-----------|-------|--------|----------|
| 1 | {name} | {missing token ref / primitive used / duplicate} | {detail} | {CRITICAL/WARNING} |

## Composition

| # | Component | Issue | Detail | Severity |
|---|-----------|-------|--------|----------|
| 1 | {name} | {wrong atomic level / missing compound pattern} | {detail} | {WARNING/INFO} |

## Props API

| # | Component | Issue | Detail | Severity |
|---|-----------|-------|--------|----------|
| 1 | {name} | {inconsistent prop / missing type / boolean trap} | {detail} | {WARNING/INFO} |

## Accessibility

| # | Component | Issue | Detail | Severity |
|---|-----------|-------|--------|----------|
| 1 | {name} | {missing ARIA / no keyboard nav / no focus style} | {detail} | {CRITICAL/WARNING} |

## Cross-Component Consistency

<!-- Codebase mode only — skip for single Figma component audit -->

| Pattern | Consistent? | Detail |
|---------|------------|--------|
| Size scale | YES/NO | {e.g., "Button uses sm/md/lg, Input uses small/medium/large"} |
| Variant naming | YES/NO | {detail} |
| Event naming | YES/NO | {detail} |
| Token tier usage | YES/NO | {detail} |
| Export pattern | YES/NO | {detail} |

## Recommendations

### Immediate (fix now)

1. {Action — tied to critical findings}
2. {Action}

### Next (fix soon)

1. {Action — tied to warnings}
2. {Action}

### Future (improve over time)

1. {Action — tied to info findings}

## Components Scanned

| Component | Critical | Warning | Info | Status |
|-----------|----------|---------|------|--------|
| {name} | {N} | {N} | {N} | COMPLIANT / NEEDS WORK / NON-COMPLIANT |
```

---

## Generation Rules

- **Hardcoded Values section is MANDATORY** — even if empty (show "None found. Clean.")
- Every finding has: component, location, severity, and concrete fix
- Codebase mode: scan every component file, no sampling
- Figma mode: focus on naming + token usage + value standardization
- Cross-component consistency only in codebase mode
- Compliance score = components with 0 criticals / total components
- Report must be shareable — designer or DS lead can read it without context
