# Story Template

```markdown
# [{US-ID}] {Short descriptive title}

## Story

**Source**: {F-ID Feature Name} → {J-ID Phase N Pain Point #M} (omit if no upstream Journey/Feature files)

**As a** {persona/user type},
**I want to** {goal/action},
**So that** {benefit/value}.

## Acceptance Criteria

**Scenario: {Happy path description}**
- **Given** {precondition}
- **And** {additional precondition, if needed}
- **When** {action}
- **Then** {expected result}
- **And** {additional assertion, if needed}

**Scenario: {Edge case / error path}**
- **Given** {precondition}
- **When** {action}
- **Then** {expected result}

## INVEST Check

| Criterion | Status | Notes |
|-----------|--------|-------|
| Independent | {pass/flag} | {brief note} |
| Negotiable | {pass/flag} | {brief note} |
| Valuable | {pass/flag} | {brief note} |
| Estimable | {pass/flag} | {brief note} |
| Small | {pass/flag} | {brief note} |
| Testable | {pass/flag} | {brief note} |

## Sizing Hint

{T-shirt size: XS/S/M/L/XL with brief rationale}

---

*Created: [YYYY-MM-DD]*
*Updated: [YYYY-MM-DD]*
```

## Fields

| Field | Description | When Filled |
|-------|-------------|-------------|
| Source | Traceability chain: Feature → Journey Pain Point | Step 4 |
| Story (As a / I want / So that) | User-perspective goal and value | Step 4 |
| Acceptance Criteria | Given/When/Then scenarios covering happy + edge paths | Step 4, updated by Step 4.5 (mockup calibration) |
| INVEST Check | Quality validation against 6 criteria | Step 5 |
| Sizing Hint | T-shirt estimate with rationale | Step 4 |

## Notes

- **One story per file.** If a story needs splitting, create new files — don't add multiple stories to one file.
- **Source traceability is recommended.** When upstream Journey/Feature files exist, every story should link back to a Feature and its originating Pain Point. When no upstream context exists, omit the Source field.
- **Acceptance Criteria may grow.** Step 4.5 (mockup calibration) often discovers missing UI state scenarios (loading, empty, error, overflow, partial, permission). These get appended as new Scenario blocks.
