# Generate Action

> **Agent:** Load this file when `generate` triggers. Also load `references/templates/claude-md.md` for the output template.

Reads all `.context/` docs and produces a condensed CLAUDE.md (or agent-specific config file).

---

## Input Detection

```
.context/ exists with at least 1 axis?
  NO  -> "No context found. Run 'kickoff init' first."
  YES -> Continue

Existing CLAUDE.md in project root?
  YES -> Merge mode (preserve user's custom sections)
  NO  -> Create mode (generate from scratch)
```

## Step 0: Read All Context

1. Read `.context/_meta.md` -> active axes, dates, completion status
2. Read ALL axis docs in `.context/`
3. Read ALL decision records in `.context/decisions/`
4. Note any TBD markers in docs
5. **No-op check**:
   - If `last_generated` is `"never"` → skip check, always continue
   - If `last_generated` is a date → compare against each axis `Last Updated` in `_meta.md`
   - If ANY axis `Last Updated` >= `last_generated` → context has changed, continue silently
   - If ALL axis dates are strictly before `last_generated` → "CLAUDE.md is already up to date (no context changes since last generation). Regenerate anyway?"
     - User says yes → continue
     - User says no → stop

## Step 1: Detect Target Format

```
Existing CLAUDE.md?        -> Claude Code format
Existing .cursorrules?     -> Cursor format
Existing .windsurfrules?   -> Windsurf format
Nothing?                   -> Default to CLAUDE.md (ask user if unsure)
```

## Step 2: Condense Context

For each axis, extract the most impactful information for the agent:

### Priority order (what changes agent behavior most)

```
1. Engineering conventions (naming, style, patterns, commands)
   -> Agents consult this on EVERY code task
   -> Include: full conventions content

2. Stack + architecture (frameworks, structure, data flow)
   -> Agents need this for every file they create/edit
   -> Include: stack summary + architecture diagram

3. Key decisions (why choices were made)
   -> Prevents agents from suggesting rejected alternatives
   -> Include: one-line per decision

4. Features + roadmap (current state + what's next)
   -> Agents need to know what exists AND what's coming
   -> Include: feature list with status + condensed roadmap (current sprint + next milestones)

5. Product vision (problem, users, differentiation)
   -> Agents need this for naming, UX copy, prioritization
   -> Include: condensed vision (3-5 lines)

6. Design tokens + principles (if applicable)
   -> Agents need this for UI code
   -> Include: key tokens, design principles

7. Business + marketing (if applicable)
   -> Rarely needed for coding tasks
   -> Include: 2-3 lines max

8. Team (if applicable)
   -> Process info for PR structure, reviews
   -> Include: 2-3 lines max
```

### Condensation rules

- **No line limit** — include all actionable context from `.context/` docs
- Each section uses bullets, not prose
- Omit empty or fully-TBD sections
- Include TBD markers for partially-complete sections (so the agent knows what's uncertain)
- ASCII architecture diagram: include if it exists in architecture.md
- More context = better agent behavior. Never truncate useful information

## Step 3: Merge with Existing (if applicable)

```
IF existing CLAUDE.md:
  1. Parse existing content into sections
  2. Identify sections that came from a previous generate (marked with <!-- context:generated -->)
  3. Identify user's custom sections (NOT marked)
  4. Replace generated sections with new content
  5. Preserve user's custom sections untouched
  6. Show diff to user before writing

  NEVER delete user's custom sections.
  ALWAYS show what will change before writing.

IF new CLAUDE.md:
  1. Generate from template
  2. Show preview to user
  3. Write on confirmation
```

### Section markers

Generated sections include an HTML comment marker:

```markdown
<!-- context:generated -->
## Engineering
...
<!-- /context:generated -->
```

This allows future `generate` calls to identify and replace only the generated content.

**Corrupted markers:** If only one marker is found (opening without closing or vice versa), warn the user: "Found a corrupted context marker in CLAUDE.md. Cannot safely merge. Please fix the markers manually or delete them — I'll regenerate the full generated block." Do NOT attempt to guess where the missing marker should be.

## Step 4: Write

1. Write CLAUDE.md (or .cursorrules, etc.)
2. Update `.context/_meta.md` with generation date
3. Show summary to user

## Output

Follow the output template in `references/templates/outputs/generate-output.md`.

## Rules

1. Include ALL actionable context — no artificial line limit. More context = better agent behavior
2. NEVER delete user's custom sections in existing CLAUDE.md
3. ALWAYS show diff/preview before writing
4. ALWAYS mark generated sections with <!-- context:generated --> markers
5. Priority: engineering > stack > decisions > features + roadmap > product > design > business > team
6. Omit empty sections — don't include headers with no content
7. TBD items: include as "[TBD]" so the agent knows what's uncertain
8. If context is thin (< 3 axes), warn: "Context is minimal. Consider running 'kickoff init' for a full kickoff."
9. ALWAYS include a Deep Context table at the end mapping each CLAUDE.md section to its `.context/` source file with situational triggers — agents use this to dig deeper when needed
