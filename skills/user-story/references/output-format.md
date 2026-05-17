# Output File Format

Save each story as an individual file, with a lightweight index for routing. This matches the upstream pattern used by `product-framer` (capabilities.yaml + `capabilities/`) and `journey-designer` (`journeys/` + `features/`).

## Directory Structure

```
product-management/
  user-stories/
    US1-{name}.md              # Individual story files
    US2-{name}.md
    US3-{name}.md
    {feature-name}.yaml        # Lightweight index (routing only)
  mockups/                     # Mockup artifacts (if Step 4.5 produced content)
    {feature-name}-layout.md   # Text path mockup
    {feature-name}-wireframe.html  # Visual Companion mockup
```

**File naming:** Lowercase kebab-case English slugs (e.g., `US1-vehicle-search.md`, `US2-alert-configuration.md`).

## Individual Story File

Use `templates/story-template.md` for format. Each file contains one story with its full package: card, acceptance criteria, INVEST check, sizing hint, and source traceability.

## Index File (`{feature-name}.yaml`)

Lightweight routing index — **not** a duplicate of story content:

```yaml
feature: {Feature/Epic Name}
date: {YYYY-MM-DD}
status: Draft | Reviewed | Approved
epic: {Parent epic if applicable}

personas:
  - id: P1
    name: {Persona Name}
    role: {description}

stories:
  - id: US-1
    title: {title}
    file: US1-{name}.md
    persona: {persona}
    size: {XS/S/M/L/XL}
    invest: {pass/flags}
    source: {F-ID or N/A}
  - id: US-2
    title: {title}
    file: US2-{name}.md
    persona: {persona}
    size: {XS/S/M/L/XL}
    invest: {pass/flags}
    source: {F-ID or N/A}

mockups:
  - file: ../mockups/{feature-name}-layout.md    # omit section if no mockups
    type: text
    related_stories: [US-1, US-3]

open_questions:
  - {Any unresolved questions from the brainstorming session}
```

## Mockup Files

Saved during Step 4.5 per `references/mockup-calibration.md` output rules. Only created when mockup discussion produced substantive content. Referenced from the index yaml.
