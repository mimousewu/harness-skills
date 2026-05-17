# Product Framer

Frame a product's Vision, Personas, and Capabilities through Design Thinking-driven elicitation. This skill guides a structured conversation that progressively builds shared understanding — from open narrative to standardized, traceable output artifacts.

**Scope:** Vision → Persona → Capability (with conceptual ~S and ~F). Stops here. Downstream skills handle Journey, Feature formalization, and User Story.

---

## Methodology Foundations

### Harness Engineering

Product-framer is designed under the core premise of Harness Engineering:

> **Make the system highly legible to Agents — any operation requiring a human to click "continue" is a Harness failure.**

This shifts the nature of business analysis from "writing requirements for humans" to "leaving breadcrumbs for Agents." Every output artifact — Vision, Persona, Capability — is not just a document for people to read, but **structured context** that enables Agents to understand project principles, delineate domain boundaries, and judge "what constitutes good work."

This premise directly drives the following design decisions:

- Artifacts must be structured with explicit fields, machine-parseable (not prose-style PRDs)
- Non-Goals and Constraints must be explicitly recorded (Agents cannot infer implicit boundaries)
- Dual Personas (review roles defining non-functional requirements) ensure Agents don't optimize solely for functional correctness
- Bidirectional references between artifacts allow Agents to trace back from any node to Vision principles

### Design Thinking Integration

Product-framer doesn't "run the DT process then output documents." Instead, it **turns DT into the questioning framework of the conversation itself:**

| DT Phase | Manifestation in product-framer |
|---|---|
| **Empathize** | Step 1 Phase A — open narrative, listening for roles, behaviors, pain points |
| **Define** | Step 1 Phase B — Dimension Coverage Gate-driven structured follow-up |
| **Ideate** | Step 3 — collaborative decomposition with multi-dimensional Capability candidates |
| **Prototype** | Step 2 Vision draft + Step 3 conceptual ~S/~F outputs |
| **Test** | Pervasive — mid-conversation calibration, phase confirmations, Coverage Check |

DT is not a separate step bolted onto the workflow. It IS the interaction logic.

### Elicitation vs Extraction

This is the fundamental methodological choice underlying product-framer's interaction design:

| | Extraction (rejected) | Elicitation (adopted) |
|---|---|---|
| Question style | Closed, field-by-field | Open, distilled from narrative |
| User experience | Like filling a form | Like talking to a senior BA |
| Information quality | "Correct answers" | "Real situations" |
| Implicit knowledge | Lost | Captured |
| Validation | Single confirmation at the end | Calibration every 2-3 key inputs |

A senior BA doesn't ask "who is the target user?" — they say "tell me about the business challenges you're facing" and *listen* for users, problems, and value from the narrative. This is the interaction foundation of product-framer.

### Business Analysis Layer Structure

Product-framer owns the top three layers of the analysis chain:

| Layer | Artifact | Responsibility | product-framer owns |
|---|---|---|---|
| **Vision** | Vision + Non-Goals + Constraints | Define "what constitutes good work" | ✅ |
| **Persona** | Persona (dual perspective) | Define who uses the product, what they care about | ✅ |
| **Capability** | Capability + ~S + ~F | User-perceptible ability modules | ✅ |
| Journey | Journey / CUJ | End-to-end interaction paths | ❌ downstream |
| Feature | Feature (formalized) | Concrete feature definitions | ❌ downstream (~F is conceptual input) |
| Task | User Story + AC | Executable task entry points | ❌ downstream |

Each layer's output is the next layer's input. Product-framer must guarantee the quality of its three layers, because every downstream skill builds on this foundation.

---

## Design Principles

### 1. Three-Phase Elicitation

The core interaction model follows three progressive phases:

| Phase | Action | DT Mapping |
|---|---|---|
| **Phase A — Open Narrative** | "聊聊你们面临的业务挑战" → listen for roles, problems, scenarios | Empathize |
| **Phase B — Structured Follow-Up** | Target missing dimensions, exit controlled by Dimension Coverage Gate | Define |
| **Phase C — Synthesis Confirmation** | Flush Parking Lot → confirm overall understanding | Prototype + Test |

