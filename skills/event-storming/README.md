# Event Storming

Domain discovery through interactive Event Storming elicitation, producing a formalized DDD domain model aligned with Scania's `ddd-concepts` annotation library. This skill takes upstream Harness output (Journeys, Features, User Stories) and produces structured domain model artifacts through collaborative dialogue.

**Scope:** Journey + Features + User Stories → Event Storming → Domain Model. Upstream: `user-story` (User Stories with AC), `journey-designer` (Journeys, Features), `product-framer` (Vision, Personas, Capabilities). Downstream: implementation.

---

## Methodology Foundations

### Position in the Analysis Chain

Event-storming owns the domain modeling layer — the translation from "what the user needs" to "how the domain is structured":

| Layer | Artifact | Owner | event-storming role |
|---|---|---|---|
| Vision | Vision + Personas | product-framer | Reads constraints, non-goals |
| Capability | Capability + ~S + ~F | product-framer | Reads for BC framing |
| Journey | Journey (Phases + Pain Points) | journey-designer | **Entry point** — selected journey defines scope |
| Feature | Feature definitions | journey-designer | Reads as scope boundary |
| User Story | US + Acceptance Criteria | user-story | Reads AC for mechanical extraction |
| **Domain Model** | **BC directories + aggregate files** | **event-storming** | **✅ Produces** |

This position means event-storming bridges the gap between business analysis (upstream) and code architecture (downstream). Without explicit domain modeling, developers make implicit domain decisions scattered across code — aggregate boundaries are discovered during debugging, not during design.

### Harness Engineering: Domain Models as Agent Blueprints

Under Harness Engineering, domain model files are **structural guidance for development Agents:**

> Agents generating code need to know aggregate boundaries, invariants, command preconditions, and event payloads. A domain model is the structural blueprint that ensures Agents produce architecturally sound implementations, not just plausible-looking code.

This dual-consumer requirement (human architects + development Agents) shapes every output format — structured markdown with explicit annotations, typed fields, and source traceability.

### Event Storming: From Physical Workshop to AI-Assisted Process

Event Storming (Alberto Brandolini, 2013) is a collaborative domain discovery technique using colored sticky notes on a wall. In the AI-assisted context, the skill preserves the same thinking process but makes the interaction model explicit about **what the AI derives autonomously** vs. **what requires user input**.

The key insight from the step1 research: different Event Storming elements have different user input requirements:

| Element | Color | AI Can Derive? | User Input Level |
|---------|-------|---------------|-----------------|
| Domain Events | 🟠 Orange | Yes — from AC "Then..." clauses | **Low** — confirm |
| Commands | 🔵 Blue | Yes — from "I want to..." clauses | **Low** — confirm |
| Read Models | 🟢 Green | Yes — from "So that..." clauses | **Low** — confirm |
| External Systems | 🩷 Pink | Yes — from Journey touchpoints | **Low** — confirm |
| Policies | 🟣 Lilac | Partially — AI spots pattern, user defines rule | **Medium** — elicit |
| Aggregate Boundaries | 🟡 Yellow | No — consistency requirements aren't in User Stories | **High** — design decision |
| Invariants | — | Partially — some from AC edge cases | **High** — implicit knowledge |
| Ubiquitous Language | — | Partially — from Feature/Capability vocabulary | **Medium** — validate |

This classification drives the skill's adaptive workflow: mechanical extraction for low-input elements, interactive elicitation for high-input ones.

### Two Artifacts, Not One

The Event Storming → Domain Model pipeline produces two distinct artifact types:

| | Event Storming | Domain Model |
|---|---|---|
| Nature | Discovery — messy, exploratory, captures "what we learned" | Formalization — clean, structured, ready for consumption |
| Stability | Changes during discovery | Stabilizes early |
| Consumers | Humans (rationale, audit trail) | Agents + code generation |
| Format | Single .md per journey | BC directory + per-aggregate .md files |

