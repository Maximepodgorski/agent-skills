# Page States — Loading, Empty, Error, Success

> Every screen has multiple states beyond "happy path." This principle ensures every state is explicitly designed, not discovered in production.

## The 5 States

```
                          ┌─────────┐
                    ┌────▶│ SUCCESS │ (data loaded, content displayed)
                    │     └─────────┘
┌─────────┐   ┌────┴───┐
│ LOADING │──▶│ RESULT  │
└─────────┘   └────┬───┘
                    │     ┌─────────┐
                    ├────▶│  EMPTY  │ (no data, first use, or filtered out)
                    │     └─────────┘
                    │     ┌─────────┐
                    └────▶│  ERROR  │ (fetch failed, permission denied)
                          └─────────┘

                          ┌─────────┐
                          │ PARTIAL │ (some sections loaded, others pending)
                          └─────────┘
```

| State | Trigger | User Sees | Required Action |
|-------|---------|-----------|-----------------|
| **Loading** | Data is being fetched | Skeleton / spinner / progress bar | Indicate something is happening |
| **Success** | Data loaded successfully | Full content | Display data in layout |
| **Empty** | No data exists yet | Illustration + message + CTA | Guide user to create first item |
| **Error** | Fetch/permission failed | Error message + retry action | Offer recovery path |
| **Partial** | Some data loaded, some pending | Mix of content + skeletons | Show what's ready, skeleton the rest |

## Loading State

### Skeleton vs Spinner

```
Use SKELETON when:
  - Page layout is known (dashboard, list, form)
  - Multiple content zones exist
  - Goal: perceived performance + layout stability

Use SPINNER when:
  - Full-page load (no layout known yet)
  - Single action result (button click → wait)
  - Goal: simple feedback
```

### Skeleton Rules

```
Skeleton MUST:
  - Match the exact layout of the success state
  - Use the same grid, spacing, and sizing
  - Pulse/animate to indicate loading (not static gray boxes)

Skeleton MUST NOT:
  - Change layout between loading and success (layout shift)
  - Show text placeholders that don't match actual content length
  - Flash briefly (use minimum display time: 200-300ms)
```

### Per-Zone Loading

For screens with independent data zones (dashboards):

```
┌─────────────────────────────────────┐
│  Header (loaded — static)           │
├──────────┬──────────┬───────────────┤
│  ████████ │  Card 2  │  ████████████ │  ← Zone 1 loading, Zone 2 loaded, Zone 3 loading
│  ████████ │  (ready) │  ████████████ │
├──────────┴──────────┴───────────────┤
│  ████████████████████████████████████│  ← Primary zone loading
└─────────────────────────────────────┘

Rule: Show zones as they resolve. Don't wait for all zones.
```

## Empty State

### Types

| Type | Description | Content |
|------|-------------|---------|
| **First use** | User has never created anything | Welcome message + primary CTA |
| **No results** | Search/filter returned nothing | "No results" + suggestion to adjust filters |
| **Cleared** | User deleted all items | "All done" + option to create more |

### Empty State Anatomy

```
┌─────────────────────────────────────┐
│                                     │
│         [illustration]              │  ← Optional, adds personality
│                                     │
│     Title: descriptive, not sad     │  ← "No projects yet" not "Nothing here!"
│     Body: what to do next           │  ← "Create your first project to get started"
│                                     │
│         [Primary CTA]               │  ← Always provide the next action
│                                     │
└─────────────────────────────────────┘
```

### Rules

- **Always provide a CTA** — empty state without action = dead end
- **Title = positive** — frame as opportunity, not absence
- **No results** must show what was searched/filtered + clear filters action
- **First use** should guide, not overwhelm

## Error State

### Types

| Type | Severity | Recovery |
|------|----------|----------|
| **Network error** | Recoverable | Retry button |
| **Permission denied** | Blocking | Contact admin / request access |
| **Not found (404)** | Redirect | Go back / go home |
| **Server error (500)** | Recoverable | Retry + contact support |
| **Validation error** | Inline | Fix input + resubmit |

### Error Display Rules

```
INLINE errors (field-level):
  - Show below the field, not in a toast
  - Red border on field + error message
  - Clear when user corrects the input

SECTION errors (zone failed to load):
  - Replace zone content with error + retry
  - Don't crash the entire page for one zone

PAGE errors (entire page failed):
  - Full-page error with retry + navigation
  - Preserve any entered data if possible

TOAST errors (non-blocking):
  - Transient feedback for background operations
  - Auto-dismiss after 5-8 seconds
  - Include undo if destructive
```

## Partial State

For complex screens with multiple independent data sources:

```
Priority rendering:
  1. Shell (navigation) — always render first
  2. Static content (headers, labels) — render immediately
  3. Primary data zone — load first, skeleton others
  4. Secondary data zones — load in parallel, render as ready
  5. Non-critical zones — load last, can use lazy loading
```

## Spec Integration

When the screen spec is generated, the agent MUST include a states table:

```markdown
## Page States

| State | Components Affected | Behavior |
|-------|-------------------|----------|
| Loading | {zones using skeletons} | Skeleton layout matching success state |
| Success | {all zones} | Full content displayed |
| Empty | {primary zone} | Empty illustration + CTA: "{action}" |
| Error | {zones that can fail} | Inline error + retry per zone |
| Partial | {independent zones} | Per-zone loading, render as ready |
```

**Skip-if:** Stateless screens (static content, no data fetching) can skip this section.

## Do / Don't

| Do | Don't |
|----|-------|
| Design all 5 states for every data-driven screen | Only design the happy path |
| Use skeletons matching success layout | Use generic spinners for complex screens |
| Provide recovery actions for every error | Show errors without next steps |
| Show per-zone loading for dashboards | Block entire page while one zone loads |
| Make empty states actionable | Show "No data" with no CTA |
