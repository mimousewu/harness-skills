---
name: journey-designer
description: Map user journeys from Capabilities and derive Features through structured elicitation. ALWAYS use this skill when user wants to create user journeys, map user experience flows, identify pain points from user behaviors, or derive features from journey analysis. Also use when breaking down Capabilities into end-to-end user paths, or when the user mentions CUJ (Critical User Journey), journey mapping, experience mapping, or pain-point-to-feature derivation. Triggers: "user journey", "journey mapping", "map the experience", "CUJ", "critical user journey", "pain points to features", "derive features from journey", "end-to-end user flow", "break down capability into journeys", "journey designer".
---

# Journey Designer

Map a Capability's conceptual scenes into end-to-end user journeys and derive Features from journey pain points. This skill takes product-framer's output (Capabilities with `~S` and `~F`) and produces structured Journey maps plus Feature definitions through collaborative dialogue.

A Journey describes how a user currently experiences a capability — the steps they take, the tools they touch, where they get stuck, and what emotions arise. Features are derived from journey pain points, not imagined.

This skill covers the middle layer of product analysis: **Capability → Journeys → Features**. Upstream: `product-framer` (Vision, Personas, Capabilities). Downstream: `user-story` (User Stories with acceptance criteria).

## Output Specification

**Directory structure (append to existing `product-management/`):**

```
product-management/
  journeys/
    J1-{name}.md              # Journey detail files
    J2-{name}.md
  features/
    F1-{name}.md              # Feature detail files
    F2-{name}.md
```

**File naming:** Lowercase kebab-case English slugs (e.g., `J1-daily-monitoring.md`, `F1-vehicle-search.md`).

**Ownership rule:** This skill writes ONLY to `journeys/` and `features/`. It does not directly modify files produced by product-framer (`vision.md`, `personas/`, `capabilities/`, `capabilities.yaml`). When Journey analysis reveals upstream issues (e.g., Capability scope seems wrong, Persona missing information), offer the user the choice to pause and switch to product-framer to make corrections — see "Upstream Issue Escalation" in Guiding Principles.

## Interaction Rules

Inherit all interaction rules from product-framer. The following are carried forward without change:

### 1. Progressive Elicitation, Not Form-Filling

A senior BA doesn't say "list your pain points" — they walk through the journey with the user and *discover* pain points in the steps. Open questions surface implicit knowledge.

### 2. One Question Per Turn

Ask only ONE question per turn. Wait for the response before proceeding.

### 3. Visual State Display

Before every question, display a state panel. See `references/state-display-templates.md`.

### 4. Mid-Conversation Calibration

After enriching 2-3 phases, pause to verify understanding:

```
"到目前为止，我理解的旅程是这样的：
- [角色] 在 [触发条件] 下开始
- 先做 [Phase 1]，主要卡在 [痛点]
- 然后进入 [Phase 2]，这里 [情况描述]

这个理解对吗？哪里需要修正？"
```

### 5. Confirm → Revise → Continue

Every step ends with a structured draft + user confirmation before proceeding.

### 6. Answer Drift Handling

Users rarely answer exactly what was asked. Handle by type:

- **Off-topic but valuable**: Acknowledge, extract, file for later, steer back. "了解，这个我记下了，后面会用到。回到 [当前话题]：[追问]"
- **Abstraction mismatch** (solution instead of problem): Accept the solution, reverse-engineer the problem. "这是一种解决方案。用户现在因为缺少这个，具体遇到了什么困难？"
- **Information dump**: Extract main threads, confirm, then drill into one at a time. "信息量很大，我先梳理：1) [A] 2) [B] 3) [C]。对吗？先从 [最关键的] 展开。"
- **Evasion or uncertainty**: Don't insist. Reframe from a different angle. "没关系，换个角度——这一步最让人不满意的地方是什么？" After 2 attempts, mark "待定" and move on.

## Workflow

```
Step 0:   Load Context + Journey Scoping
Step 1:   Phase-by-Phase Enrichment
Step 2:   Journey Assembly + Diagram Generation
Step 3:   Feature Derivation + Traceability Diagram
Step 4:   Gap Analysis + Priority Diagram + Output
```

### Step 0: Load Context + Journey Scoping

**Purpose:** Read upstream outputs and determine which `~S` entries form a Journey.

**0a — Load Files (silent, no user interaction)**

Read from `product-management/`:
- Target Capability file (`capabilities/C{x}-{name}.md`) — extract `~S`, `~F`, Persona references
- Referenced Persona files (`personas/P{n}-{name}.md`) — extract daily workflow, current tools, pain points, drivers

