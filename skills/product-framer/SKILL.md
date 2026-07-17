---
name: product-framer
description: Frame product Vision, Personas, and Capabilities through Design Thinking elicitation. ALWAYS use this skill when user wants to plan a new product, create a product roadmap, or define what to build. Also use when decomposing business context into Vision + Capability modules, or when user has feature lists/user stories but lacks Vision/Capability structure (bottom-up/reverse mode). Make sure to invoke this skill even if user doesn't explicitly say "skill" — if they mention product planning, "what should we build", business analysis, product framing, capability definition, or roadmap creation, use this skill. Triggers: "plan product", "create vision", "product roadmap", "frame product", "decompose capabilities", "reverse mode", "I have features but no vision", "define what to build", "product framing", "capability modules", "strategy to capabilities".
---

# Product Framer

Frame a product's Vision, Personas, and Capabilities through structured elicitation. This skill guides a Design Thinking-informed conversation that progressively builds shared understanding — from open narrative to structured output.

It covers the first three layers of product analysis: **Vision → Personas → Capabilities** (with conceptual Scenes and Feature Candidates). Downstream skills (`journey-designer`, `user-story`) consume these outputs.

## Output Specification

**Directory structure:**

```
product-management/
  vision.md                   # Product Vision
  personas/                   # Persona detail files
    P1-{name}.md
    P2-{name}.md
  capabilities.yaml           # Lightweight index (routing only)
  capabilities/               # Capability detail files
    C1-{name}.md
    C2-{name}.md
```

**File naming:** Lowercase kebab-case English slugs (e.g., `C1-vehicle-monitoring.md`, `P1-fleet-manager.md`).

## Interaction Rules

### 1. Progressive Elicitation, Not Form-Filling

The core interaction model is a structured conversation, not a questionnaire.

A senior BA doesn't ask "who is the target user?" — they say "tell me about the business situation that led you here" and *listen* for users, problems, and value.

Open questions surface implicit knowledge — the things users know but wouldn't volunteer in a form field. Closed questions get "correct answers"; open questions get "real situations."

### 2. One Question Per Turn

Ask only ONE question per turn. Wait for the user's response before asking the next. This respects thinking pace and allows natural follow-up.

### 3. Visual State Display

Before every question, display a state panel showing:
- Current phase and step
- What's been confirmed
- What's still unknown
- What you're currently working on

See `references/state-display-templates.md` for formats.

### 4. Mid-Conversation Calibration

After every 2-3 key pieces of information, pause to verify your understanding:

```
"到目前为止，我理解的情况是：
- 你们的 [用户] 在 [场景] 下面临 [问题]
- 现在的应对方式是 [现状]
- 你们希望通过 [能力] 来解决

这个理解对吗？哪里需要修正？"
```

This is hypothesis testing — it's how understanding goes from 60% to 90%. Don't wait until the final draft to calibrate; validate incrementally.

### 6. Answer Drift Handling

Users rarely answer exactly what was asked. This is normal — drift often carries valuable information. Handle by type:

**Type A — Off-topic but valuable** (user goes on a tangent with useful info):
Acknowledge, extract, file for later, then gently steer back.
> "了解，[提取的信息] — 这个我记下了，后面讨论 [对应维度] 时会用到。回到 [当前话题]：[追问]"

**Type B — Abstraction mismatch** (user answers with a solution instead of a problem, or vice versa):
Accept the solution, reverse-engineer the problem behind it.
> "实时数据推送 — 这是一种解决方案。能帮我理解，用户现在因为缺少实时数据，具体遇到了什么困难？"

**Type C — Information dump** (user pours out too much at once):
Extract the main thread, confirm, then drill into one dimension at a time.
> "信息量很大，我先梳理确保没遗漏：1) [主线A] 2) [主线B] 3) [主线C]。对吗？我想先从 [最关键的] 展开。"

