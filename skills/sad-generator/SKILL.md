---
name: sad-generator
description: "Generate a Software Architecture Document (SAD) from Harness chain outputs. Uses arc42 structure with C4 diagrams. Synthesizes existing product-management artifacts (domain models, journeys, event storming) into a unified architecture document. Triggers: 'generate SAD', 'architecture document', 'create SAD', 'software architecture document', 'generate architecture', 'write SAD', 'arc42 document', 'document the architecture'."
---

# SAD Generator

Generate a Software Architecture Document from Harness chain outputs. This skill synthesizes existing `product-management/` artifacts into a single structured architecture document following the arc42 template with C4 model diagrams.

Unlike upstream Harness skills (which are elicitation-heavy), this skill is **synthesis-heavy** — it derives architecture views from structured files, with minimal targeted elicitation only for genuine gaps (deployment decisions, cross-cutting technology choices).

**Scope:** All upstream Harness outputs → SAD (single file). Upstream: `product-framer`, `journey-designer`, `user-story`, `event-storming`. Downstream: implementation teams + development Agents.

---

## Output Specification

**Single file output:**

```
product-management/
  architecture/
    sad.md                    # Complete Software Architecture Document
```

**File naming:** Fixed name `sad.md`. The file contains all arc42 sections with embedded diagrams (PlantUML or Mermaid code blocks).

**Ownership rule:** This skill writes ONLY to `architecture/`. It never modifies upstream files. When synthesis reveals upstream gaps (missing domain model, incomplete journey), surface the issue and recommend running the appropriate upstream skill first.

---

## Prerequisites

### Required Upstream Artifacts (Minimum)

The skill REQUIRES at minimum:
- `product-management/vision.md` — Product vision with constraints
- `product-management/capabilities/C*.md` OR `product-management/capabilities.yaml` — At least one capability defined

Without these, tell the user to run the Harness pipeline first (product-framer at minimum).

### Recommended (for full generation)

- `product-management/domain/context-map.md` — BC relationships
- `product-management/domain/{bc-slug}/` — At least one modeled BC
- `product-management/journeys/J*.md` — Journey maps
- `product-management/domain/event-storming-*.md` — Event flows

The skill generates what it can from available artifacts. Missing sections get `[TBD]` placeholders with notes on what upstream work is needed.

---

## Interaction Rules

### 1. Synthesis First, Questions Later

Unlike upstream skills that ask 15-30 questions, this skill asks **at most 5 questions total**. The primary workflow is autonomous reading and synthesis. Only ask when:
- A critical architecture decision cannot be inferred from existing files
- Multiple valid interpretations exist and the wrong choice would produce incorrect diagrams

### 2. Provide Options, Not Open Questions

Every elicitation question MUST offer concrete options. No open-ended "how would you like to deploy?" — instead, offer specific choices with implications.

### 3. State Display Before Questions

Before any question, show a brief state panel indicating:
- What has been successfully derived
- What is missing and why a question is needed

See `references/state-display-templates.md`.

### 4. Diagram Types Are Prescribed by Workflow

Each workflow step specifies exactly which diagrams to generate (see Step 1 and `references/diagram-conventions.md`). Do not generate diagram types not listed in the workflow — specifically, no UML Use Case diagrams.

---

## Workflow

```
Step 0:  Inventory — Scan product-management/, classify available artifacts
Step 1:  Auto-Derive — Generate all derivable sections autonomously
Step 2:  Gap Assessment + Elicit — Identify critical gaps, ask 1-3 targeted questions
Step 3:  Generate SAD — Assemble complete document with diagrams
Step 4:  Review + Save — Present for confirmation, save to file
```

---

## Step 0: Inventory

### Scan Available Artifacts

Read `product-management/` directory and classify findings:

| Category | File Pattern | Maps To |
|----------|-------------|---------|
| Vision | `vision.md` | §1, §2, §9 |
| Personas | `personas/P*.md` | §1 stakeholders |
| Capabilities | `capabilities/C*.md` | §3 scope |
| Capabilities Index | `capabilities.yaml` | §3 scope overview |
| Journeys | `journeys/J*.md` | §6 runtime scenarios |
| Features | `features/F*.md` | §5 functional mapping |
| User Stories | `user-stories/US*.md` | §6 scenario detail |
| Context Map | `domain/context-map.md` | §3, §5 BC structure |
| Event Storming | `domain/event-storming-*.md` | §6 command/event flows |
| BC Directories | `domain/{bc-slug}/README.md` | §5, §8, §11 glossary |
| Aggregates | `domain/{bc-slug}/aggregate-*.md` | §5 component detail |

