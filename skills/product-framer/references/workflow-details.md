# Workflow Details

Complete 6-step workflow instructions. SKILL.md retains the core framework; this file provides step-by-step operational guidance.

---

## Step 0: Context Intake

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

---

## Step 1: Empathize + Open Elicitation

**Purpose:** Build shared understanding of the business context through open conversation.

Display state using `@references/state-display-templates.md` — "Elicitation State."

### Phase A — Open Narrative (Empathize)

Start with:

> "能告诉我你们目前面临的业务挑战是什么吗？是什么促使你想做这个产品/服务？"

From the user's narrative, actively listen for:
- Who are the users? (roles, relationships)
- What situations trigger their needs?
- What are they doing today? What tools?
- Where does it break down?
- What would "good" look like?

Draw from `@references/empathize-question-bank.md` for natural follow-up questions — use them conversationally, not as a checklist.

### Phase B — Structured Follow-Up (Define)

Based on gaps from Phase A, ask targeted questions to complete the picture. Use the **Dimension Coverage Gate** below to identify what's missing.

**[Calibration]**: After 2-3 key pieces of information, run a mid-conversation calibration.

### Dimension Coverage Gate — Step 1 Exit Condition

Track these dimensions throughout the conversation. Phase B targets whichever dimensions haven't reached "sufficient" yet:

| Dimension | Sufficient | Insufficient (needs follow-up) |
|---|---|---|
| **User Roles (Who)** | At least 1 role with concrete goal | Only vague labels ("users", "customers") |
| **Business Problem** | At least 1 specific difficulty in a specific scenario | Abstract complaints ("inefficient", "hard to use") |
| **Current State (As-Is)** | Know what they do now + what tools they use | No idea about current approach |
| **Desired State (To-Be)** | Desired outcome can be visualized | Only negation ("don't want to do XX anymore") |

**Exit to Phase C when:** all four dimensions (Who, Problem, As-Is, To-Be) reach "sufficient."

> **Note:** Product name is NOT an exit condition for Step 1. Names often emerge later in conversation — mark as "TBD" if not yet defined, and fill it in during Step 2 Vision confirmation.

"Sufficient" does not mean "perfect" — it means **enough to write a calibratable Vision draft.** Step 2 has its own confirmation loop; don't try to perfect everything here.

If a dimension stays insufficient after 2 targeted questions, mark it "TBD" and note it explicitly in the state display. Don't block the flow.

