---
name: user-story
description: "Use this skill whenever the user wants to create, generate, write, or refine user stories — whether for a new feature, an epic breakdown, a sprint planning session, or improving existing stories. Trigger on phrases like 'write user stories', 'create stories for X', 'break this epic into stories', 'improve these user stories', 'add acceptance criteria', 'I need user stories for...', or any mention of user story creation or refinement in an Agile/Scrum context. Also trigger when the user asks about story splitting, acceptance criteria writing, or INVEST validation."
---

# User Story Crafting

Help teams create well-structured, INVEST-compliant user stories with acceptance criteria through collaborative dialogue — modeled after brainstorming-style discovery.

A user story is a short, plain-language description of a feature written from the end user's perspective. It's a placeholder for a conversation, not a final spec. This skill ensures stories are user-focused, testable, and ready for sprint planning.

<HARD-GATE>
Do NOT generate user stories until you understand the product context, target users, and the feature/problem being addressed. Even if the user says "just generate stories for X", ask at least 2-3 clarifying questions first. Shallow context produces shallow stories.
</HARD-GATE>

## Two Modes

**Create Mode** — The user wants new user stories for a feature, epic, or product area.
**Refine Mode** — The user has existing stories and wants them improved, split, or validated.

Detect which mode from the user's request. If unclear, ask.

## Checklist

You MUST create a task for each item and complete them in order:

1. **Detect mode** — Create or Refine? Set the flow accordingly.
2. **Gather context** — Understand the product, feature, problem, and constraints.
3. **Identify personas** — Who are the users? What are their goals and pain points?
4. **Ask clarifying questions** — One at a time. Understand scope, edge cases, priorities.
5. **Generate/Refine stories** — Produce stories in standard format with full package.
6. **Mockup calibration** — If stories involve UI, offer low-fi mockup thinking to surface AC gaps. Progressive — skip if no UI or user declines.
7. **Validate against INVEST** — Check each story. Flag violations.
8. **Present for review** — Show stories with validation notes. Get feedback.
9. **Iterate** — Refine based on feedback. Repeat until the user is satisfied.
10. **Save output** — Write individual story files, index yaml, and mockup files (if generated). Present in conversation.

## Create Mode Flow

### Step 1: Gather Context

Before generating anything, understand:

- **Product/System**: What is being built? What does it do?
- **Feature/Epic**: What specific area are we writing stories for?
- **Problem**: What user problem does this feature solve?
- **Constraints**: Technical limitations, platform, regulations, timeline?
- **Existing work**: Any related stories, specs, or designs already written?

Check the current project for context — files, docs, recent commits, README. If you find relevant domain knowledge, use it. Don't make the user repeat what's already in the codebase.

If a `product-management/` directory exists, read `journeys/` and `features/` for upstream context (Personas, Pain Points, Feature definitions) produced by earlier BA workflow steps. This pre-fills Product, Feature, Problem, and Persona information — skip questions already answered by these files.