### Generate Coverage Report

Produce an internal coverage assessment:

```
┌─────────────────────────────────────────┐
│  SAD Generation — Inventory Complete    │
├─────────────────────────────────────────┤
│  Available:                             │
│    ✓ Vision (constraints, NFR)          │
│    ✓ 5 Personas                         │
│    ✓ 4 Capabilities (C1-C4)            │
│    ✓ 2 Journeys                         │
│    ✓ 1 BC modeled (offering-management) │
│    ✓ Event Storming for J1              │
│                                         │
│  Derivable Sections:                    │
│    §1 Introduction     ✓ auto           │
│    §2 Constraints      ✓ auto           │
│    §3 Context          ✓ auto           │
│    §4 Solution Strategy ⚠ need elicit   │
│    §5 Building Blocks  ✓ auto           │
│    §6 Runtime          ✓ auto           │
│    §7 Deployment       ⚠ need elicit    │
│    §8 Cross-cutting    ◐ partial        │
│    §9 Quality          ✓ auto           │
│    §10 Risks           ✓ auto           │
│    §11 Glossary        ✓ auto           │
│                                         │
│  Questions needed: ~2-3                 │
└─────────────────────────────────────────┘
```

---

## Step 1: Auto-Derive

Generate content for each section using derivation rules. See `references/derivation-rules.md` for complete mapping logic.

### §1 Introduction & Goals

**Source:** `vision.md` + `personas/P*.md`

- Extract Business Problems → "Business Goals"
- Extract Core Values → "Architecture Goals"  
- Extract Persona summaries → "Stakeholders" table
- Classify personas: User roles = functional stakeholders, Review roles = quality stakeholders

### §2 Architecture Constraints

**Source:** `vision.md`

- Vision.Constraints → "Technical Constraints"
- Vision.Non-Goals → "Scope Exclusions"
- Vision.Assumptions → "Key Assumptions"
- Data sovereignty requirements → "Regulatory Constraints"

### §3 Context & Scope

**Source:** `capabilities.yaml` + `domain/context-map.md` + `personas/P*.md`

Generate a **C4 System Context diagram** (Mermaid):
- System boundary = product name from Vision
- Actors = Personas (both human and system)
- External systems = from context-map.md External Systems section or Provider entries in aggregate files
- Relationships = from Journey touchpoints

### §5 Building Block View

**Source:** `domain/context-map.md` + `domain/{bc-slug}/` directories

Generate **two levels**:

**Level 1 — C4 Container diagram:**
- Multiple BCs may share one deployable service (container), with code isolation via sub-projects/modules
- Each container = one deployable service (may contain multiple BCs as sub-projects)
- Each BC within a container = a code-isolated sub-project/module (not a separate deployable)
- API Gateway = entry point container
- Message broker = infrastructure container (if event-driven patterns found)
- External systems = external containers

When generating: group BCs into containers based on context-map relationships (BCs with tight coupling / Shared Kernel / Partnership tend to co-deploy). If grouping is ambiguous, show each BC as its own container and note that co-deployment is possible.

**Level 2 — C4 Component diagram (per container with domain model):**
- Each BC within the container = a sub-project boundary (shown as component group)
- Each Aggregate = one component within its BC group
- Cross-aggregate Domain Services (from BC README) = components
- Relationships from event flows, command issuance, and snapshot references

For BCs without domain models: generate Level 1 entry only, mark internals as `[TBD - run event-storming for this BC]`.

**Level 2 — Vehicle-Side Component diagram (if vehicle-facing BCs exist):**

Trigger: If any Capability involves cloud-vehicle interaction (e.g., configuration delivery, OTA, remote control, status reporting), generate a vehicle-side component view.

Detection signals:
- Capability descriptions mentioning vehicle/device/T-Box/ECU
- Provider entries with vehicle/IoT protocols (MQTT, AMQP, CoAP, etc.)
- Journey touchpoints involving vehicle-side systems