Display state using `references/state-display-templates.md` — "Step 0 State."

**0b — Journey Scoping**

Present the `~S` entries and propose how they group into Journeys. Apply merge rules:

| Rule | Condition | Action |
|---|---|---|
| **Same Persona + sequential trigger** | ~S1 naturally flows into ~S2 | Merge into one Journey |
| **Same Persona + causal link** | ~S1 outcome triggers ~S2 | Merge into one Journey |
| **Cross-Capability + same Persona + end-to-end chain** | ~S from C1 leads into ~S from C2 | Merge (Journey spans multiple Capabilities) |
| **Different Personas** | ~S1 is for role A, ~S2 is for role B | Separate Journeys |

The test: does the user naturally transition from ~S1 to ~S2 without a break? If yes → merge. If no → separate.

Present your proposal:

> "基于 C1 的概念化场景，我建议这样组织 Journey：
>
> **J1: [名称]** — 串联 ~S1 + ~S2（同一管理员，自然连续流程）
> **J2: [名称]** — ~S3 独立（不同触发，不同目标）
>
> 这样分合理吗？"

After confirmation, define each Journey's skeleton: Persona + Trigger + Phase names (3-5 phases).

### Step 1: Phase-by-Phase Enrichment

**Purpose:** Expand each Phase from a one-line name into a detailed behavioral description.

For each Phase, follow the enrichment protocol in `references/enrichment-protocol.md`. The core process:

**1a — Agent Drafts Phase Steps**

Based on Persona's daily workflow, current tools, and the ~S activities, draft the Phase's steps:

```
Phase 1: 查看车辆状态
  1. 打开监控系统
  2. 查看车辆总览地图
  3. 筛选目标区域/车辆
  4. 查看单车详情
```

Present to user: "这是我根据 [Persona] 的日常工作推断的步骤。哪里需要修正？有遗漏吗？"

**1b — Problem Discovery**

For confirmed steps, ask where things break down:

> "在 [步骤 X] 这一步，[角色] 现在遇到什么困难或不便吗？"

Problems must be grounded in user behavior — never fabricate. If the user says "没什么问题", accept it and move to the next step. Don't insist.

**1c — Layered Display**

For each Phase, explicitly distinguish three layers:

- **用户动作**: What the user does
- **系统触点**: What current tools/systems the user interacts with (cross-reference Persona's "当前工具")
- **产品支撑（现状）**: What our product currently provides at this step, or "无" if this is greenfield

This prevents mixing up "what users do" with "what our product should do."

**1d — Emotion Tagging**

After steps and problems are confirmed, tag the Phase's emotional state:

- ↑ 正向 — user feels in control, efficient, satisfied
- → 中性 — routine, no strong feeling
- ↓ 负向 — frustrated, anxious, blocked

Add a one-sentence reason: "↓ 负向 — 需要切换三个系统才能确认一辆车的状态"

Emotions must reference Persona's pain points or drivers. Don't fabricate emotional states.

Repeat 1a-1d for each Phase. After every 2-3 Phases, run a mid-conversation calibration.

### Step 2: Journey Assembly

**Purpose:** Assemble all enriched Phases into a complete Journey and confirm the whole picture.

**2a — Full Journey Presentation**

Display the complete Journey with all Phases, showing:
- All steps with problems inline
- All touchpoints
- Emotion flow across Phases (the "emotional arc")
- All discovered pain points in a summary table

**2b — Completion Criteria (Agent Drafts)**

Based on the last Phase's outcome and the Journey's overall goal, draft the Completion Criteria — the observable end state that means "this Journey succeeded."

> "基于旅程的最后一步，我认为这条 Journey 的完成标准是：[Agent 推断的终态]。准确吗？"

The user confirms or corrects.

**2c — Pain Points Summary**

Compile all pain points into a table with Moments of Truth marking:

| # | Phase | Step | Pain Point | Severity | Criticality | Boundary Check |
|---|---|---|---|---|---|---|
| 1 | ... | ... | ... | High/Medium/Low | Fatal/Important/Minor | ✅ In scope / ❌ Out of boundary |

**Severity**: How much it hurts (High = blocks work, Medium = slows down, Low = minor annoyance).

**Criticality** (Moment of Truth):
- **Fatal** — if this step fails, the entire Journey cannot complete. This is a Moment of Truth.
- **Important** — significant experience degradation but Journey can continue
- **Minor** — inconvenience only

**Boundary Check**: Apply the two-gate check from `references/feature-derivation-rules.md`:
1. Is this problem within the target Capability's scope?
2. Is this our product's responsibility (vs. customer's existing system)?

Mark out-of-boundary problems clearly — they won't generate Features.

