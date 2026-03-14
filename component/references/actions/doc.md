# Doc Action

> **Agent:** Load this file when `doc` triggers. Also load `references/templates/doc.md` for the output template.

Generate component documentation as a co-located `doc.md` file. Reads from spec (if exists) or from component code directly.

---

## Input Detection

```
Input is a file path (contains / or .)?
  YES → Read component code directly
  NO  → Search for component by name
    Found in codebase?
      YES → Read component code
      NO  → Check ds/specs/active/ for spec
        Found?
          YES → Generate doc from spec (pre-implementation)
          NO  → Error: "Component not found. Run /component spec first."
```

## Step 0: Gather Context

1. **From spec** (if exists in `ds/specs/active/`):
   - Component tree, API draft, edge cases, decisions
   - Figma-derived variants and props

2. **From code** (if component exists):
   - Read all component files in the directory
   - Extract: props interface, slots, events, expose
   - Detect: variants, states, composition pattern

3. **From Figma** (if link provided or stored in spec):
   - Design intent, visual variants, interaction states

4. **From codebase**:
   - Similar components and their docs (pattern matching)
   - How existing `doc.md` files are structured (if any exist)

## Step 1: Generate Documentation

Follow the template in `references/templates/doc.md` exactly.

Generate in this order:
1. **One-line description** — what this component does
2. **When to use** — use cases, contexts where this component is appropriate
3. **When NOT to use** — anti-patterns, suggest alternatives
4. **How to use** — basic example, minimal working code
5. **API** — props (type, default, description), slots, events, expose
6. **Examples** — variants, compositions, common patterns
7. **Do / Don't** — concrete usage rules with code
8. **Accessibility** — keyboard nav, screen reader, ARIA requirements

## Step 2: Write File

```
Component directory exists?
  YES → Write doc.md in component directory
        ComponentName/
        ├── ComponentName.{ext}
        ├── doc.md              ← HERE
        └── ...
  NO  → Write alongside component file
        components/
        ├── ComponentName.{ext}
        ├── doc.md              ← HERE (same directory)
        └── ...
```

## Step 3: Existing Doc Check

```
doc.md already exists in target location?
  YES → Ask user: "doc.md already exists. Overwrite or merge?"
    Overwrite → Replace entirely
    Merge → Read existing, preserve custom sections, update generated sections
  NO → Write new file
```

## Output

Follow the output template in `references/templates/doc-output.md`.

## Rules

- **When to use > How to use**: Context before code. The developer must know WHEN before HOW.
- All code examples must use the project's actual framework syntax (detect from codebase)
- Props table must match the actual component interface (read the code, don't guess)
- Do/Don't must include concrete code examples, not abstract advice
- If generating from spec (pre-implementation), mark examples as "DRAFT — update after implementation"
- Never include internal implementation details — doc is for consumers of the component
