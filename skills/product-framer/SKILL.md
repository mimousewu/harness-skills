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
Step 1.5: Persona Confirmation
Step 2:   Vision Draft → Confirm
Step 3:   Collaborative Capability Decomposition
Step 4:   Coverage Check + Worksheet
Step 5:   Output + Save Files
```

### Step 0: Context Intake

**Purpose:** Determine whether the user starts from scratch or has existing materials.

Ask:

> "在我们开始之前，你手上有没有已经积累的材料？比如 Feature 列表、User Story/Backlog、竞品分析、PRD、技术架构图、用户反馈记录？有就分享给我，我先读一遍再开始。没有的话我们从头聊。"

**If materials provided (Bottom-Up / Reverse Mode):**

1. Read all provided materials
2. Extract: user roles, business problems, capabilities implied, value propositions
3. Display your understanding as a structured summary
4. Ask: "这是我从材料中提炼的理解，哪里需要修正？"
5. Use extracted information as pre-fill for subsequent steps

**If no materials (Top-Down / Forward Mode):** Proceed to Step 1.

### Step 1: Empathize + Open Elicitation

**Purpose:** Build shared understanding of the business context through open conversation.

Display state using `references/state-display-templates.md` — "Elicitation State."

Three phases:

**Phase A — Open Narrative (Empathize)**

Start with:

> "能告诉我你们目前面临的业务挑战是什么吗？是什么促使你想做这个产品/服务？"

From the user's narrative, actively listen for:
- Who are the users? (roles, relationships)
- What situations trigger their needs?
- What are they doing today? What tools?
- Where does it break down?
- What would "good" look like?

Draw from `references/empathize-question-bank.md` for natural follow-up questions — use them conversationally, not as a checklist.

**Phase B — Structured Follow-Up (Define)**

Based on gaps from Phase A, ask targeted questions to complete the picture. Use the **Dimension Coverage Gate** below to identify what's missing.

**[Calibration]**: After 2-3 key pieces of information, run a mid-conversation calibration.

**Dimension Coverage Gate — Step 1 Exit Condition**

Track these dimensions throughout the conversation. Phase B targets whichever dimensions haven't reached "sufficient" yet:

| Dimension | Sufficient | Insufficient (needs follow-up) |
|---|---|---|
| **用户角色 (Who)** | At least 1 role with concrete goal | Only vague labels ("用户"、"客户") |
| **业务问题 (Problem)** | At least 1 specific difficulty in a specific scenario | Abstract complaints ("效率低"、"不好用") |
| **当前状态 (As-Is)** | Know what they do now + what tools they use | No idea about current approach |
| **期望状态 (To-Be)** | Desired outcome can be visualized | Only negation ("不想再 XX") |
| **产品名称 (Name)** | Has an explicit name | Not mentioned |

Exit to Phase C when: all **required** dimensions (Who, Problem, As-Is, To-Be) reach "sufficient." Product name can be filled in Phase B if missed.

"Sufficient" doesn't mean "perfect" — it means: **enough to write a calibratable Vision draft.** Step 2 has its own confirmation loop; don't try to perfect everything here.

If a dimension stays insufficient after 2 targeted questions, mark it "待定" and note it explicitly in the state display. Don't block the flow.

If dimensions contradict each other (e.g., stated problem doesn't match described workflow), trigger a calibration: "你刚才说 X，但前面提到 Y，这两个怎么对应？"

**Phase C — Synthesis Confirmation**

Before synthesizing, flush the Parking Lot — route each parked item to its proper field or discard it:

```
Parking Lot 清理：
  · [项目 1] → 归入 Vision.Constraints
  · [项目 2] → 归入 Vision.Non-Goals
  · [项目 3] → 留作 Capability 阶段参考
  · [项目 4] → 判定为超出范围，丢弃

  确认处理方式？