Depth (auto-configured):
- If a vehicle-facing BC has a complete domain model → **Component-level**: show T-Box, Agent, ECU as separate components with inter-component protocols (CAN/DoIP)
- If vehicle-facing BC has only Capability definition → **High-level**: show vehicle as a single Container (T-Box) with cloud↔vehicle interface only

Components (when expanded):
- T-Box Gateway (connectivity, protocol translation)
- Configuration Agent / OTA Agent (cloud command interpretation, ECU orchestration)
- Target ECU (feature activation endpoint)
- Message Broker (cloud-vehicle communication bridge, shown on cloud side)

Relationships:
- Downlink: cloud service → Broker → T-Box → Agent → ECU
- Uplink: ECU → Agent → T-Box → Broker → cloud service
- Protocols: determined by Q6 elicitation (cloud↔vehicle) + CAN/DoIP/UDS (intra-vehicle)

### §6 Runtime View

**Source:** `domain/event-storming-*.md` + `journeys/J*.md` + aggregate files

Generate **sequence diagrams** for architecturally significant flows:

1. Select flows by priority:
   - Cloud-vehicle interaction flows (highest priority for IoT/vehicle systems)
   - Cross-BC command chains
   - Event-driven policy reactions
   - External system integration flows
   - Flows with complex branching (from Policy logic)

2. For each selected flow, generate Mermaid sequence diagram:
   - Participants = Actors, Aggregates, Policies, Providers, External Systems, **Vehicle Components (T-Box, Agent, ECU)**
   - Messages = Commands, Events, **Vehicle protocols (per Q6 choice, e.g. MQTT topics / CAN frames)**
   - Notes = Invariant checks, preconditions

3. Maximum 5 sequence diagrams (avoid noise). If more flows exist, select the most architecturally significant ones.

### §8 Cross-cutting Concepts

**Source:** Domain model patterns + context-map relationships

Auto-derive from detected patterns:
- **Anti-Corruption Layer (ACL):** If context-map shows ACL relationships → document adapter pattern
- **CQRS:** If aggregate files have Read Model sections → document read/write separation
- **Event-Driven:** If Policies react to events across aggregates → document event flow pattern
- **Domain Events:** Document event naming conventions, payload structure from aggregate templates
- **Error Handling:** If Provider entries have Failure Strategy → document resilience patterns

#### §8.1 API Design (Architecturally Significant)

**Source:** `capabilities.yaml` + `domain/context-map.md` + aggregate Provider entries

Only document architecture-level decisions — NOT full API contracts (those belong in downstream OpenAPI specs).

Generate:
- **API Style:** REST / gRPC / GraphQL (from Q1 elicitation or default)
- **Authentication & Authorization:** Bearer token / OAuth2 / mTLS — from Q1 elicitation
- **Resource Naming Conventions:** Derive from BC aggregate names (e.g., `AggregateX` → `/aggregate-xs`)
- **Key Endpoint Overview Table:** One row per BC, listing primary resource paths and HTTP verbs

| BC | Resource | Verbs | Description |
|----|----------|-------|-------------|
| {bc-name} | `/{resource}` | GET, POST, PUT | {derived from aggregate commands} |

- **Error Response Convention:** Standard error envelope structure (derive from Error Handling patterns if present)
- **Rate Limiting / Throttling:** If NFR constraints mention throughput → document strategy

Boundary: Do NOT generate request/response body schemas, field-level validation rules, or individual status code mappings. Reference downstream OpenAPI spec location instead.

#### §8.2 Database Design (Architecturally Significant)

**Source:** `domain/{bc-slug}/` aggregate files + context-map.md + Q5 elicitation

Only document architecture-level decisions — NOT full ER diagrams or DDL (those belong in downstream data model docs).

Generate:
- **Storage Technology:** Per-service database choice (from Q5 elicitation)
- **Database Isolation Strategy:** Shared database instance per deployable service, with each BC owning a separate DB schema for logical isolation (e.g., `bc_offering.`, `bc_vehicle.`). NOT separate database instances per BC.
- **Data Ownership Table:**

| BC | Schema | Primary Aggregates | Estimated Scale |
|----|--------|-------------------|-----------------|
| {bc-name} | {schema_name} | {aggregate list} | {from NFR if available} |