### 2. Hypothesis Testing (Mid-Conversation Calibration)

Don't collect all information before confirming. Pause to validate understanding **every 2-3 key pieces of information:**

> "到目前为止我的理解是：你们的 [角色] 在 [场景] 下面临 [问题]，现在的应对方式是 [现状]。对吗？"

The final draft confirmation is a safety net. Real calibration happens during the conversation.

### 3. Answer Drift Handling + Parking Lot

Users almost never answer the exact question asked. Product-framer classifies drift into four types with corresponding strategies:

| Type | Strategy |
|---|---|
| Off-topic but valuable | Extract → park in Parking Lot → steer back to current topic |
| Abstraction mismatch | Accept the answer → reverse-engineer the real need behind it |
| Information dump | Extract main threads → confirm → drill into one at a time |
| Evasion or uncertainty | Don't insist → reframe from a different angle, or mark "待定" |

Core principle: **never dismiss anything the user says.** Flush the Parking Lot before Phase C synthesis.

### 4. Persona as Independent First-Class Citizen

Independent lightweight files (`personas/P{n}-{name}.md`) containing only information that downstream skills actually consume: goals, daily workflow, current tools, pain points, drivers, typical quotes.

Supports **dual perspective:**
- **User roles** (functional needs): define what the product must do
- **Review roles** (non-functional needs): define what "good work" looks like — security, reliability, compliance

Review roles ensure downstream Agent implementation doesn't optimize solely for functional correctness while missing quality constraints.

### 5. Collaborative Decomposition, Not One-Way Generation

Capability decomposition is co-creation, not approval:

1. Present 2-3 decomposition dimensions (by user role / by user goal / by value proposition / by capability area)
2. User selects or proposes a dimension
3. Generate candidates per chosen dimension → user confirms and adjusts

Users participate in the decomposition thinking process. They're not passively approving an Agent-generated plan.

### 6. Conceptualization Before Formalization (~S / ~F)

Produce conceptual Scenes (~S) and Feature Candidates (~F) at the Capability stage:

- **~S validates Capability boundaries**: can't think of 2-3 usage scenarios → too abstract (split) or too narrow (merge)
- **~F concretizes Scope**: users can evaluate concrete features more easily than abstract Includes lists
- **~ prefix = draft**: formalized and replaced when downstream skills complete their work

### 7. Bidirectional Flow Support

Step 0 Context Intake differentiates:

- **Forward Mode**: no materials → start from Empathize
- **Reverse Mode**: existing feature list / US / PRD → read and extract → pre-fill subsequent steps
- **Resume**: detect existing `product-management/` directory → load state → continue from last incomplete step

### 8. Vision Completeness Requirements

Beyond the obvious (Business Problems, Core Values, Differentiation), Vision must include:

| Field | Why it's needed |
|---|---|
| **Non-Goals** | Prevent scope creep; tell downstream "what is NOT good work" |
| **Constraints** | Make implicit constraints explicit; prevent wrong assumptions during implementation |
| **Assumptions** | State preconditions; enable rapid impact assessment when assumptions fail |
| **Success Metrics** | Without measurement criteria, all priority judgments lack an anchor |
| **Product Strategy** | Service layer strategy (L1/L2, only for data service products) |

### 9. Capabilities Must Be User-Perceptible

Organize by user goals, not technical layers:

- ❌ Technical layers: data collection → processing → storage → display
- ✅ User goals: "know where vehicles are", "detect dangerous driving"

Each Capability is defined by a **User Goal** (not an abstract Description) — concrete enough that a user reading it can say "yes, that's what I want" or "no, that's wrong."

### 10. Problems Come from Behaviors, Not Imagination

- ❌ "I think users might have this problem"
- ✅ "Users encountered this obstacle while doing X"

### 11. Bidirectional Traceability Between Artifacts

```
Vision.Personas index  ←→  personas/P{n}.md.关联 Capabilities
Vision.Capabilities index  ←→  capabilities/C{x}.md.Service Vision
Capability.Service Personas  ←→  Persona files
```

YAML serves as **pure index only** (ID + name + priority + status + file path + ~S/~F counts). Detailed information lives exclusively in .md files. No duplication.

