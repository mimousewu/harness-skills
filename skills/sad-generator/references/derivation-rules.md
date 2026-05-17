# Derivation Rules

How each SAD section is derived from upstream Harness artifacts. This file defines the exact mapping logic the skill uses in Step 1 (Auto-Derive).

---

## §1 Introduction & Goals

### Source Files
- `vision.md` — Business Problems, Core Values
- `personas/P*.md` — All persona files

### Derivation Logic

**Business Goals:**
Extract from `vision.md` → "Business Problems" section. Reframe as positive goals:
- Problem: "无法快速了解..." → Goal: "Enable rapid visibility into..."
- Keep 3-5 top-level goals

**Architecture Goals:**
Extract from `vision.md` → "Core Values" section. These become quality-oriented architecture goals:
- Core Value: "数据延迟不超过30秒" → Architecture Goal: "Near-real-time data propagation (≤30s)"

**Stakeholders Table:**

| Role | Source | Maps To |
|------|--------|---------|
| User personas (type: 用户角色) | `personas/P*.md` | Functional stakeholders |
| Review personas (type: 审查角色) | `personas/P*.md` | Quality/governance stakeholders |
| External systems | `domain/context-map.md` External Systems | System stakeholders |

Generate table:
```markdown
| Stakeholder | Role | Key Concerns |
|-------------|------|-------------|
| {P-ID} {Name} | {type} | {goals summary from persona file} |
```

---

## §2 Architecture Constraints

### Source Files
- `vision.md` — Constraints, Non-Goals, Assumptions

### Derivation Logic

**Technical Constraints:**
Direct extract from `vision.md` → "Constraints" section. Categorize:
- Data constraints (sovereignty, retention, encryption)
- Integration constraints (protocols, APIs, compatibility)
- Performance constraints (latency, throughput)
- Platform constraints (language, framework mandates)

**Scope Exclusions:**
Direct extract from `vision.md` → "Non-Goals" section. Format as explicit "The system will NOT..."

**Key Assumptions:**
Direct extract from `vision.md` → "Assumptions" section. Format as "This architecture assumes..."

---

## §3 Context & Scope

### Source Files
- `vision.md` — Product name, one-line description
- `capabilities.yaml` OR `capabilities/C*.md` — System functional scope
- `domain/context-map.md` — External systems, BC relationships
- `personas/P*.md` — Actors
- `domain/{bc-slug}/aggregate-*.md` — Provider entries (external systems)

### Derivation Logic

**System Boundary:**
- System name = from `vision.md` title
- System description = from `vision.md` one-liner or Core Values summary
- Functional scope = Capability names from `capabilities.yaml`

**Actors (left side of context diagram):**
- Human actors = User role personas (P1-P{n} where type = 用户角色)
- System actors = Non-human actors from User Stories ("As the {system}...")

**External Systems (right side of context diagram):**
Priority order for discovery:
1. `domain/context-map.md` → External Systems table
2. Aggregate files → `## Provider` sections (external system name)
3. Journey files → System Touchpoints (systems that are NOT our product)

**Relationships:**
- Actor → System: derived from Capability "Service Personas" field
- System → External: derived from Provider entries (method names indicate interaction type)

**Generate C4 Context Diagram (PlantUML C4):**
```
@startuml context
!include <C4/C4_Context>

LAYOUT_TOP_DOWN()

Person(actor1, "{P-ID} {Name}", "{brief role}")
System(system, "{Product Name}", "{description}")
System_Ext(ext1, "{External System}", "{what it provides}")

Rel_Down(actor1, system, "{capability verb}", "HTTPS")
Rel_Right(system, ext1, "{provider method purpose}", "{protocol}")

SHOW_LEGEND()
@enduml
```

---

## §4 Solution Strategy

### Source Files
- Elicitation results (API Gateway, Auth, Messaging)
- Default technology assumptions (if no elicitation needed)

### Derivation Logic

This section is primarily filled from elicitation (Step 2) or defaults. Structure:

| Decision | Choice | Rationale |
|----------|--------|-----------|
| Service framework | Spring Cloud (Spring Boot) | {default or elicited} |
| API Gateway | {elicited} | {user's stated reason or "team convention"} |
| Authentication | {elicited} | {user's stated reason} |
| Inter-service sync | REST / Feign | {inferred from domain model patterns} |
| Inter-service async | {elicited: Kafka/etc.} | {event-driven patterns found in domain model} |
| Deployment | Kubernetes | {default or elicited} |
| Data storage | {TBD or inferred} | Per-service database (microservice principle) |

**Inference rules for communication:**
- If domain model has `@DomainEvent` + Policies reacting to events → async messaging confirmed
- If domain model has `@Provider` with REST-style methods → sync REST confirmed
- If context-map shows ACL relationship → adapter pattern confirmed

---

## §5 Building Block View

### Source Files
- `domain/context-map.md` — BC list, relationships
- `domain/{bc-slug}/README.md` — BC overview, cross-aggregate services
- `domain/{bc-slug}/aggregate-*.md` — Aggregate list + event/command relationships
- `capabilities/C*.md` — For BCs without domain model

### Derivation Logic

**Level 1 — C4 Container Diagram:**

Containers are derived from:
1. Each BC in context-map = 1 service container
2. API Gateway = 1 infrastructure container (from elicitation)
3. Message Broker = 1 infrastructure container (if event-driven patterns detected)
4. Each external system = 1 external container

For BCs WITHOUT domain model directories:
- Still show as a container in the diagram
- Label as "{Capability Name} Service [planned]"
- Derive minimal info from the Capability file's Scope section

**Level 2 — C4 Component Diagram (per modeled BC):**

Components are limited to two types:
- Each `aggregate-*.md` file = 1 component (Aggregate)
- Cross-aggregate Domain Services (from BC README) = 1 component each

Relationships derived from:
- Event flows: Aggregate A raises Event → Cross-Aggregate Service listens = A → Service
- Service commands: Cross-Aggregate Service issues Command → Aggregate B = Service → B
- Snapshot references: Aggregate A uses snapshot from Aggregate B = A ..> B (weak dependency)

The diagram answers ONE question: "How do aggregates collaborate within this service?"

**Grouping rule for large BCs:**
- If aggregate count ≤ 5: one component per Aggregate (1:1 mapping)
- If aggregate count > 5: group related Aggregates by functional subdomain (shared event subscribers or co-orchestrated by the same Policy). Component name = subdomain name.

**Domain Overview (BC README PlantUML) is NOT the Component diagram.**
The existing Domain Overview shows class-level internal structure (C4 Level 4). The Component diagram shows inter-aggregate collaboration (C4 Level 3). They are complementary, not interchangeable.

---

## §6 Runtime View

### Source Files
- `domain/event-storming-*.md` — Command→Event→Policy chains
- `domain/{bc-slug}/aggregate-*.md` — Command preconditions, event subscribers
- `journeys/J*.md` — Cross-system interaction paths

### Derivation Logic

**Flow Selection (maximum 5 diagrams):**

Score each potential flow:
| Criterion | Score |
|-----------|-------|
| Crosses BC boundary | +3 |
| Involves external system (Provider) | +2 |
| Has Policy with branching logic | +2 |
| Involves async event propagation | +1 |
| Is critical path (from Journey "Fatal" pain point) | +2 |
| Single aggregate, single command, no branching | -5 (skip) |

Select top 3-5 flows by score.

**Sequence Diagram Generation (Mermaid):**

For each selected flow:

1. **Participants** — derive from the flow:
   - Actor (from Command's actor/source field)
   - Aggregates involved (from Command target + Event subscribers)
   - Policies (from Event → Policy mappings)
   - Providers (from Command preconditions requiring external data)
   - External Systems (from Provider's external system field)

2. **Messages** — derive from event storming tables:
   - Sync calls: Actor → Aggregate (Command name)
   - Internal: Aggregate checks invariants (note)
   - Events: Aggregate → Event (emit)
   - Async: Event → Policy (react)
   - Policy actions: Policy → Aggregate (new Command)
   - External: Aggregate → Provider → External System

3. **Template:**
```
sequenceDiagram
  actor User as {Persona}
  participant GW as API Gateway
  participant Agg1 as {Aggregate1}
  participant Agg2 as {Aggregate2}
  participant Ext as {External System}
  
  User->>GW: {HTTP verb} {resource}
  GW->>Agg1: {CommandName}
  Note over Agg1: Check: {invariant}
  Agg1->>Agg1: {EventName}
  Agg1-->>Agg2: Event: {EventName}
  Note over Agg2: Policy: {PolicyName}
  Agg2->>Ext: {Provider.method()}
  Ext-->>Agg2: {response}
```

**Journey-based flows:**
If event-storming is unavailable for some journeys, generate simplified activity diagrams from Journey Phase system touchpoints (showing cross-system handoffs without aggregate detail).

---

## §7 Deployment View

### Source Files
- Elicitation results (deployment platform, topology)
- Default assumptions (Spring Cloud + K8s)
- `domain/context-map.md` — External system list

### Derivation Logic

**Generate PlantUML Deployment Diagram:**

Nodes:
- K8s Cluster = deployment node
  - Namespace per BC = logical grouping
  - Pod per service (from §5 containers)
- External systems = external nodes
- API Gateway = edge node
- Message Broker = infrastructure node

Relationships:
- Gateway → Services (HTTP/gRPC)
- Services → Message Broker (if async patterns detected)
- Services → External Systems (from Provider relationships)
- Services → Databases (one per service, type TBD unless specified)

If deployment info is minimal (only defaults), generate a simplified diagram showing logical deployment without infrastructure detail (no replica counts, no CIDR, no monitoring).

---

## §8 Cross-cutting Concepts

### Source Files
- `domain/context-map.md` — Relationship types (ACL, OHS, Conformist)
- `domain/{bc-slug}/aggregate-*.md` — Patterns found in structure
- Elicitation results (auth mechanism)

### Derivation Logic

**Pattern Detection:**

| Pattern | Detection Signal | Document As |
|---------|-----------------|-------------|
| Anti-Corruption Layer | Context-map shows "ACL" relationship | Adapter pattern between BCs |
| CQRS | Aggregate files have "Read Models" section | Separate read/write models |
| Event-Driven | Policies react to events across aggregates | Async event propagation |
| Domain Events | `@DomainEvent` annotations in aggregate files | Event naming, payload conventions |
| Provider/ACL | Provider sections in aggregate files | External system integration strategy |
| Specification Pattern | `@DomainService(Type.SPECIFICATION)` entries | Business rule encapsulation |

**Authentication & Security:**
From elicitation (Q1): document OAuth2.0/JWT flow, token propagation strategy, Gateway vs per-service auth.

**Error Handling:**
From Provider "Failure Strategy" fields: document resilience patterns (retry, circuit breaker, fallback).

---

## §9 Quality Requirements

### Source Files
- `vision.md` — Constraints (NFR embedded in constraints)
- `personas/P*.md` — Review role personas (security, compliance concerns)

### Derivation Logic

**Quality Attribute Scenarios:**

From Vision.Constraints, extract measurable requirements:
- "数据延迟不超过30秒" → Latency scenario
- "数据主权" → Data residency scenario
- "7×24" → Availability scenario

From Review Personas (P5-type), extract quality concerns:
- Security audit persona → Security scenarios
- Compliance persona → Regulatory scenarios

Format as quality attribute table:
```markdown
| Quality Attribute | Scenario | Measure |
|---|---|---|
| Performance | {stimulus} → {response} | {metric} |
| Security | {threat} → {countermeasure} | {verification} |
```

---

## §10 Risks & Technical Debt

### Source Files
- `domain/{bc-slug}/README.md` — Hotspot Resolutions section
- `domain/context-map.md` — Open questions, unresolved relationships
- Inventory coverage report — missing domain models

### Derivation Logic

**Architectural Risks:**
- Domain hotspots without resolution → "Open design decision: {hotspot description}"
- External system dependencies without fallback strategy → "Integration risk: {system name}"
- Single point of failure patterns → "Availability risk: {component}"

**Technical Debt:**
- BCs without domain models → "Design debt: {BC name} lacks formal domain model"
- Capabilities without journeys → "Analysis debt: {Capability} user paths not mapped"
- TBD sections in this SAD → "Documentation debt: {section} pending decision"

---

## §11 Glossary

### Source Files
- `domain/{bc-slug}/README.md` — Ubiquitous Language sections
- `vision.md` — Product-specific terms
- `capabilities/C*.md` — Capability-level terminology

### Derivation Logic

1. Read all BC README files → extract "Ubiquitous Language" tables
2. Merge terms across all BCs (check for conflicts — same term, different meaning = BC boundary signal)
3. Add product-level terms from Vision that aren't already covered
4. Sort alphabetically
5. Format:

```markdown
| Term | Definition | Context (BC) |
|------|-----------|-------------|
| {term} | {definition} | {bc-name} |
```

If a term has different meanings in different BCs, list both with BC context (this is valuable architectural information about BC boundaries).
