# User Story

Create well-structured, INVEST-compliant user stories with acceptance criteria through collaborative dialogue. This skill transforms upstream product analysis (Journeys, Features, Pain Points) into executable task entry points for development — whether by human teams or AI Agents.

**Scope:** Feature + Journey → User Stories (with Acceptance Criteria, INVEST validation, sizing hints). Upstream: `journey-designer` (Journeys, Features) and `product-framer` (Vision, Personas, Capabilities). Downstream: development implementation.

---

## Methodology Foundations

### Position in the Analysis Chain

User-story owns the task layer — the final analytical step before implementation begins:

| Layer | Artifact | Owner | user-story role |
|---|---|---|---|
| Vision | Vision + Personas | product-framer | Reads Persona context |
| Capability | Capability + ~S + ~F | product-framer | (indirect, via Features) |
| Journey | Journey (Phases + Pain Points) | journey-designer | Reads Pain Points as source |
| Feature | Feature definitions | journey-designer | Reads as primary input |
| **User Story** | **US + Acceptance Criteria** | **user-story** | **✅ Produces** |

When upstream artifacts exist (`product-management/` directory with journeys and features), the skill pre-fills context from those files — skipping questions already answered. When no upstream context exists (standalone mode), the skill gathers context through its own elicitation.

### Harness Engineering: Stories as Agent Task Entry Points

Under Harness Engineering, User Stories are the **exclusive task entry point for development Agents:**

> All work must begin from a Ticket (corresponding to a User Story). Agents should start development directly by reading the Ticket and relevant Skills.

This elevates two requirements:

1. **Acceptance Criteria must be executable** — an Agent reads them and knows exactly what to implement and how to verify it passes. Vague or subjective criteria ("should be fast") are useless to an Agent.
2. **Source traceability must be explicit** — `Feature → Journey Pain Point → Story` so Agents understand not just *what* to build but *why* it matters and *what problem* it solves.

### The 3 C's Framework

Every story follows Ron Jeffries' three dimensions:

| C | What it is | How the skill handles it |
|---|---|---|
| **Card** | The written story (brief, focused) | "As a / I want / So that" format |
| **Conversation** | The discussion it triggers | The skill's elicitation questions ARE the Conversation |
| **Confirmation** | How we know it's done | Scenario-based acceptance criteria |

The skill automates the Conversation phase through structured elicitation, then produces the Card and Confirmation as output. The critical insight: a story without Conversation context is shallow — which is why the Hard Gate exists.

### INVEST Criteria

Bill Wake's INVEST model is the quality bar for every story:

| Criterion | What it checks | Common violation |
|---|---|---|
| **Independent** | Can be developed/delivered without depending on other stories in the batch | Stories that share a data model or UI component |
| **Negotiable** | Solution is open to discussion, not over-specified | AC that prescribes implementation ("use React", "store in Redis") |
| **Valuable** | Delivers clear value to a user or the business | "So that" clause restates the "I want" instead of expressing value |
| **Estimable** | Team can reasonably estimate the effort | Too vague ("improve performance") or too large to estimate |
| **Small** | Completable in one sprint | "And" in the goal → probably needs splitting |
| **Testable** | Every AC can be verified with a test | Subjective criteria ("should be fast", "looks good") |

The skill runs INVEST validation on every generated story and flags violations with suggested fixes.

---

## Design Principles

### 1. Hard Gate: No Generation Without Context

The skill refuses to generate stories until it understands the product context, target users, and the feature/problem being addressed. Even if the user says "just generate stories for X", at least 2-3 clarifying questions must be asked first.

This is not politeness — it's quality control. Shallow context produces shallow stories with missing edge cases and vague acceptance criteria.

### 2. Two Modes: Create and Refine

| Mode | Trigger | Flow |
|---|---|---|
| **Create** | User wants new stories for a feature/epic | Gather context → Personas → Clarify → Generate → Validate → Iterate |
| **Refine** | User has existing stories to improve | Read stories → Evaluate quality → Present findings → Rewrite → Iterate |

The skill detects which mode from the user's request. Both modes converge at the INVEST validation and iteration steps.

### 3. Upstream Context Absorption

The skill actively scans for context before asking questions — both upstream BA artifacts and the project codebase itself:

- **Project codebase**: files, docs, recent commits, README — any domain knowledge already available
- **Personas** from `personas/P{n}.md` — goals, pain points, daily workflow
- **Feature definitions** from `features/F{n}.md` — scope, problem solved, user value
- **Pain Points** from `journeys/J{n}.md` — specific steps where friction occurs
- **Source traceability** auto-populates: `F-ID → J-ID Phase N Pain Point #M`

This eliminates redundant questioning. Don't make the user repeat what's already in the codebase or upstream artifacts.

### 4. Non-Human Actors Are Valid

In IoT/B2B architectures where components interact autonomously, a system itself is a valid story actor:

> "As the notification service, I need to send alerts when thresholds are exceeded, so that operators respond before incidents escalate."

Use non-human actors when the story describes inter-system behavior, data ingestion, event processing, or any capability where no human is directly operating. This is critical for products where device → cloud → user interaction chains define business logic.

### 5. Acceptance Criteria Are Non-Negotiable

A story without acceptance criteria is a wish, not a story. Every story must have:

- At least one **happy path** scenario
- At least one **edge case or error path** scenario
- Criteria that are **testable** — no subjective language ("should be fast", "looks good")

### 6. Split Aggressively