The Event Storming artifact is an **audit trail**: why did we choose these aggregate boundaries? What alternatives did we consider? The Domain Model artifacts are the **structural contracts** consumed by implementation.

---

## Design Principles

### 1. Journey-First Scoping

The skill starts from a **selected Journey**, not a feature list or a proposal document. The Journey defines scope by tracing to Features, User Stories, Personas, and the parent Capability. This ensures the domain model is grounded in real user interaction paths, not abstract capability lists.

### 2. Bounded Context Before Aggregates

A critical addition beyond traditional Event Storming: the skill explicitly frames **Bounded Contexts** (Step 2.5) before diving into aggregate boundaries. BCs are derived from Capability boundaries + language divergence checks, preventing the common mistake of lumping cross-context aggregates together.

### 3. User Story Completeness as Quality Gate

The skill checks User Story coverage before starting extraction (Step 0). AC is the primary source for Events, Commands, and Read Models — without it, extraction degrades to pure interactive elicitation. Three coverage levels: Complete (proceed), Partial (warn), Missing (block).

### 4. Read-Only Operations Are Not Domain Events

Actions like viewing, searching, listing, or querying do not change domain state. The skill explicitly suppresses candidates like `Viewed`, `Searched`, `Listed`, `Queried`, `Fetched` from the event extraction. These belong to the Read Model / query side.

### 5. Adaptive Complexity Path

The skill assesses complexity after extraction (Step 2) and routes accordingly:
- **Fast-Path** (1-2 aggregates, obvious invariants) → single round-trip confirmation
- **Interactive** (3+ aggregates, cross-aggregate policies, implicit business rules) → structured multi-turn elicitation

### 6. AggregateRoot Fields and Behaviors Are Derivable

Rather than asking users to list fields and methods, the skill derives them from tables already produced during discovery:
- **Fields** ← Value Objects, Entity references, Command parameters, Event payloads, AC "Given..." clauses
- **Behaviors** ← Commands (one method per command), Invariants (guard logic), ENUM state transitions

### 7. Domain Model Per-Aggregate, Not Per-File

Each aggregate gets its own `.md` file within a BC directory. This enables independent lifecycle, clean git diffs, and Agent consumption — an Agent reads one aggregate file to understand its implementation scope.

### 8. Bidirectional Traceability

Forward: every Command, Event, Invariant has a `Source` field pointing to its originating US/Journey/Feature. Reverse: a Traceability Index in `context-map.md` aggregates all Source references for lookup by Journey, Feature, or User Story.

### 9. CQRS as First-Class Concept

Write Side (Commands, Events, Aggregates, Repositories) and Read Side (Read Models, query-only Repositories, Criteria VOs) are explicitly separated throughout. `@ReadModel` annotation makes the boundary visible. Read models that project a single aggregate's events live in that aggregate's file; cross-aggregate read models live in the BC README.

### 10. Inherited Interaction Model

Event-storming inherits product-framer's interaction rules:
- One question per turn
- Visual state display before every question
- Calibration every 2-3 discoveries
- Answer drift handling (4 types)
- Language matches user

---

## Scania DDD Concepts Alignment

The skill maps every discovered domain concept to Scania's `com.scania.hero.ddd.concepts` annotation library (MIT, Tao Wu 2022):

### Write Side (Command)

| Annotation | DDD Building Block |
|-----------|-------------------|
| `@AggregateRoot` | Root entity of an aggregate (consistency boundary) |
| `@AggregateIdentifier` | Identity field of the aggregate |
| `@Entity` | Child entity within an aggregate |
| `@ValueObject(Type.ID/VALUE/CRITERIA/ENUM)` | Immutable typed object |
| `@DomainEvent` | Past-tense record of state change |
| `@DomainCommand` | Intent to change domain state |
| `@DomainService(Type.DOMAIN/SPECIFICATION/POLICY)` | Stateless domain operation |
| `@Repository` | Aggregate persistence (load + save) |
| `@Factory` | Complex object creation |
| `@Provider` | External system adapter (ACL) |