**Type D — Evasion or uncertainty** (user doesn't know or avoids the question):
Don't insist. Reframe from a different angle — negation is often easier than affirmation.
> "没关系，这个我们后面再定义。换个角度——现有方案最让客户不满意的地方是什么？"

If a dimension stays blank after 2 attempts, mark it "待定" and move on. Don't block the flow.

When drift produces information that doesn't belong to the current dimension, place it in the **Parking Lot** — a visible section in the state display that holds items awaiting classification. Each parked item should note its suggested destination (e.g., "→ Constraints", "→ ~F candidate"). See `references/state-display-templates.md` — "Parking Lot Block."

Core principle: **never dismiss what the user says**. Everything they say — even tangents — reveals what they care about.

### 5. Confirm → Revise → Continue

Every phase ends with a structured draft + user confirmation:

1. Collect minimum necessary information
2. Generate structured draft
3. User confirms or corrects
4. Update state
5. Move to next phase

## Workflow

```
Step 0:   Context Intake
Step 1:   Empathize + Open Elicitation
Step 1.5: Persona Confirmation + LVT Goal Derivation
Step 2:   Vision Draft → Confirm
Step 3:   Collaborative Capability Decomposition
Step 4:   Coverage Check + Worksheet
Step 5:   Output + Save Files
```

**详细步骤指令见 `references/workflow-details.md`。** 该文件包含每一步的具体提问、Gate 条件和操作指南。

## Guiding Principles

### Capabilities Must Be User-Perceptible

Focus only on abilities users can directly use or experience. Infrastructure, middleware, and data pipelines are not Capabilities — they're implementation details.

**Common mistake:** "Data Processing Pipeline" is not a Capability. "Real-Time Vehicle Monitoring" is.

### Organize by User Goals, Not Technical Layers

- ❌ Technical: data collection → processing → storage → display
- ✅ User goals: "know where vehicles are", "detect dangerous driving"

### Problems Come from Behaviors, Not Imagination

- ❌ "I think users might have this problem"
- ✅ "Users encountered this obstacle while doing X"

### Ask "What Specifically?" and "Why?"

- "complex" → Process complexity? Unclear docs? Difficult concepts?
- "can't do" → Don't know they can? Or know but can't?
- "data insufficient" → What data? Coverage? Granularity? Timeliness?

### Distinguish Product vs Customer System Responsibilities

Clearly separate:
- What **users** do (actions and decisions)
- What **customer's existing systems** do (current tools)
- What **our product** provides (new capabilities)

Don't claim customer system responsibilities as our product capabilities.

### MECE for Capability Boundaries

- Mutually Exclusive: no feature overlap between Capabilities
- Collectively Exhaustive: all user goals covered
- For ambiguous boundaries: think from user perspective — "Where would users use this?"

### Dual Persona Perspective

Personas include both:
- **User roles** (functional needs): drivers, fleet managers, dispatchers
- **Review roles** (non-functional needs): security auditors, reliability engineers

Review roles define what "good work" looks like from a quality/compliance angle — constraints that downstream Agent implementation must respect.

## Fast-Path Support

Users often don't start from zero. Support these entry points:

| Entry Point | Behavior |
|---|---|
| From scratch | Normal: Step 0 → Step 5 |
| Has materials (features, US) | Step 0 reads materials → Reverse Mode → pre-fills Steps 1-2 |
| Has Vision, needs Capabilities | Read existing `vision.md` → skip to Step 3 |
| Has Vision + Capabilities | Read existing files → show state → targeted editing |
| Resume from outputs | Detect `product-management/` → load state → continue from last incomplete step |

When resuming, always show current state before asking what to do next.

## Resources

- `references/workflow-details.md` — 完整的 Steps 0-5 操作指令（含 Gate 条件和具体提问）
- `references/state-display-templates.md` — ASCII state panels for all workflow steps
- `references/decomposition-patterns.md` — Capability decomposition dimensions and guidelines
- `references/empathize-question-bank.md` — Categorized open questions for Empathize phase
- `references/worksheet-examples.md` — Complete examples with full derivation
- `templates/vision-template.md` — Vision document template
- `templates/capability-template.md` — Capability file template
- `templates/persona-template.md` — Persona file template
