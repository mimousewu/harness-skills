# Journey Designer

Map a Capability's conceptual scenes into end-to-end user journeys and derive Features from journey pain points through collaborative dialogue. This skill takes product-framer's output (Capabilities with `~S` and `~F`) and produces structured Journey maps plus Feature definitions.

**Scope:** Capability → Journeys → Features. Upstream: `product-framer` (Vision, Personas, Capabilities). Downstream: `user-story` (User Stories with acceptance criteria).

---

## Methodology Foundations

### Position in the Analysis Chain

Journey-designer owns the middle layer — between user-perceptible Capabilities and executable User Stories:

| Layer | Artifact | Owner | journey-designer role |
|---|---|---|---|
| Vision | Vision + Personas | product-framer | Reads as context |
| Capability | Capability + ~S + ~F | product-framer | Reads as input |
| **Journey** | **Journey (Phases + Pain Points + Emotions)** | **journey-designer** | **✅ Produces** |
| **Feature** | **Feature (derived from pain points)** | **journey-designer** | **✅ Produces** |
| User Story | US + Acceptance Criteria | user-story | Consumes Journey + Feature |

This position means journey-designer must serve two masters: it consumes upstream structured artifacts (Persona files, Capability ~S/~F) and produces downstream-consumable outputs (Journey pain points feed User Story creation, Feature definitions feed implementation).

### Harness Engineering: Journeys as Agent Breadcrumbs

Under Harness Engineering, Journey files are not just human-readable documentation — they are **structured context for development Agents:**

> Agents need to know "how users interact with APIs and services across system boundaries."

This means Journey Phases must document cross-service interaction paths, not just "user clicks button." An Agent reading a Journey file should understand the device → cloud → user interaction chain that defines business logic. This is especially critical for IoT/B2B products.

This dual-consumer requirement (human BA/PM teams + development Agents) directly shapes the output format — structured markdown with explicit three-layer display per Phase.

### Unified Scene + Journey Model

The skill merges two traditionally separate concepts — **Scene** (a snapshot of a triggering situation) and **CUJ / Customer User Journey** (an end-to-end path) — into a single **Journey** artifact.

The analysis behind this merge considered three complexity levels:

| Complexity | Scene vs CUJ distinction value | Decision |
|---|---|---|
| Simple (1 trigger, 1 role, linear) | Low — Scene and CUJ are nearly identical | Merge: one Journey covers both |
| Medium (2-3 triggers, some branching) | Ambiguous — maintaining both creates redundancy | Merge: Journey with multiple Phases absorbs Scene scope |
| Complex (3+ roles, heavy branching) | Potentially high — subprocess needs isolation | Merge + **Sub-Journey escape hatch** for complex Phases |

Result: a unified Journey concept where each Phase is essentially an enriched Scene embedded in an end-to-end flow. The Sub-Journey escape hatch handles edge cases where a Phase warrants independent deep analysis (3+ roles, complex branching).

### Three-Layer Display Model

Every Journey Phase explicitly separates three layers:

- **用户动作 (User Actions):** What the person does — decisions, clicks, calls, observations
- **系统触点 (System Touchpoints):** What current tools/systems the user interacts with
- **产品支撑 (Product Support):** What our product provides at this step, or "无" for greenfield

This structure descends from Shostack's 1984 Service Blueprint (Customer Actions → Frontstage → Backstage → Support Processes), collapsed from four layers to three for practical elicitation.

The separation prevents a common failure mode: **mixing up what users do with what our product should do**, which creates false requirements — claiming customer system responsibilities as our product capabilities.

### Feature Derivation Chain

Features are not imagined or listed from intuition. They follow a traceable derivation:

```
Pain Point → Opportunity → Feature Candidate → ~F Cross-Check → Feature
```

This chain descends from Design Thinking's Define → Ideate transition (Stanford d.school), made explicitly traceable with Pain Point → Opportunity → Feature links. The `~F Cross-Check` step is original to this skill — reconciling top-down feature candidates from product-framer with bottom-up journey-discovered features.

The result: every Feature has a complete audit trail back to a specific step in a specific Phase where a specific user encounters a specific problem.

---

## Design Principles

### 1. Problems Come from Behaviors, Not Imagination

The hardest discipline and the most critical. Every pain point must trace to a specific step in the Journey where the user does something and encounters friction.

- ❌ "I think users might struggle with X"
- ✅ "Users switch between 3 systems at Step 2 to find vehicle status"