### Read Side (Query — CQRS)

| Annotation | DDD Building Block |
|-----------|-------------------|
| `@ReadModel` | Denormalized projection built from events |
| `@Repository` | Query-only interface (find only, no save/delete) |
| `@ValueObject(Type.CRITERIA)` | Query/filter parameters |

### Event Storming → Annotation Mapping

| Event Storming Color | DDD Concept | Annotation |
|---------------------|-------------|-----------|
| 🟠 Orange | Domain Event | `@DomainEvent` |
| 🔵 Blue | Command | `@DomainCommand` |
| 🟡 Yellow | Aggregate | `@AggregateRoot` + `@AggregateIdentifier` |
| 🟣 Lilac | Policy | `@DomainService(Type.POLICY)` |
| 🩷 Pink | External System | `@Provider` |
| 🟢 Green | Read Model | `@ReadModel` + `@Repository` (query-only) |

---

## Key Design Decisions

### Why Journey-First, Not Feature-First

Features are abstract scope boundaries. Journeys describe concrete user interaction paths with pain points, system touchpoints, and emotional context. Starting from a Journey ensures the domain model is grounded in real behavior — the same principle as journey-designer's "Problems Come from Behaviors, Not Imagination."

### Why Bounded Context Framing Is a Separate Step

Traditional Event Storming discovers aggregates first, then groups them into BCs post-hoc. This risks early decisions based on incomplete context. The skill frames BCs first (from Capability boundaries + language divergence), then discovers aggregates *within* each BC. This top-down framing prevents cross-context pollution.

### Why Per-Aggregate Files

A single monolithic `domain-model.md` grows unwieldy for complex domains. Per-aggregate files enable:
- Independent lifecycle (an aggregate can evolve without touching others)
- Clean git diffs (reviewable per-aggregate)
- Agent consumption (an Agent reads one file to start implementation)
- BC-level overview in README.md (PlantUML domain overview, cross-aggregate services)

### Why Domain Services Split Between Aggregate and BC Level

Aggregate-scoped services (Specifications, single-aggregate Policies) live in the aggregate's own `.md` file. Cross-aggregate services (Policies spanning multiple aggregates) live in the BC `README.md`. This follows the DDD principle that aggregate-internal concerns stay internal.

### Why PlantUML for Domain Overview, Mermaid for Context Map

| Diagram | Tool | Reason |
|---------|------|--------|
| Domain Overview (per-BC) | PlantUML | Class diagram with stereotypes, aggregate packages, relationships — PlantUML handles class diagrams better |
| Context Map (cross-BC) | Mermaid | Component/graph diagram — Mermaid renders natively on GitHub/GitLab, no external tools needed |

### Why Traceability Index in context-map.md

Forward traceability (Source fields in aggregate files) is scattered across many files. The Traceability Index aggregates all Source references into three reverse-lookup tables (By Journey, By Feature, By User Story) in one place. This enables impact analysis: "If Journey J3 changes, which domain concepts are affected?"

---

## Workflow Overview

```
Step 0:   Load Context (select journey, derive scope, US completeness check)
Step 1:   Autonomous Extraction (events, commands, read models, external systems)
Step 2:   Quick Confirmation (present extraction, assess complexity)
Step 2.5: Bounded Context Framing (identify BCs from capabilities + language divergence)
Step 3a:  Fast-Path Confirmation (simple domains: 1-2 aggregates)
Step 3b:  Interactive Elicitation (complex domains: 3+ aggregates)
Step 4:   Domain Model Formalization (annotations, fields/behaviors, PlantUML, traceability)
Step 5:   Final Review + Save Files
```

See `SKILL.md` for detailed step-by-step instructions.

---

## Output Specification