- **Partitioning / Sharding Strategy:** If NFR constraints mention data volume or geo-distribution → document strategy, otherwise mark TBD
- **Read/Write Separation:** If CQRS pattern detected → document read replica or separate read store
- **Data Sovereignty:** If Vision.Constraints mention data residency → document per-region storage rules
- **Migration Strategy:** If system is brownfield (existing data mentioned) → note migration approach, otherwise omit

Boundary: Do NOT generate table schemas, column definitions, indexes, or migration scripts. Reference downstream data model location instead.

### §9 Quality Requirements

**Source:** `vision.md` Constraints + Review Personas (P5-type)

- Extract NFR from Vision.Constraints (latency, availability, etc.)
- Extract security/compliance requirements from review role personas
- Format as quality attribute scenarios (Stimulus → Response → Measure)

### §10 Risks & Technical Debt

**Source:** BC README.md "Hotspot Resolutions" + context-map.md open questions

- Domain hotspots that lack resolution → architectural risks
- BCs without domain models → technical debt (insufficient design)
- External system dependencies without fallback → integration risks

### §11 Glossary

**Source:** All BC README.md "Ubiquitous Language" sections

- Merge UL terms from all modeled BCs
- Add capability-level terms from capabilities if not already covered
- Sort alphabetically

---

## Step 2: Gap Assessment + Elicit

After Step 1 derivation, assess what CANNOT be derived:

### Critical Gaps (MUST elicit)

These affect diagram accuracy — wrong answers produce wrong architecture:

**Q1: API Gateway & Authentication**

Only ask if no gateway/auth information is found in existing artifacts.

See `references/elicitation-questions.md` for exact question format.

Impacts: §3 Context diagram (gateway as entry point), §5 Container diagram (gateway component), §6 Sequence diagrams (auth flow), §8 Cross-cutting (security pattern)

**Q2: Inter-service Communication**

Only ask if event-driven patterns exist but middleware choice is unclear.

Impacts: §5 Container diagram (broker component), §6 Sequence diagrams (async vs sync), §7 Deployment (broker node)

**Q3: Deployment Topology**

Only ask if completely unknown. Default assumption: Spring Cloud + K8s.

Impacts: §7 Deployment diagram

**Q4: Service Discovery**

Always ask — no default assumption. Offer concrete options:
- Consul (service mesh with health checks, suitable for hybrid/multi-cloud)
- K8s native DNS (simple, built-in, suitable for pure K8s environments)
- Nacos (popular in Spring Cloud Alibaba ecosystems)

Impacts: §4 Solution Strategy, §5 Container diagram (infrastructure component), §7 Deployment (discovery node)

**Q5: Database (per-service)**

Always ask — no default assumption. Offer concrete options:
- PostgreSQL (ACID-compliant, rich feature set, JSON support)
- Other (user specifies)

Impacts: §4 Solution Strategy, §5 Container diagram (data store per service), §7 Deployment (database nodes)

**Q6: Cloud-Vehicle Communication Protocol**

Only ask if vehicle-facing BCs are detected (see §5 Vehicle-Side Component detection signals). Offer concrete options:
- MQTT/TLS + Protobuf (lightweight pub/sub, binary payload, low bandwidth)
- MQTT/TLS + JSON (lightweight pub/sub, human-readable payload)
- AMQP/TLS + Protobuf (richer routing, binary payload)
- CoAP/DTLS (UDP-based, constrained devices, very low power)
- Other (user specifies)

Impacts: §4 Solution Strategy, §5 Vehicle-Side Component diagram (broker type, protocol labels), §6 Sequence diagrams (message format in vehicle flows)

### Non-Critical Gaps (Mark TBD)

These don't affect overall architecture correctness:
- Specific middleware versions
- Network CIDR ranges
- Monitoring/observability stack
- CI/CD pipeline details

Mark as `[TBD]` with brief note on what decision is pending.

### Elicitation Protocol