**2d — Confirm**

> "这是完整的 Journey。Pain Points 标注了严重度和关键度（致命 = 此步骤失败则旅程中断）。请确认或修正。"

**2e — Generate Journey Diagrams**

After user confirms the full Journey, generate visual diagrams following `references/diagram-generation.md`. Two diagrams are evaluated:

1. **Journey Emotion Map** (always generated): Convert each Phase's steps and emotion tags into a Mermaid `journey` diagram. Map emotion tags to scores: ↑ positive → 4-5, → neutral → 3, ↓ negative → 1-2. Steps with ⚠️ problems get lower scores within the negative range.

2. **Service Blueprint** (conditional — generate only if 3+ distinct system touchpoints across the Journey's phases): Render the three-layer display (用户动作 / 系统触点 / 产品支撑) as a Mermaid `flowchart TD` with subgraphs per Phase.

Present the generated diagrams to the user:

> "我为这条旅程生成了可视化图表：
>
> [Journey Emotion Map diagram]
>
> [Service Blueprint diagram — if applicable]
>
> 图表会嵌入到最终的 Journey 文件中。需要调整吗？"

The user can request adjustments to scores, labels, or layout before proceeding.

### Step 3: Feature Derivation

**Purpose:** Derive Features from confirmed Pain Points through a traceable chain.

Follow the derivation rules in `references/feature-derivation-rules.md`. The core chain:

```
Pain Point → Opportunity → Feature Candidate → ~F Cross-Check → Feature
```

**3a — Opportunity Identification**

For each in-scope Pain Point, define the opportunity:

> "如果能 [消除/减轻痛点]，[角色] 就能 [达到的效果]。"

**3b — Feature Candidate Generation**

Each Opportunity maps to a Feature Candidate with: name, one-line description, source traceability.

**3c — ~F Cross-Check**

Compare generated Feature Candidates against product-framer's `~F` list:

| Situation | Action |
|---|---|
| ~F confirmed by Journey pain point | ✅ Formalize as F{n}, note "~F verified" |
| ~F NOT encountered in Journey | ❓ Ask user: "~F{n} [名称] 没有在旅程中出现。是因为我们遗漏了场景，还是这个功能其实不需要？" |
| New pain point, no matching ~F | ➕ New Feature, note "Journey-discovered" |

Every `~F` must have a final disposition — verified, questioned, or confirmed unnecessary. None can be silently ignored.

**3d — Feature Confirmation**

Present the Feature Mapping table:

| Phase | Pain Point | Opportunity | Feature | ~F Status |
|---|---|---|---|---|
| Phase 1 | #1 ... | 如果能... | F1 ... | C1 ~F1 ✅ verified |
| Phase 2 | #2 ... | 如果能... | F2 ... | New (Journey-discovered) |
| — | — | — | — | C1 ~F3 ❓ not encountered |

Confirm with user.

**3e — Generate Feature Derivation Chain Diagram**

After user confirms the Feature Mapping, generate the traceability chain diagram following `references/diagram-generation.md`:

**Feature Derivation Chain** (always generated when Features exist): Render Pain Points → Opportunities → Features as a Mermaid `flowchart LR` with color-coded node classes (red for pain points, yellow for opportunities, blue for features). Grouped pain points merge into single nodes. Out-of-boundary pain points are omitted. ~F status is reflected in Feature node labels.

Present inline with the Feature Mapping confirmation — no separate confirmation needed unless the user asks to adjust.

### Step 4: Gap Analysis + Output

**Purpose:** Validate completeness and save all files.

**4a — Two-Way Gap Analysis**

Check both directions:

**Direction 1: Every Phase with a Fatal/Important pain point → has a Feature?**
If not → Feature gap. Ask user whether to add a Feature or accept the gap.

**Direction 2: Every Feature → has a corresponding Phase + Pain Point?**
If not → Feature may be unnecessary. Confirm with user.

Display as a matrix:

```
Phase ↔ Feature Coverage:
  Phase 1: F1 ✅, F2 ✅
  Phase 2: F3 ✅
  Phase 3: ❌ 缺口 — Fatal pain point #4 无 Feature 支撑
  
  F4: ❌ 无对应 Phase — 确认是否需要
```

**4b — Generate Pain Point Priority Diagram (Conditional)**

If the Journey has 5+ in-scope pain points, generate a Priority Matrix following `references/diagram-generation.md`:

**Pain Point Priority Matrix**: Map pain points onto a Mermaid `quadrantChart` with Severity (x-axis) vs. Criticality (y-axis). Score mapping: 低/Low→0.2, 中/Medium→0.5, 高/High→0.8 for severity; 一般/Minor→0.2, 重要/Important→0.6, 致命/Fatal→0.9 for criticality. The quadrants help stakeholders quickly see P0 (must fix) vs. monitor vs. quick wins.

Present inline with the Gap Analysis display.

**4c — Save Files**

After confirmation, save:
1. `product-management/journeys/J{n}-{name}.md` — using `templates/journey-template.md`
2. `product-management/features/F{n}-{name}.md` — using `templates/feature-template.md`

Journey files include the `## Diagrams` section with all generated Mermaid diagrams embedded inline.

**Do not directly modify** product-framer files. If upstream issues were discovered and deferred, mention them before saving.

**4d — Completion Display**

Show output summary and next step guidance:

> "Journey 设计完成！
>
> 产出文件：[列表]
>
> 下一步：使用 user-story skill 从 Journey + Feature 创建 User Story。
> user-story 会读取 Journey 的 Pain Points 和 Feature 定义作为上下文。"

## Guiding Principles

### Problems Come from Behaviors, Not Imagination

This is the hardest discipline and the most critical. Every pain point must trace to a specific step in the Journey where the user does something and encounters friction. "I think users might struggle with X" is not valid — "users switch between 3 systems at Step 2 to find vehicle status" is.

If you can't point to the step where the problem occurs, the problem doesn't belong in this Journey.

### Distinguish Three Layers

Every Phase must clearly separate:
- **User actions** — what the person does (decisions, clicks, calls)
- **System touchpoints** — what current tools/systems are involved
- **Product support** — what our product provides (or "无" for greenfield)

Mixing these up creates false requirements — claiming customer system responsibilities as our product capabilities.

### Emotions Must Be Anchored

Don't fabricate emotional states. Every ↑/→/↓ must connect to either:
- A Persona pain point (↓ negative)
- A Persona driver being satisfied or frustrated (↑ positive or ↓ negative)
- Observed friction in the steps (↓ negative)

### Feature Traceability Is Non-Negotiable

Every Feature must have a complete trace: `Pain Point (#N) → Opportunity → Feature (F{n})`. A Feature without a traced Pain Point does not belong in the output.

### As-Is Only

Journeys describe the **current** user experience — how things work today with existing tools. The "to-be" state is implicit: Features are the to-be, derived from as-is pain points. Don't mix current-state observations with future-state aspirations in the Journey.

### Journeys Are Agent Breadcrumbs Too

Journey files serve two consumers: human BA/PM teams (for Feature derivation and US creation) and downstream development Agents (for understanding how users interact with APIs and services across system boundaries). When writing Journey Phases, remember that an Agent reading this file needs to understand the cross-service interaction path — not just "user clicks button" but "user clicks button → our API receives request → processes → returns result → user sees update." This is especially important for IoT/B2B products where device-cloud-user interaction chains define the business logic.

### Upstream Issue Escalation

journey-designer does not directly edit product-framer outputs. When Journey analysis reveals an upstream issue (Capability scope mismatch, Persona info missing, ~S inaccurate, new role discovered), present it inline:

> "在旅程分析中发现 [具体问题]。需要现在切换 product-framer 修正吗？还是先继续？"

Continue or pause based on user's answer. No separate tracking list — issues surface naturally during the workflow.

## Fast-Path Support

| Entry Point | Behavior |
|---|---|
| From product-framer output | Normal: Step 0 → Step 4 |
| Specific Capability target | "我要做 C2 的 Journey" → load only C2 + related Personas |
| Resume from partial output | Detect existing `journeys/` → load state → continue from last incomplete Journey |
| Cross-Capability Journey | User specifies ~S from multiple Capabilities → load all involved Capabilities |

## Sub-Journey Escape Hatch

When a Journey Phase involves:
- 3+ roles interacting
- Complex branching (3+ paths)
- A subprocess needing independent deep analysis

Allow expanding that Phase into a Sub-Journey:

```
File: journeys/J1-S3-detail.md (J1's Phase 3 expanded)
```

This is **on-demand expansion**, not default behavior. Only suggest when complexity warrants it.

## Resources

- `references/state-display-templates.md` — ASCII state panels for all workflow steps
- `references/enrichment-protocol.md` — Phase enrichment rules (migrated from scene-mapper)
- `references/feature-derivation-rules.md` — Feature derivation chain + ~F handling + boundary check
- `references/diagram-generation.md` — Mermaid diagram templates + tiered generation rules for journey visualization
- `references/journey-examples.md` — Complete worked example with full derivation
- `templates/journey-template.md` — Journey document template
- `templates/feature-template.md` — Feature document template