If dimensions contradict each other (e.g., stated problem doesn't match described workflow), trigger a calibration:

> "你刚才说 X，但前面提到 Y，这两个怎么对应？"

### Phase C — Synthesis Confirmation

Before synthesizing, flush the Parking Lot — route each parked item to its proper field or discard it:

```
Parking Lot cleanup:
  · [item 1] → route to Vision.Constraints
  · [item 2] → route to Vision.Non-Goals
  · [item 3] → hold for Capability phase
  · [item 4] → out of scope, discard

  Confirm routing?
```

Then synthesize:

> "基于我们的对话，我理解的整体情况是这样的：... 哪里需要修正？"

---

## Step 1.5: Persona Confirmation + LVT Goal Derivation

**Purpose:** Capture personas and derive business-outcome goals using Lean Value Tree.

### Phase A — Extract Personas

From Step 1, extract personas — both **user roles** (functional needs) and **review roles** (non-functional: security auditors, reliability engineers, etc.). For each, capture: role name, type, daily workflow, current tools, pain points, drivers, typical quotes.

### Phase B — LVT Goal Derivation

For each persona:

1. **Derive the value stream** — Based on the role name and domain context, map the end-to-end value flow. Use questions from `@references/empathize-question-bank.md` "Surfacing Business Outcomes (LVT)" if needed.

2. **Derive business goals** — From each value stream stage, derive business-outcome goals. Goals must be outcome statements, NOT feature descriptions:
   - ✅ "Improve vehicle utilization rate"
   - ❌ "Real-time vehicle monitoring"

3. **Present for validation**:
   > "Based on [role]'s value stream: [stages], here are the business goals: [goals]. Is this value stream complete? Any goals missing or off?"

4. **Handle partial coverage** — Some goals may have no corresponding product capability. Keep all goals in the persona; the gap becomes scoping input for Step 3.

### Phase C — Present and Save

Present all personas with value streams and goals:

> "我从对话中识别出这些角色：... 还有其他关键角色吗？信息哪里需要修正？"

After confirmation, save to `personas/P{n}-{name}.md` using `templates/persona-template.md`.

---

## Step 2: Vision Draft → Confirm

**Purpose:** Synthesize all elicited information into a structured Vision document.

Display vision draft using `@references/state-display-templates.md` — "Vision Draft State."

The Vision includes (see `templates/vision-template.md` for full format):

- **Product Name** — record here if not yet defined in Step 1
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

Ask:

> "以上 Vision 内容是否正确？直接说'确认'或告诉我哪里需要修改。"

---

## Step 3: Collaborative Capability Decomposition

**Purpose:** Decompose Vision into Capabilities. Since persona goals are now business outcomes (via LVT in Step 1.5), goal-based decomposition is the natural default.

### 3a — Present Decomposition Dimensions

Prefer persona business goals as the primary dimension. Offer alternatives from `@references/decomposition-patterns.md` only when goals don't map cleanly:

> "Based on [P1]'s business goals: [goal1], [goal2], [goal3] — each maps to a Capability. OK to decompose this way? Or would you prefer a different dimension (by role, by workflow stage, etc.)?"

### 3b — User Selects Dimension

User chooses or proposes a decomposition approach.

### 3c — Generate Capability Candidates

Each candidate includes:
- C-ID and name
- **User Goal** — a concrete statement of what the user achieves (not abstract "description")
- **Service Personas** — P-IDs
- **Scope**: Includes / Excludes

Present and confirm.

### 3d — Conceptual Scenes (~S)

For each Capability, identify 2-3 conceptual scenes:

| ID | Scene Name | Trigger | Key Activities |
|---|---|---|---|
| ~S1 | [name] | [trigger] | [2-3 activities] |

> `~S` prefix = conceptual. Formalized in `journey-designer`.

If you can't think of 2-3 scenarios for a Capability, it's either too abstract (split) or too narrow (merge). Conceptual Scenes validate Capability boundaries.

### 3e — Feature Candidates (~F)

For each Capability, derive feature candidates from scope and ~S:

| ID | Feature Name | One-Line Description | Source |
|---|---|---|---|
| ~F1 | [name] | [description] | [Scope / ~S derivation] |

> `~F` prefix = candidate. Formalized in `journey-designer`.

### 3f — Overall Confirmation

Present the complete Capability set with ~S and ~F. Confirm.

---

## Step 4: Coverage Check + Worksheet

**Purpose:** Validate that Capabilities fully cover Vision elements.

Display worksheet using `@references/state-display-templates.md` — "Worksheet State."

Coverage checks:
- Every Persona → covered by at least one Capability
- Every Business Problem → addressed by at least one Capability
- Every Core Value → delivered by at least one Capability
- Differentiation → reflected in Capabilities
- No overlapping scope between Capabilities (MECE)
- Each Capability independently deliverable
- Vision.md stays in sync with current persona files and capability files — update any outdated sections before Step 5

For each Capability, confirm:
- Priority (P0/P1/P2) with justification
- Dependencies (which Capabilities must exist first)

Ask:

> "Coverage Check 是否通过？直接说'确认'或告诉我缺什么。"

---

## Step 5: Output + Save Files

**Purpose:** Generate and save all output files.

1. `product-management/vision.md` — Vision with Persona index + Capability reverse index
2. `product-management/personas/P{n}-{name}.md` — update with Capability references (`## 关联 Capabilities`)
3. `product-management/capabilities.yaml` — lightweight index: ID, name, priority, status, persona_goals, file path
4. `product-management/capabilities/C{x}-{name}.md` — with ~S and ~F sections

Display output summary using `@references/state-display-templates.md` — "Output State."

Ask:

> "Planning 完成！是否需要继续 Journey 设计？（下一步使用 journey-designer skill）"