1. Show state panel (what was derived, what's missing)
2. Ask questions using the Question tool with concrete options
3. Maximum 5 questions total — if still unclear, mark TBD
4. Never ask follow-up questions on the same topic — take the answer and move on

---

## Step 3: Generate SAD

### Assembly Rules

1. Use the template from `templates/sad-template.md` as the structural skeleton
2. Fill each section with derived + elicited content
3. Embed diagrams as fenced code blocks:
   - Mermaid: ` ```mermaid ... ``` `
   - PlantUML: ` ```plantuml ... ``` `
4. Use relative paths when referencing upstream artifacts: `../journeys/J1-*.md`
5. Mark incomplete sections with `> [TBD] ...` blockquote
6. Add generation metadata at the top:

```markdown
<!-- Generated by: sad-generator | Date: YYYY-MM-DD | Source: product-management/ -->
```

### Diagram Selection Rules

| Section | Diagram Type | Tool | When to Generate |
|---------|-------------|------|-----------------|
| §3 Context | C4 System Context | PlantUML C4 | Always |
| §5 Level 1 | C4 Container | PlantUML C4 | Always |
| §5 Level 2 | C4 Component | PlantUML C4 | Per modeled BC |
| §6 Runtime | Sequence diagram | Mermaid | Top 3-5 flows |
| §7 Deployment | Deployment diagram | PlantUML | When deployment info available |

See `references/diagram-conventions.md` for syntax and styling rules.

### Quality Checks Before Saving

Before presenting the document:
1. Verify all cross-references to upstream files use valid relative paths
2. Verify every BC mentioned in §5 appears in §3 Context diagram
3. Verify sequence diagram participants match §5 components
4. Verify glossary terms appear in the document body
5. Ensure no section is completely empty (minimum: TBD marker with explanation)

---

## Step 4: Review + Save

### Present for Review

Show a summary of what was generated:

```
┌─────────────────────────────────────────┐
│  SAD Generated — Review                 │
├─────────────────────────────────────────┤
│  Sections complete:    9/11             │
│  Sections with TBD:   2 (§4, §7)       │
│  Diagrams generated:  7                 │
│    - 1x System Context (Mermaid)        │
│    - 1x Container (Mermaid)             │
│    - 1x Component per BC (PlantUML)     │
│    - 3x Sequence (Mermaid)              │
│    - 1x Deployment (PlantUML)           │
│  Glossary terms:      15                │
│  Risks identified:    3                 │
│                                         │
│  Ready to save to:                      │
│  product-management/architecture/sad.md │
└─────────────────────────────────────────┘
```

Then present the full document content for user review.

### Iterate if Needed

If user requests changes:
- Diagram adjustments → regenerate specific diagrams
- Section rewording → edit in place
- Missing information → add TBD or ask one clarifying question
- Structural changes → regenerate affected sections

### Save

Create `product-management/architecture/` directory if needed and write `sad.md`.

---

## Default Technology Assumptions

When no explicit technology information is found in upstream artifacts, use these defaults:

| Concern | Default Assumption | Override Trigger |
|---------|-------------------|-----------------|
| Service framework | Spring Cloud (Spring Boot) | User specifies otherwise |
| Deployment platform | Kubernetes | User specifies otherwise |
| Service communication | REST (sync) + Event Bus (async) | User specifies otherwise |
| Service discovery | — (must elicit) | Always ask — see Q4 below |
| Configuration | K8s ConfigMap + Secret | Apollo/Nacos Config mentioned |
| API style | RESTful JSON | GraphQL/gRPC mentioned |
| Database | — (must elicit) | Always ask — see Q5 below |
| Cloud-vehicle protocol | — (must elicit) | Ask if vehicle-facing BCs detected — see Q6 below |

These defaults appear in §4 Solution Strategy. If the user confirms or provides alternatives during elicitation, update accordingly.

---

## Boundary Discipline

### This Skill Writes

- `product-management/architecture/sad.md`

### This Skill Reads (Never Modifies)

- `product-management/vision.md`
- `product-management/personas/P*.md`
- `product-management/capabilities.yaml`
- `product-management/capabilities/C*.md`
- `product-management/journeys/J*.md`
- `product-management/features/F*.md`
- `product-management/user-stories/US*.md`
- `product-management/domain/**`

### Upstream Issue Escalation

If synthesis reveals problems in upstream artifacts:
- Missing domain model for a critical BC → Recommend running `event-storming`
- Incomplete journey (no system touchpoints) → Recommend running `journey-designer`
- No vision file → BLOCK: recommend running `product-framer`

Never attempt to fix upstream artifacts. Surface the issue and let the user decide.