If you can't point to the step where the problem occurs, the problem doesn't belong in this Journey.

### 2. As-Is Only, No To-Be Mixing

Journeys describe the **current** user experience — how things work today with existing tools. The "to-be" state is implicit: Features ARE the to-be, derived from as-is pain points.

Don't mix current-state observations with future-state aspirations in the Journey. This is standard Service Design practice.

### 3. Emotion Tags Must Be Anchored

Don't fabricate emotional states. Every ↑ (positive) / → (neutral) / ↓ (negative) tag must connect to:
- A Persona pain point (↓)
- A Persona driver being satisfied or frustrated (↑ or ↓)
- Observed friction in the steps (↓)

This simplified 3-level model descends from UXPressia-style emotional journey mapping, which typically uses a 5-point scale. The simplification prioritizes elicitation speed over precision.

### 4. Feature Traceability Is Non-Negotiable

Every Feature must have a complete trace: `Pain Point (#N) → Opportunity → Feature (F{n})`. A Feature without a traced Pain Point does not belong in the output. This is the skill's core quality gate.

### 5. ~F Cross-Check Reconciliation

Product-framer produces conceptual Feature Candidates (`~F`). Journey analysis discovers features bottom-up from pain points. These two sources must be reconciled:

| Situation | Action |
|---|---|
| ~F confirmed by Journey pain point | ✅ Formalize as F{n} |
| ~F NOT encountered in Journey | ❓ Ask user: missed scenario or unnecessary feature? |
| New pain point, no matching ~F | ➕ New Feature, mark "Journey-discovered" |

Every `~F` must have a final disposition. None can be silently ignored.

### 6. Moments of Truth (Criticality Assessment)

Pain points are assessed on two dimensions:

- **Severity:** How much it hurts (High = blocks work, Medium = slows down, Low = annoyance)
- **Criticality:** Moment of Truth classification:
  - **Fatal** — if this step fails, the entire Journey cannot complete
  - **Important** — significant experience degradation but Journey can continue
  - **Minor** — inconvenience only

The "Fatal" designation descends from Jan Carlzon's Moments of Truth concept (1987) — critical customer interaction points where the entire relationship is at stake. Fatal pain points are P0 candidates for Feature derivation.

### 7. Boundary Discipline: Read Upstream, Never Edit

Journey-designer writes ONLY to `journeys/` and `features/`. It never directly modifies product-framer files (`vision.md`, `personas/`, `capabilities/`). When Journey analysis reveals upstream issues (Capability scope mismatch, Persona info missing), the skill surfaces the issue and offers the user a choice to pause and switch to product-framer.

This maintains clean ownership boundaries in the modular skill chain.

### 8. Inherited Interaction Model

Journey-designer inherits product-framer's interaction rules without modification:

- Progressive elicitation, not form-filling
- One question per turn
- Visual state display before every question
- Mid-conversation calibration every 2-3 Phases
- Confirm → Revise → Continue loop
- Answer drift handling (4 types)

These are the same methodological foundations (Contextual Inquiry, BA interviewing technique, hypothesis testing) applied to a different content domain.

---

## Key Design Decisions

### Why Scene and CUJ Were Merged

Traditional approaches maintain separate artifacts: Scenes (situational snapshots) and Customer User Journeys (end-to-end paths). Maintaining both creates redundancy in most cases and forces users to track information in two places.

The merge means each Journey Phase IS an enriched Scene embedded in an end-to-end flow. Scene's triggering context becomes the Journey's Trigger field. Scene's activities become Phase steps. Scene's problems become Phase pain points.

The Sub-Journey escape hatch preserves the ability to go deep on complex Phases without forcing that complexity on every Phase.

### Why Emotions Are Simplified to 3 Levels

Industry-standard emotional journey maps use 5-point or 7-point scales. The skill uses 3 levels (↑/→/↓) because:

1. **Elicitation speed**: users can immediately tag without deliberation
2. **Directional signal > precision**: for Feature derivation, knowing "negative here" matters more than distinguishing "somewhat negative" from "very negative"
3. **Severity already captures intensity**: the separate Severity (High/Medium/Low) dimension handles the "how bad" question

### Why Mermaid for Diagrams