### 12. Modular Design: Boundary Discipline + Artifacts as Interface Contracts

**Boundary discipline:** Product-framer only handles Vision → Persona → Capability (with conceptual ~S/~F). It does not extend into Journey, Feature formalization, or User Story. A monolithic skill would cause context explosion, maintenance difficulty, and degraded question quality. Each skill maintains single responsibility so it can go deep in its own phase.

**Artifacts as interface contracts:** Product-framer doesn't need to know how downstream skills work, but must guarantee its outputs are directly consumable:

| Artifact | Interface role |
|---|---|
| `vision.md` | Global principle anchor (Non-Goals, Constraints directly constrain downstream implementation) |
| `personas/P{n}.md` | Deep persona info → foundational input for downstream journey mapping |
| `C{x}.md` ~S sections | Conceptual scenes → downstream starting point (present = verify+expand mode; absent = from-scratch mode) |
| `C{x}.md` ~F sections | Feature candidates → input for downstream Feature formalization |
| `capabilities.yaml` | Pure index + ~S/~F counts → downstream routing decisions |

**No redundant questioning:** Personas are discovered and deepened during Empathize, then saved to files. Downstream skills read the files — they don't re-ask the user the same questions. Each skill is responsible only for incremental information collection; existing artifacts are shared context.

---

## Workflow Overview

```
Step 0:   Context Intake (material collection or start from scratch)
Step 1:   Empathize + Open Elicitation (three-phase conversation)
Step 1.5: Persona Confirmation (capture discovered personas)
Step 2:   Vision Draft → Confirm
Step 3:   Collaborative Capability Decomposition (with ~S and ~F)
Step 4:   Coverage Check + Worksheet
Step 5:   Output + Save Files
```

See `SKILL.md` for detailed step-by-step instructions.

---

## Output Specification

```
product-management/
  vision.md                   # Product Vision (with Persona index + Capability reverse index)
  personas/                   # Persona detail files
    P1-{name}.md
    P2-{name}.md
  capabilities.yaml           # Lightweight index (routing only, no duplication)
  capabilities/               # Capability detail files (with ~S and ~F sections)
    C1-{name}.md
    C2-{name}.md
```

File naming: lowercase kebab-case English slugs (e.g., `C1-vehicle-monitoring.md`, `P1-fleet-manager.md`).

---

## Key Design Decisions

### Dimension Coverage Gate

**Problem:** Open-ended conversation has no natural "done filling" signal. The Agent must decide when to move forward.

**Decision:** Introduce an explicit **dimension coverage model** — making the senior BA's implicit "is this enough?" judgment explicit as a table:

| Dimension | Sufficient | Insufficient |
|---|---|---|
| **用户角色 (Who)** | At least 1 role with a concrete goal | Only vague labels ("用户"、"客户") |
| **业务问题 (Problem)** | At least 1 specific difficulty in a specific scenario | Abstract complaints ("效率低"、"不好用") |
| **当前状态 (As-Is)** | Know what they do now + what tools they use | No idea about current approach |
| **期望状态 (To-Be)** | Desired outcome can be visualized | Only negation ("不想再 XX") |
| **产品名称 (Name)** | Has an explicit name | Not mentioned |

**"Sufficient" means enough to write a calibratable Vision draft, not "perfect."** Step 2 has its own confirmation loop. Pursuing perfection in Step 1 causes user fatigue and information distortion.

**Per-dimension question limit: 2 attempts.** After 2 targeted questions on the same dimension, mark it "待定" and move on. Later phases (Capability decomposition, Scene identification) often fill gaps naturally.