Also read `vision.md` if present — extract **Non-Goals**, **Constraints**, and **Assumptions** as guardrails for story generation:
- **Non-Goals** → boundary check: if a story's scope touches a Non-Goal, flag it before generating
- **Constraints** → inject into AC where relevant (e.g., Vision says "数据延迟不超过 30 秒" → related stories' AC should reflect this)
- **Assumptions** → note in stories that depend on an assumption (e.g., "assumes T-Box device is installed")

These are context enrichment — if `vision.md` doesn't exist, gather constraints and boundaries through clarifying questions instead.

### Step 2: Identify Personas

Every story needs a clear "who". The actor can be a human user OR a non-human entity — both are valid.

**Human personas** — end users, admins, operators:
- Who are the primary users of this feature?
- Are there secondary users (admins, support staff)?
- What are their goals and pain points?

**Non-human actors** — systems, services, devices, scheduled jobs:
- In architectures where components interact autonomously, a system itself is a valid actor: "As the notification service, I need to send alerts when thresholds are exceeded, so that operators respond before incidents escalate."
- Use non-human actors when the story describes inter-system behavior, data ingestion, event processing, or any capability where no human is directly operating at that moment.
- Format: "As the {system/device/service name}, I need to {action}, so that {outcome}."

If the user has existing persona documents (e.g., `product-management/personas/`), read them. Otherwise, help define lightweight personas collaboratively.

**Review role personas** — if persona files include review roles (type: 审查角色, e.g., security auditors, reliability engineers), also generate non-functional stories from their perspective:
- "As a security auditor, I need all data access events to be logged with user identity and timestamp, so that I can verify compliance in quarterly audits."
- These stories capture quality/compliance requirements that functional stories miss.
- If no review role personas exist, ask: "有没有安全、合规、可靠性方面的要求需要考虑？" — one question, not blocking. If the user says no, move on.

### Step 3: Clarifying Questions

Ask questions **one at a time** — don't overwhelm. Ask naturally, not as structured option tables. When in doubt about scope, default to the more comprehensive approach and let the user trim.

Focus on:
- Scope boundaries (what's in, what's explicitly out?)
- Priority (MVP vs nice-to-have?)
- Edge cases (what happens when things go wrong?)
- Dependencies (what does this feature need from other systems?)
- Success criteria (how will the user know this works?)

Stop asking when you have enough context to write meaningful stories. Usually 3-5 questions suffice. Don't interrogate.

### Step 4: Generate Stories

Use the standard format — every story follows the **3 C's**:

- **Card**: The written story (brief, focused)
- **Conversation**: The discussion it triggers (your clarifying questions already started this)
- **Confirmation**: The acceptance criteria (see below)

**Story format:**
```
### US-{number}: {Short descriptive title}

**Source**: {F-ID Feature Name} → {J-ID Phase N Pain Point #M} (if upstream Journey/Feature files exist, otherwise omit)

**As a** {persona/user type},
**I want to** {goal/action},
**So that** {benefit/value}.

#### Acceptance Criteria

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

#### INVEST Check
| Criterion | Status | Notes |
|-----------|--------|-------|
| Independent | {pass/flag} | {brief note} |
| Negotiable | {pass/flag} | {brief note} |
| Valuable | {pass/flag} | {brief note} |
| Estimable | {pass/flag} | {brief note} |
| Small | {pass/flag} | {brief note} |
| Testable | {pass/flag} | {brief note} |

#### Sizing Hint
{T-shirt size: XS/S/M/L/XL with brief rationale}
{Priority suggestion: if upstream Pain Point has Severity/Criticality, note it — e.g., "Fatal severity pain point → P0 candidate"}
```

**Guidelines for writing stories:**

- One feature/need per story. If a story has "and" in the goal, it probably needs splitting.
- Write from the user's perspective, not the developer's. "As a developer, I want to refactor the auth module" is NOT a user story.
- The "so that" clause must express real user/business value, not restate the "I want".
- Acceptance criteria should cover both happy path AND error/edge cases.
- Use Given/When/Then for scenario-based criteria. Use a checklist for simple rule-based criteria.
- Keep stories small enough to complete in a single sprint.
- When the Source Pain Point has upstream Severity/Criticality data (from journey-designer), use it to inform priority: Fatal/High → P0 candidate, Important/Medium → P1, Minor/Low → P2. This is a suggestion, not a mandate — the user decides final priority.

### Step 4.5: Mockup Calibration (UI Features Only)

After generating stories, if any involve a user-facing interface, run the mockup calibration protocol in `references/mockup-calibration.md`. When context already indicates UI focus (e.g., user stated stories are about UI interaction), proceed directly — don't ask permission to start, just begin Level 2. The user can opt out at any point.

**Mockup saving:** If the mockup discussion produced substantive content, save results per the Output section in `references/mockup-calibration.md` — Visual Companion wireframes to `product-management/mockups/{feature-name}-wireframe.html`, text layouts to `product-management/mockups/{feature-name}-layout.md`. Skip saving for trivial one-liner descriptions.

### Step 5: Validate Against INVEST

After generating, explicitly check each story:

- **Independent**: Can this be developed and delivered without depending on another story in this batch?
- **Negotiable**: Is the solution open to discussion, or is it over-specified?
- **Valuable**: Does it deliver clear value to a user or the business?
- **Estimable**: Can the team reasonably estimate the effort?
- **Small**: Can it be completed in one sprint? If not, suggest how to split it.
- **Testable**: Can you write a test for every acceptance criterion?

Flag any violations and suggest fixes. If a story is too large, offer 2-3 splitting strategies.

### Step 6: Present and Iterate

Present all stories in conversation. Then ask:

> "Here are {N} user stories for {feature}. Each includes acceptance criteria, INVEST validation, and a sizing hint. Take a look — anything you'd like to adjust, split further, or add?"

Iterate based on feedback. Common refinements:
- Splitting large stories
- Adding missed edge cases to acceptance criteria
- Adjusting personas
- Re-prioritizing scope (MVP vs later)

## Refine Mode Flow

When the user provides existing stories to improve:

1. **Read the existing stories** — from file or conversation
2. **Evaluate quality** — check against INVEST, acceptance criteria completeness, persona clarity, value articulation
3. **Present findings** — "Here's what I found: Story 3 has no acceptance criteria. Story 5 combines two features (violates Independent). Story 1's 'so that' restates the 'I want'."
4. **Propose improvements** — rewrite weak stories, add missing criteria, suggest splits
5. **Iterate** — same review loop as Create mode

## Story Splitting Strategies

When a story is too large, apply one of these patterns:

| Strategy | When to use | Example |
|----------|-------------|---------|
| **By workflow step** | Story covers a multi-step process | "Checkout" -> "Add to cart", "Enter shipping", "Process payment" |
| **By business rule** | Story has complex conditional logic | "Apply discount" -> "Apply percentage discount", "Apply fixed discount", "Apply bulk discount" |
| **By user role** | Different users have different needs | "Manage users" -> "Admin creates user", "User updates profile" |
| **By data variation** | Story handles multiple data types | "Import data" -> "Import CSV", "Import Excel", "Import API" |
| **By happy/unhappy path** | Error handling is substantial | "Login" -> "Successful login", "Failed login with retry", "Account lockout" |
| **By CRUD** | Story covers full lifecycle | "Manage items" -> "Create item", "View item", "Edit item", "Delete item" |

## Output File Format

**Ownership rule:** This skill writes ONLY to `user-stories/` and `mockups/`. It does not modify files produced by upstream skills (`vision.md`, `personas/`, `capabilities/`, `journeys/`, `features/`).

See `references/output-format.md` for directory structure, individual story file format, index yaml schema, and mockup file rules.

## Key Principles

- **One question at a time** — don't overwhelm the user
- **User value first** — every story must articulate why it matters
- **Acceptance criteria are non-negotiable** — a story without criteria is a wish, not a story
- **INVEST is your quality bar** — flag violations, don't ignore them
- **Split aggressively** — smaller stories are almost always better
- **Context over templates** — adapt the format to the team's needs, don't be rigid about exact wording
- **AI generates, humans validate** — always present stories for review, never assume they're final