```

Then synthesize:

> "基于我们的对话，我理解的整体情况是这样的：... 哪里需要修正？"

### Step 1.5: Persona Confirmation

**Purpose:** Capture personas discovered during Empathize before they get lost.

From Step 1, extract personas — both **user roles** (functional needs) and **review roles** (non-functional: security auditors, reliability engineers, etc.).

For each persona, generate a lightweight card containing:
- Role name and type (用户角色 / 审查角色)
- Goals
- Daily workflow
- Current tools/systems
- Pain points
- Drivers (what motivates decisions)
- Typical quotes (if surfaced)

Present all personas:

> "我从对话中识别出这些角色：... 还有其他关键角色吗？信息哪里需要修正？"

After confirmation, save to `personas/P{n}-{name}.md` using `templates/persona-template.md`.

### Step 2: Vision Draft → Confirm

**Purpose:** Synthesize all elicited information into a structured Vision document.

Display vision draft using `references/state-display-templates.md` — "Vision Draft State."

The Vision includes (see `templates/vision-template.md` for full format):
- **Personas** — index table referencing persona files
- **Business Problems** — pain points surfaced in Empathize
- **Core Capabilities** — high-level capability statement
- **Core Values** — value propositions
- **Differentiation** — what makes this product unique
- **Product Type** — delivery form
- **Product Strategy** — service layer strategy (if applicable)
- **Success Metrics** — measurable targets
- **Non-Goals** — boundary exclusions
- **Constraints** — technical/business constraints
- **Assumptions** — preconditions assumed true

**Non-Goals are critical.** Always ask:

> "这个产品**不做**什么？有没有什么容易被误认为在范围内、但其实不应该做的？"

Ask: "以上 Vision 内容是否正确？直接说'确认'或告诉我哪里需要修改。"

### Step 3: Collaborative Capability Decomposition

**Purpose:** Decompose Vision into Capabilities through collaborative discussion, not one-way generation.

**3a — Present Decomposition Dimensions**

Offer 2-3 decomposition approaches based on the product context. See `references/decomposition-patterns.md` for four standard dimensions.

> "基于 Vision，我看到几种可能的拆法：
>
> **按用户角色拆**：[角色A] 关心 X，[角色B] 关心 Y
> **按用户目标拆**：[目标1]、[目标2]、[目标3]
>
> 你觉得哪种更符合业务逻辑？或者有其他维度？"

**3b — User Selects Dimension**

User chooses or proposes a decomposition approach.

**3c — Generate Capability Candidates**

Each candidate includes:
- C-ID and name
- **User Goal** — a concrete statement of what the user achieves (not abstract "description")
- **Service Personas** — P-IDs
- **Scope**: Includes / Excludes

Present and confirm.

**3d — Conceptual Scenes (~S)**

For each Capability, identify 2-3 conceptual scenes:

| ID | Scene Name | Trigger | Key Activities |
|---|---|---|---|
| ~S1 | [name] | [trigger] | [2-3 activities] |

> `~S` prefix = conceptual. Formalized in `journey-designer`.

If you can't think of 2-3 scenarios for a Capability, it's either too abstract (split) or too narrow (merge). Conceptual Scenes validate Capability boundaries.

**3e — Feature Candidates (~F)**

For each Capability, derive feature candidates from scope and ~S:

| ID | Feature Name | One-Line Description | Source |
|---|---|---|---|
| ~F1 | [name] | [description] | [Scope / ~S derivation] |

> `~F` prefix = candidate. Formalized in `journey-designer`.

**3f — Overall Confirmation**

Present the complete Capability set with ~S and ~F. Confirm.

### Step 4: Coverage Check + Worksheet

**Purpose:** Validate that Capabilities fully cover Vision elements.

Display worksheet using `references/state-display-templates.md` — "Worksheet State."

Coverage checks:
- Every Persona → covered by at least one Capability
- Every Business Problem → addressed by at least one Capability
- Every Core Value → delivered by at least one Capability
- Differentiation → reflected in Capabilities
- No overlapping scope between Capabilities (MECE)
- Each Capability independently deliverable

For each Capability, confirm:
- Priority (P0/P1/P2) with justification
- Dependencies (which Capabilities must exist first)

Ask: "Coverage Check 是否通过？直接说'确认'或告诉我缺什么。"

### Step 5: Output + Save Files

**Purpose:** Generate and save all output files.

1. `product-management/vision.md` — Vision with Persona index + Capability reverse index
2. `product-management/personas/P{n}-{name}.md` — update with Capability references (`## 关联 Capabilities`)
3. `product-management/capabilities.yaml` — **lightweight index only**: ID, name, priority, status, file path, conceptual_scenes count, feature_candidates count
4. `product-management/capabilities/C{x}-{name}.md` — with ~S and ~F sections

Display output summary using `references/state-display-templates.md` — "Output State."

Ask: "Planning 完成！是否需要继续 Journey 设计？（下一步使用 journey-designer skill）"

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

- `references/state-display-templates.md` — ASCII state panels for all workflow steps
- `references/decomposition-patterns.md` — Capability decomposition dimensions and guidelines
- `references/empathize-question-bank.md` — Categorized open questions for Empathize phase
- `references/worksheet-examples.md` — Complete examples with full derivation
- `templates/vision-template.md` — Vision document template
- `templates/capability-template.md` — Capability file template
- `templates/persona-template.md` — Persona file template