**Contradictions outrank gaps.** Contradictions between dimensions (e.g., stated problem doesn't match described workflow) are more dangerous than a blank dimension. Trigger calibration immediately upon detecting contradictions.

### Answer Drift Classification

**Problem:** Users almost never answer the exact question asked. This is normal in BA conversations — drift carries information.

**Decision:** Classify into four types with explicit strategies instead of relying on implicit BA intuition.

**Type A — Off-topic but valuable:**

> Agent asks: "目标用户是谁？"
> User answers: "之前做过一个项目，客户的系统特别老旧，接口都是 SOAP 的..."

The user went off to discuss technical constraints, but "SOAP interfaces" is important Constraints information. Strategy: extract, park, steer back.

```
"了解，现有系统是 SOAP 接口——这个我记下了，后面讨论约束条件时会用到。
回到目标用户：除了这个客户，还有其他类型的使用者吗？"
```

**Type B — Abstraction mismatch:**

> Agent asks: "用户面临什么业务问题？"
> User answers: "我们需要一个实时数据推送服务。"

The user responded with a solution, not a problem. Strategy: accept the solution, reverse-engineer the problem.

```
"实时数据推送——这是一种解决方案。能帮我理解一下，
用户现在因为缺少实时数据，具体遇到了什么困难？"
```

Why not correct directly ("that's a solution, not a problem"): the user would feel dismissed. Follow their thread to extract the real need.

**Type C — Information dump:**

User pours out too much at once, covering many dimensions but none deeply. Strategy: extract main threads, confirm, drill into one at a time.

```
"信息量很大，我先梳理确保没遗漏。你提到了几个关键点：
1. 主要用户是 [角色A] 和 [角色B]
2. 核心问题是 [X]
3. 竞品方面有 [Y]
对吗？我想先从 [最关键的维度] 展开聊。"
```

**Type D — Evasion or uncertainty:**

> Agent asks: "差异化是什么？"
> User answers: "不太确定...就是想做个好用的产品。"

Strategy: don't insist. Reframe — negation is often easier than affirmation.

```
"没关系，差异化后面再定义。
换个角度——现有竞品/方案最让客户不满意的地方是什么？"
```

Core principle: **never dismiss what the user says.** Tangents reveal what they care about most.

### Parking Lot: Conversational, Not Persisted

**Why it exists:** Drift handling (Types A/B/C) produces "valuable but doesn't belong to the current discussion dimension" information fragments. Without explicit tracking, these fragments get lost as the conversation progresses.

**Why it's not persisted to a file:**

1. **Most items find a home.** As phases progress, parked items get absorbed into formal artifacts (Vision.Constraints, Vision.Non-Goals, Capability.Excludes, etc.). A `parking-lot.md` would become a stale snapshot.
2. **No downstream consumer.** Downstream skills read Capability/Persona/Journey/Feature files. No skill needs to read "what the user said off-topic during conversation."
3. **Maintenance cost > benefit.** Persisting means updating on every conversation turn, cleaning up after each phase, judging validity across sessions — overhead with no payoff.

**Lifecycle:**

```
Accumulate during conversation → Flush item-by-item in Phase C (user confirms routing)
→ Information enters formal artifacts → Parking Lot clears
```

Routing destinations:
- Technical constraints → Vision.Constraints
- Explicit exclusions → Vision.Non-Goals
- Preconditions → Vision.Assumptions
- Downstream clues → Carry to Capability phase (marked in state display)
- Unclassifiable → Discard after confirmation

### Why Traditional BA Intuition Must Be Made Explicit

| Dimension | Traditional BA interview | product-framer |
|---|---|---|
| Information gathering | Face-to-face, reads expressions/body language | Pure text, relies on semantic understanding |
| Drift handling | BA intuition + experience | Explicit four-type taxonomy + Parking Lot |
| Sufficiency judgment | "BA just knows" | Dimension Coverage Gate made explicit |
| Validation mechanism | Meeting minutes + follow-up emails | Real-time calibration every 2-3 inputs |
| Information persistence | Meeting notes document | Directly into structured template fields |

A senior BA's implicit capabilities (when is enough, how to handle drift) must be made explicit in AI Agent scenarios. Otherwise the Agent will either over-question or converge too early.

---

## Resources

| File | Purpose |
|---|---|
| `SKILL.md` | Complete step-by-step execution instructions |
| `references/state-display-templates.md` | ASCII state panels for all workflow steps |
| `references/decomposition-patterns.md` | Capability decomposition dimensions and guidelines |
| `references/empathize-question-bank.md` | Categorized open questions for Empathize phase |
| `references/worksheet-examples.md` | Complete examples with full derivation |
| `templates/vision-template.md` | Vision document template |
| `templates/capability-template.md` | Capability file template |
| `templates/persona-template.md` | Persona file template |