If a story has "and" in the goal, it probably needs splitting. The skill offers six splitting strategies depending on the story's nature:

| Strategy | Signal |
|---|---|
| By workflow step | Multi-step process in one story |
| By business rule | Complex conditional logic |
| By user role | Different users, different needs |
| By data variation | Multiple data types handled |
| By happy/unhappy path | Substantial error handling |
| By CRUD | Full lifecycle in one story |

Smaller stories are almost always better — easier to estimate, faster to deliver, clearer to test.

### 7. Context Over Templates

Adapt the story format to the team's needs. The standard format (As a / I want / So that + Gherkin AC) is a strong default, but don't be rigid about exact wording if the team has established conventions.

### 8. AI Generates, Humans Validate

Always present stories for review. Never assume they're final. The skill's role is to produce high-quality drafts that accelerate the team's conversation — not to replace human judgment on scope, priority, or business value.

---

## Key Design Decisions

### Why Mockup Calibration Exists

The skill includes an optional UI mockup step (Step 4.5) that surfaces acceptance criteria gaps through low-fi mockup thinking. This is a **calibration tool, not a design phase.**

The insight: when stories describe user-facing interfaces, textual AC often misses UI states that only become obvious when you visualize the screen. The **6-State Checklist** systematically catches these:

| UI State | What gets missed without it |
|---|---|
| **Loading** | 数据加载中显示什么？骨架屏？转圈？ |
| **Empty** | 没有数据/结果时显示什么？ |
| **Error** | 请求失败/超时时怎么提示？ |
| **Overflow** | 数据量很大时怎么处理？分页？聚合？ |
| **Partial** | 部分数据到了、部分没到怎么显示？ |
| **Permission** | 没权限时界面什么样？ |

The protocol is **progressive and non-blocking**: it self-skips for non-UI stories, offers itself only when UI stories are detected, and the user can decline at any point. When it does run, it prioritizes the most complex UI story for full analysis and batch-confirms similar patterns.

### Why Individual Files Per Story

Each story is saved as a separate file (`user-stories/US{n}-{name}.md`), with a lightweight YAML index for routing. This matches the upstream pattern:

```
product-framer:     capabilities.yaml  +  capabilities/C{x}.md
journey-designer:   journeys/J{n}.md   +  features/F{n}.md
user-story:         {feature}.yaml     +  user-stories/US{n}.md
```

Individual files enable:
- Independent lifecycle (a story can be archived, split, or moved without touching others)
- Clean `git diff` per story (reviewable in PRs)
- Agent consumption — an Agent reads one ticket file to start work

### Why the Hard Gate Is Mandatory

The temptation with LLM-powered story generation is to immediately produce output. The Hard Gate — requiring context gathering before any generation — exists because:

1. **First-draft stories from thin context have plausible-sounding but wrong AC.** They pass a casual review but fail during implementation when edge cases surface.
2. **Upstream file reading is cheap.** If `product-management/` exists, 90% of the context is already there — but only if the skill actually reads it.
3. **2-3 clarifying questions compound quality.** Each question eliminates an entire class of bad assumptions. The ROI is enormous compared to iterating on shallow stories.

### Source Traceability Design

When upstream artifacts exist, every story carries a Source field:

```
Source: F2 实时异常告警 → J1 Phase 3 Pain Point #2
```

This chain — `Story → Feature → Journey Phase → Pain Point → User Behavior Step` — is what makes Harness Engineering work: an Agent can trace from its Ticket all the way back to "why does this matter" and "what user behavior does this address."

When no upstream context exists (standalone mode), the Source field is omitted. The skill doesn't fabricate traceability.

---

## Workflow Overview

```
Create Mode:
  Step 1:   Gather Context (scan codebase + read upstream files + ask what's missing)
  Step 2:   Identify Personas (human + non-human actors)
  Step 3:   Clarifying Questions (scope, edge cases, priorities)
  Step 4:   Generate Stories (Card + AC + INVEST + Sizing)
  Step 4.5: Mockup Calibration (progressive, UI stories only)
  Step 5:   Validate Against INVEST (flag violations)
  Step 6:   Present + Iterate (review loop until satisfied, then save output)

Refine Mode:
  Step 1:   Read existing stories
  Step 2:   Evaluate quality (INVEST + AC completeness)
  Step 3:   Present findings
  Step 4:   Propose improvements (rewrite, add criteria, suggest splits)
  Step 5:   Iterate (same review loop as Create mode, then save output)
```

The skill enforces a mandatory 10-item checklist — each item must be completed in order. The checklist maps to these steps but also includes mode detection (before Step 1) and output saving (after the last step). See `SKILL.md` for the full checklist and detailed step-by-step instructions.

---

## Output Specification

```
product-management/
  user-stories/
    US1-{name}.md              # Individual story files (one per story)
    US2-{name}.md
    {feature-name}.yaml        # Lightweight index (routing only)
  mockups/                     # Optional — only if Step 4.5 produced content
    {feature-name}-layout.md   # Text mockup
    {feature-name}-wireframe.html  # Visual Companion mockup
```

File naming: lowercase kebab-case English slugs (e.g., `US1-vehicle-search.md`, `US2-alert-configuration.md`).

---

## Resources

| File | Purpose |
|---|---|
| `SKILL.md` | Complete step-by-step execution instructions |
| `references/mockup-calibration.md` | UI mockup calibration protocol + 6-State Checklist |
| `references/output-format.md` | Directory structure, file format, index yaml schema |
| `templates/story-template.md` | Individual story file template |
