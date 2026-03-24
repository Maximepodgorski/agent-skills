# Analyze Action

> **Agent:** Load this file when `analyze` triggers. Launches parallel subagents to challenge the project context.

Multi-perspective analysis of `.context/` docs. Surfaces gaps, inconsistencies, risks, and opportunities.

---

## Input Detection

```
.context/ exists with at least product/ and engineering/?
  YES -> Continue
  NO  -> "No context found (or incomplete). Run 'kickoff init' first."
```

## Step 0: Load Full Context

Read ALL `.context/` files:
- All axis docs
- All decision records
- `_meta.md`

Concatenate into a single context payload for the subagents.

## Step 1: Launch 4 Perspectives in Parallel

Launch 4 subagents in parallel using the Task tool. Each receives the FULL context payload.

### Perspective 1: Devil's Advocate

```
Prompt:
"You are a Devil's Advocate reviewing a project's context documentation.

CONTEXT:
{full context payload}

Challenge every assumption. Find what's wrong, missing, or fragile.

Answer:
1. TOP 3 assumptions that could be wrong — with evidence
2. What's conspicuously ABSENT that should be documented?
3. Where is the context internally contradictory?
4. What would a new team member find confusing or incomplete?
5. What risks are not acknowledged?

For each finding:
  - Severity: CRITICAL / HIGH / MEDIUM / LOW
  - Which axis it affects
  - Suggested question to ask the user to resolve it"
```

### Perspective 2: Technical Validator

```
Prompt:
"You are a senior technical architect reviewing project context documentation.

CONTEXT:
{full context payload}

Validate technical accuracy and completeness.

Answer:
1. Are the stack choices internally consistent? (e.g., framework + DB + hosting make sense together?)
2. Are there obvious architectural gaps? (no error handling strategy, no auth model, no data model mentioned?)
3. Do the conventions match the stated stack? (e.g., BEM with React is unusual)
4. Are the decision records sound? (do the reasons hold up?)
5. What technical debt is implied but not acknowledged?

For each finding:
  - Severity: CRITICAL / HIGH / MEDIUM / LOW
  - Which axis it affects
  - Concrete suggestion to resolve"
```

### Perspective 3: Product Strategist

```
Prompt:
"You are a product strategist reviewing project context documentation.

CONTEXT:
{full context payload}

Evaluate product-market coherence.

Answer:
1. Is the problem statement clear and specific enough to build from?
2. Do the personas match the product vision? (e.g., building for devs but describing non-technical users)
3. Does the feature set serve the stated problem? (feature creep? missing critical features?)
4. Is the differentiation defensible? (would a competitor say the same thing?)
5. Does the north star metric align with the business model?

For each finding:
  - Severity: CRITICAL / HIGH / MEDIUM / LOW
  - Which axis it affects
  - Suggested question to ask the user"
```

### Perspective 4: Consistency Checker

```
Prompt:
"You are a documentation consistency auditor.

CONTEXT:
{full context payload}

Find contradictions, redundancies, and gaps BETWEEN axes.

Answer:
1. Does engineering/ contradict product/? (e.g., product says mobile-first but no mobile stack in engineering)
2. Does business/ align with product/? (e.g., pricing model doesn't match target users)
3. Do decisions/ explain choices mentioned in other axes?
4. Are there terms used differently across axes? (naming inconsistency)
5. What's documented in one axis but missing from a related axis?

For each finding:
  - Severity: CRITICAL / HIGH / MEDIUM / LOW
  - Which axes are affected
  - What should be aligned"
```

## Step 2: Consolidate Findings

After ALL 4 subagents complete:

1. Aggregate all findings
2. Deduplicate (same finding from multiple perspectives = higher confidence)
3. **Severity conflict resolution** — when 2+ perspectives flag the same finding at different severities:
   - Use the HIGHEST severity assigned by any perspective
   - Exception: if 3+ perspectives agree on a lower severity, use the consensus severity
   - Example: 1 says CRITICAL + 3 say HIGH → use HIGH (consensus wins)
   - Example: 1 says CRITICAL + 1 says HIGH → use CRITICAL (highest wins on tie)
4. Sort by severity: CRITICAL -> HIGH -> MEDIUM -> LOW
5. For findings flagged by 2+ perspectives: mark as **CONSENSUS**

## Step 3: Mechanical Verdict

```
All findings are LOW/MEDIUM              -> COHERENT
Any CRITICAL                             -> NEEDS ATTENTION
No CRITICAL but > 3 HIGH                 -> REVIEW RECOMMENDED
No CRITICAL, <= 3 HIGH                   -> COHERENT WITH NOTES
```

## Step 4: Present to User

For each CRITICAL and HIGH finding:
- State the finding clearly
- Ask the user to clarify or confirm
- If the user provides new info -> note it for update

After presenting:

```
"Want me to update the context docs with what we just discussed?
  -> Yes: Run update action for affected axes
  -> Not now: Skip"
```

## Output

Follow the output template in `references/templates/outputs/analyze-output.md`.

## Rules

1. ALWAYS launch all 4 perspectives in parallel — never sequential
2. NEVER resolve contradictions yourself — present them to the user
3. ALWAYS include the affected axis and a suggested action per finding
4. Findings from 2+ perspectives = CONSENSUS = higher priority
5. Don't overwhelm: present top 5-7 findings first, ask if user wants more
6. CRITICAL findings block: suggest addressing them before continuing to build