```
product-management/domain/
  context-map.md                           # Cross-BC context map + Mermaid + traceability index
  event-storming-{journey-slug}.md         # Discovery audit trail (per journey)
  {bc-slug}/                               # One directory per bounded context
    README.md                              # BC overview: UL, cross-aggregate services,
                                           #   cross-aggregate read models, PlantUML domain overview,
                                           #   BC-scoped context map, hotspot resolutions
    aggregate-{aggregate-slug}.md          # Per-aggregate: root entity (fields + behaviors),
                                           #   entities, VOs, commands, events, services,
                                           #   repository, factory, provider, read models
```

File naming: lowercase kebab-case English slugs (e.g., `aggregate-vehicle.md`, `aggregate-geofence.md`).

---

## Quality Gates

### Event Storming Completeness

| Check | Pass Criteria |
|-------|--------------|
| Event coverage | Every US AC maps to ≥1 domain event |
| Command coverage | Every "I want to..." clause maps to ≥1 command |
| Actor coverage | Every command has an identified actor |
| Policy coverage | Every reactive Journey pain point has a policy |
| External system coverage | Every Journey system touchpoint has an external system entry |
| No orphans | No events without commands; no commands without events |

### Domain Model Structural Validity

| Check | Pass Criteria |
|-------|--------------|
| Aggregate identity | Every aggregate has one `@AggregateIdentifier` using `@ValueObject(Type.ID)` |
| Invariant presence | Every aggregate has ≥1 invariant |
| Command preconditions | Every command has explicit preconditions (or "none") |
| Event payloads | Every event has defined payload using VO types |
| Ubiquitous Language | Every term in tables appears in UL section |
| Traceability | Every concept traces to US, Feature, or Journey Pain Point |
| Size constraint | No aggregate > ~10 direct concepts |
| CQRS separation | Read Models separate from Aggregates; no save() on read-side repos |

---

## Methodology Concept-to-Source Traceability

| Skill Concept | Origin | Key Source |
|---|---|---|
| Event Storming workshop | Alberto Brandolini (2013) | Domain discovery via colored sticky notes; adapted for AI-assisted context |
| Aggregates as consistency boundaries | Eric Evans, DDD (2003) | Transactional boundary enforcing invariants atomically |
| Bounded Contexts | Eric Evans, DDD (2003) | Linguistic boundary; same term can mean different things in different BCs |
| Context Map (ACL, OHS, Conformist, Partnership) | Eric Evans, DDD (2003) | Relationships between bounded contexts |
| CQRS (Command Query Responsibility Segregation) | Greg Young (2010) | Write Side (commands/events/aggregates) separated from Read Side (projections/queries) |
| Ubiquitous Language | Eric Evans, DDD (2003) | Domain terms used consistently across code, docs, and conversation |
| `ddd-concepts` annotation library | Tao Wu (2022) | `com.scania.hero.ddd.concepts` — MIT licensed Java annotations for DDD building blocks |
| User Input Classification | step1-ddd-domain-modeling.md | Low/Medium/High classification driving adaptive interaction flow |
| Progressive elicitation | Beyer & Holtzblatt (1998) | Contextual Inquiry — discover in context, not through abstract questioning |
| Answer drift handling | Harness product-framer | Four-type classification inherited from upstream skill |

---

## Resources

| File | Purpose |
|------|---------|
| `SKILL.md` | Complete step-by-step execution instructions |
| `references/interaction-protocol.md` | Detailed Steps A-E interaction protocol with examples |
| `references/scania-ddd-concepts.md` | Full annotation catalog, subtypes, decision guides, Event Storming mapping |
| `references/enrichment-protocol.md` | Domain model formalization rules, quality gates |
| `references/state-display-templates.md` | ASCII state panels for all workflow steps |
| `templates/event-storming-template.md` | Output template for event-storming.md |
| `templates/context-map-template.md` | Output template for context-map.md (cross-BC, Mermaid, traceability index) |
| `templates/bc-index-template.md` | Output template for BC README.md (UL, PlantUML domain overview) |
| `templates/aggregate-template.md` | Output template for per-aggregate .md files |