| Factor | Decision |
|---|---|
| **Consistency** | product-discovery already uses Mermaid — established convention in the harness skill suite |
| **Portability** | Renders natively on GitHub, GitLab, Obsidian — no tool dependencies |
| **Agent-friendly** | Text-based, generated inline, no external CLI calls |
| **Editability** | Users modify diagrams by editing markdown text directly |

### Tiered Diagram Generation

Not all diagrams are generated every time. Tiering avoids visual noise for simple journeys while providing full visualization for complex ones:

| Diagram | Condition | Methodology Origin |
|---|---|---|
| **Journey Emotion Map** | Always | CJM horizontal timeline + emotional journey mapping |
| **Feature Derivation Chain** | Always (when Features exist) | Design Thinking Define→Ideate transition |
| **Service Blueprint** | 3+ distinct system touchpoints | Shostack's Service Blueprint (1984) |
| **Pain Point Priority Matrix** | 5+ in-scope pain points | Carlzon's Moments of Truth (1987) — Severity × Criticality quadrant |

### Output Format: Structured Markdown with Tables

The format was determined by three forces:

| Force | Requirement | How the format serves it |
|---|---|---|
| **user-story skill** (downstream) | Pain Points with step-level traceability | Pain Points Summary table has Phase + Step columns |
| **Development Agents** (downstream) | Cross-service interaction paths | Three-layer display per Phase |
| **Human BA/PM teams** | At-a-glance journey health | Mermaid diagrams embedded in Journey markdown |

---

## Methodology Concept-to-Source Traceability

The skill synthesizes concepts from multiple established UX/BA frameworks. No single framework is followed end-to-end — specific concepts are drawn from each and composed into a unified workflow.

| Skill Concept | Origin | Key Source |
|---|---|---|
| Phases as horizontal timeline | Customer Journey Mapping (CJM) | Formalized by service design practitioners (2000s). Popularized by Adam Richardson's 2010 HBR article. |
| Touchpoints (系统触点) | Service Blueprint | G. Lynn Shostack's 1984 HBR paper "Designing Services That Deliver." |
| Three-layer display | Service Blueprint | Descendant of Shostack's Customer Actions / Frontstage / Backstage layers, collapsed to 3. |
| Pain Points → Opportunities → Features | Design Thinking (Stanford d.school) | The Define → Ideate transition, made traceable with explicit links. |
| Emotion tagging (↑/→/↓) | Emotional Journey Mapping | Popularized by UXPressia, Smaply. Simplified from 5-point to 3-level for elicitation speed. |
| Moments of Truth (criticality) | Jan Carlzon (1987) + A.G. Lafley | Carlzon's concept of critical customer interaction points; "Fatal = Journey breaks." |
| Progressive elicitation | Contextual Inquiry | Beyer & Holtzblatt (1998) — discover problems in context, not through abstract questioning. |
| ~F cross-check | Original to this skill | Reconciling top-down (product-framer) with bottom-up (journey-discovered) features. |
| As-Is journey only | Service Design canon | Map current state first, then derive improvements. Features = the implicit to-be. |

---

## Workflow Overview

```
Step 0:   Load Context + Journey Scoping (read ~S, group into Journeys)
Step 1:   Phase-by-Phase Enrichment (steps, problems, touchpoints, emotions)
Step 2:   Journey Assembly + Diagram Generation (full picture + visual)
Step 3:   Feature Derivation + Traceability Diagram (pain points → features)
Step 4:   Gap Analysis + Priority Diagram + Output (validate + save)
```

See `SKILL.md` for detailed step-by-step instructions.

---

## Output Specification

```
product-management/
  journeys/
    J1-{name}.md              # Journey detail files (with embedded Mermaid diagrams)
    J2-{name}.md
  features/
    F1-{name}.md              # Feature detail files
    F2-{name}.md
```

Journey files include a `## Diagrams` section with all generated Mermaid diagrams embedded inline.

---

## Resources

| File | Purpose |
|---|---|
| `SKILL.md` | Complete step-by-step execution instructions |
| `references/state-display-templates.md` | ASCII state panels for all workflow steps |
| `references/enrichment-protocol.md` | Phase enrichment rules (migrated from deprecated scene-mapper) |
| `references/feature-derivation-rules.md` | Feature derivation chain + ~F handling + boundary check |
| `references/diagram-generation.md` | Mermaid diagram templates + tiered generation rules |
| `references/journey-examples.md` | Complete worked example with full derivation |
| `templates/journey-template.md` | Journey document template |
| `templates/feature-template.md` | Feature document template |
