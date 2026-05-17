---
name: event-storming
description: Domain discovery through interactive Event Storming elicitation, producing a formalized DDD domain model. Trigger phrases: "event storming", "domain modeling", "model the domain", "discover aggregates", "DDD model", "domain discovery", "design the domain".
---

# Event Storming Skill

> Domain discovery through interactive Event Storming elicitation, producing a formalized DDD domain model aligned with Scania's `ddd-concepts` annotation library.
>
> **Scope:** User Stories + Journeys + Features → Event Storming → Domain Model. Upstream: Harness `user-story` skill. Downstream: implementation.
>
> **Trigger phrases:** "event storming", "domain modeling", "model the domain", "discover aggregates", "DDD model", "domain discovery", "design the domain".

---

## Prerequisites

### Required Upstream Artifacts

The skill REFUSES to start without:
- `product-management/journeys/J*.md` — at least one Journey
- `product-management/features/F*.md` — Feature definitions linked from the Journey

If these don't exist, tell the user to run the Harness pipeline first (product-framer → journey-designer → user-story).

### Entry Point: Selected Journey

The skill starts from a **user-selected journey** (`product-management/journeys/J{n}-*.md`). The journey defines scope:
- **Features** → derived from the journey's pain points (F{n} references in the journey file)
- **User Stories** → linked to those features (`product-management/user-stories/US{n}-*.md`)
- **Personas** → referenced in the journey as actors
- **Capability** → the parent capability the journey maps

If no journey is selected, list available journeys and ask the user to pick one.

---

## Interaction Rules (Inherited from Harness)

These rules are non-negotiable — they define the skill's interaction quality:

1. **One question per turn.** Never ask 2+ questions in the same message.
2. **Visual state display before every question.** Show progress panel so user knows where they are.
3. **Calibrate every 2-3 discoveries.** Summarize understanding and ask "correct?"
4. **Answer drift handling.** User answers rarely match the question. Extract value, park if off-topic, steer back.
5. **Never dismiss user input.** Tangents reveal what they care about most.
6. **Language matches user.** If user speaks Chinese, respond in Chinese. If English, English.

---

## Workflow

```
Step 0:   Load Context (select journey, derive features/stories/personas)
Step 1:   Autonomous Extraction (events, commands, read models, external systems)
Step 2:   Quick Confirmation (present extraction, assess complexity)
Step 2.5: Bounded Context Framing (identify BCs from capabilities + language divergence)
Step 3a:  Fast-Path Confirmation (simple domains: 1-2 aggregates)
Step 3b:  Interactive Elicitation (complex domains: 3+ aggregates)
Step 4:   Domain Model Formalization (assign annotations, resolve hotspots)
Step 5:   Final Review + Save Files
```

---

## Step 0: Load Context

### Select Journey

1. **List available journeys** from `product-management/journeys/J*.md`
2. **Ask user to select** one journey (or accept if user already specified one)
3. **Read the selected journey** — extract:
   - Pain points (per phase)
   - System touchpoints
   - Feature references (F{n})
   - Persona references (P{n})
   - Parent Capability (C{n})

### Derive Scope from Journey

From the selected journey, automatically load:

4. **Feature files** referenced in the journey (`product-management/features/F{n}-*.md`) — extract scope, problem solved
5. **User Story files** linked to those features (`product-management/user-stories/US{n}-*.md`) — extract all AC (Given/When/Then)
6. **Persona files** referenced as actors (`product-management/personas/P{n}-*.md`) — identify human and system actors
7. **Vision constraints** (`product-management/vision.md`) — extract constraints and non-goals relevant to this journey's scope

### User Story Completeness Check

After loading context, check User Story coverage against in-scope Features:

1. For each Feature (F{n}) referenced in the Journey, check if `product-management/user-stories/US*-*.md` files exist that link to that Feature
2. For each found US file, check if it contains AC with Given/When/Then scenarios

**Classify coverage:**

| Coverage | Condition | Action |
|----------|-----------|--------|
| **Complete** | Every in-scope Feature has ≥1 US with AC | ✅ Proceed normally |
| **Partial** | Some Features have US, some don't | ⚠️ Warn + list gaps, ask user to choose: write US first or proceed with degraded quality |
| **Missing** | No US files found for any in-scope Feature | 🛑 Block — recommend running `user-story` skill first |

**When blocked or warned, present** the US Completeness Check panel (see `references/state-display-templates.md`).

**Question:** "User Stories are missing for {N} features. I recommend writing them first — the domain model quality depends heavily on AC for extracting events, commands, and invariants. Should I proceed anyway, or would you like to write User Stories first?"

If user chooses to proceed → mark uncovered Features in all downstream outputs, force Interactive path (Step 3b) for those aggregates.

### Display State Panel

Show the Step 0 panel (see `references/state-display-templates.md`).

---

## Step 1: Autonomous Extraction

Extract low-input elements from upstream artifacts WITHOUT asking the user:

### From User Story AC:
- `"Then a {X} is created/updated/deleted/activated/deactivated"` → **Domain Event**: `{X}Created`, `{X}Updated`, etc.
- `"I want to {verb} a {noun}"` → **Command**: `{Verb}{Noun}`
- `"So that I can {see/monitor/track} {X}"` → **Read Model**: `{X}View`

**Read-only operations are NOT domain events.** Actions like viewing, searching, listing, or querying do not change domain state. Suppress candidates like `Viewed`, `Searched`, `Listed`, `Queried`, `Fetched`. These belong to the Read Model / query side, not the event stream.

### From Journey Phases:
- System touchpoint in "系统触点" layer → **External System** + **Provider**
- Pain point implying reactive behavior → **Policy candidate** (details TBD in Step 3b)

### From Personas:
- User persona → human **Actor** for commands
- System component mentioned → system **Actor** for commands

DO NOT present anything yet. Accumulate silently for Step 2.

---

## Step 2: Quick Confirmation

Present the extraction as a concise summary and ask ONE question.
Show the Step 2 panel (see `references/state-display-templates.md`).

**Question:** "I extracted the above from your User Stories and Journeys. Does this look right? Anything missing or wrong?"

### Complexity Assessment Rules

| Signal | Fast-Path | Interactive |
|--------|-----------|-------------|
| Likely aggregates | 1-2 | 3+ |
| Cross-aggregate policies | No | Yes |
| Invariants all obvious from AC | Yes | No |
| User says "it's straightforward" | Yes | — |
| User says "it's complicated" / asks questions | — | Yes |

If **Fast-Path** → go to Step 2.5, then Step 3a.
If **Interactive** → go to Step 2.5, then Step 3b.

---

## Step 2.5: Bounded Context Framing

Before diving into aggregate boundaries, frame the bounded contexts. Aggregates are discovered *within* a BC, not globally — skipping this step risks lumping cross-context aggregates together.

### 2.5.1: Hypothesis from Capabilities

The selected Journey's parent Capability = the **primary BC candidate**. If the Journey crosses into other Capabilities (via cross-cutting phases or system touchpoints), those are **additional BC candidates**.

Read the Capability file(s) and extract:
- Capability name and User Goal → tentative BC name
- ~S (conceptual scenes) → scope of this BC
- ~F (feature candidates) → functionality within this BC

**Single-Capability Journey → default to 1 BC.** Skip to 2.5.3 for quick confirmation.

**Multi-Capability Journey → proceed through 2.5.2.**

### 2.5.2: Language Divergence Check

For each pair of candidate BCs, check whether key domain terms share the same meaning:

**Question pattern:**
> "I notice the term `{Term}` appears in both `{Capability A}` and `{Capability B}`.
> In `{Capability A}`, it seems to mean {meaning A}.
> In `{Capability B}`, it seems to mean {meaning B}.
>
> Are these the same concept, or different concepts that share a name?"

Signals that confirm separate BCs:
- **Same term, different meaning** (e.g., "Vehicle" in fleet ops = tracked asset; "Vehicle" in maintenance = service target)
- **Different lifecycle** (one Capability manages creation/updates; the other only reads a snapshot)
- **Different owners** (different teams responsible for each Capability's domain logic)

Signals that suggest merging into one BC:
- Terms mean the same thing and share the same lifecycle
- Commands in both Capabilities must be transactionally consistent
- Splitting would require constant synchronization with no clear boundary

**Per-pair limit: 1 question.** If the answer is ambiguous, mark as Hotspot and proceed — aggregate boundary discovery (Step 3b) will clarify.

### 2.5.3: Context Map from Journey Touchpoints

Map the Journey's system touchpoints (from the three-layer display) to BC relationships:

| Touchpoint Type | BC Relationship |
|---|---|
| External system we **depend on** (data/service in) | We are **Downstream**; external is Upstream |
| External system that **depends on us** (data/service out) | We are **Upstream**; external is Downstream |
| Shared touchpoint between our Capabilities | Relationship to elicit: ACL / OHS / Partnership |
| Internal system component (our own service) | Part of the same BC, or candidate for separation |

Present the proposed context map and ask ONE question.
Show the Step 2.5 panel (see `references/state-display-templates.md`).

**Question:** "I'm proposing {N} bounded context(s) based on the Capability boundaries. Does this partitioning make sense for your domain?"

If user confirms → carry BC assignments into Step 3a/3b. Aggregates are now discovered **per BC**.
If user adjusts → apply corrections, re-present.

---

## Step 3a: Fast-Path Confirmation

Present the FULL Event Storming output (including proposed aggregate boundaries, invariants, and policies) in ONE message.
Show the Step 3a panel (see `references/state-display-templates.md`).

**Question:** "Does this capture the domain correctly? Any business rules I'm missing?"

If user confirms → go to Step 4.
If user has corrections → apply corrections, re-present, confirm again.
If corrections reveal complexity → switch to Step 3b (Interactive).

---

## Step 3b: Interactive Elicitation

### Sub-Step 3b.1: Aggregate Boundary Questions

For EACH candidate aggregate grouping, ask about consistency requirements.
Show the Step 3b panel (see `references/state-display-templates.md`).

**Question pattern:**
> "I see commands {Cmd1} and {Cmd2}. These seem like {same aggregate / different aggregates} because {reasoning}. Can {action A} and {action B} happen independently, or must they be transactionally consistent?"

One aggregate boundary question per turn. After every 2-3 aggregates, calibrate:
> "So far I have {N} aggregates: {list with boundaries}. Does this structure feel right?"

### Sub-Step 3b.2: Invariant Discovery

For EACH confirmed aggregate, probe for business rules not in AC:

**Question pattern:**
> "For the {Aggregate} aggregate, I found these invariants from the AC:
> {list existing invariants}
>
> Are there other rules that must ALWAYS hold? For example:
> - {domain-specific probe question 1}?
> - {domain-specific probe question 2}?"

The probe questions should be SPECIFIC to the domain, not generic. Derive them from Journey pain points and Persona workflows.

**Per-dimension limit: 2 attempts.** After 2 questions about the same aggregate's invariants, mark remaining as "TBD" and move on.

### Sub-Step 3b.3: Policy Rule Elicitation

For EACH policy candidate from Step 1:

**Question pattern:**
> "The Journey shows {pain point description}. I'm proposing a {PolicyName}: when {Event} → {Action}.
>
> What exactly should happen? For example:
> - {option A}?
> - {option B}?
> - {option C}?"

### Sub-Step 3b.4: Ubiquitous Language Validation

After all discoveries, present the naming table:

**Question:**
> "Here's the Ubiquitous Language I've assembled:
>
> | Term | Definition |
> |------|-----------|
> | {terms from discoveries} |
>
> Are these terms what your team actually uses? Any corrections?"

---

## Step 4: Domain Model Formalization

### Assign Scania Annotations

Map every discovered concept to a `com.scania.hero.ddd.concepts` annotation.
Reference: `references/scania-ddd-concepts.md` for the full catalog and decision guides.

### Derive Fields, Dependencies, and Methods

For each concept in the aggregate, derive details from the tables already produced:

**AggregateRoot Fields** — derive from:
- Value Objects → aggregate properties (e.g., `GeoPosition` VO → `position: GeoPosition`)
- Child Entities → aggregate references (e.g., `VehicleRegistration` → `registration`)
- Command parameters → state being modified
- Event payloads → state that changed
- User Story AC "Given..." → preconditions implying state fields

**AggregateRoot Behaviors** — derive from:
- Commands → each command = one method (e.g., `UpdatePosition` → `updatePosition(geoPosition)`)
- Invariants → guard logic enforced within the method
- ENUM VO state transitions → state change methods (e.g., `activate()`, `deactivate()`)

**Entity Fields** — same derivation as AggregateRoot, scoped to entity-specific events/commands:
- Event payloads carrying entity-specific data
- Command parameters operating on the entity through the aggregate
- AC "Given..." mentioning entity state

**Domain Service Dependencies and Methods:**
- **Specification**: standard `isSatisfiedBy({params}) → boolean` signature; params from what's being evaluated
- **Policy**: `handle({Event}) → {Command}` from Policies table; dependencies = repositories/specs needed for the decision; internal logic from Step 3b.3 elicitation results (conditions, branching, debounce)
- **Domain Service**: methods from cross-entity coordination needs; dependencies from what entities/VOs are involved

**Provider Methods** — derive from External Systems table:
- Data/service provided → domain-facing method signature (e.g., "provides vehicle positions" → `fetchPosition(vehicleId) → GeoPosition`)
- Failure strategy → method exception/fallback behavior (e.g., "use last known" → returns `Optional`)

### Write Domain Model

Create one directory per bounded context, one file per aggregate:

```
product-management/domain/
  context-map.md                 # cross-BC context map (templates/context-map-template.md)
  {bc-slug}/
    README.md                    # BC overview (templates/bc-index-template.md)
    aggregate-{aggregate-slug}.md   # per-aggregate (templates/aggregate-template.md)
    aggregate-{aggregate2-slug}.md
  {bc2-slug}/
    README.md
    ...
```

- **`context-map.md`** — cross-BC context map with Mermaid diagram, BC relationships, and **traceability index**
- **BC README.md** — ubiquitous language, cross-aggregate services, cross-aggregate read models, PlantUML domain overview, hotspot resolutions
- **`aggregate-{aggregate-slug}.md`** — aggregate root, entities, VOs, commands, events, aggregate-scoped services, repository, factory, provider, aggregate-scoped read models

### Generate PlantUML Domain Overview

Generate a PlantUML **Domain Overview** class diagram in the BC `README.md`. Package by aggregate. Only include:
- **Entities** (`<<AggregateRoot>>` + child `<<Entity>>`)
- **Domain Services** (`<<Specification>>`, `<<Policy>>`, `<<DomainService>>`) — both aggregate-scoped and cross-aggregate

Do NOT include: Commands, Domain Events, Value Objects, Repositories, Factories, Providers, Read Models. Those details live in individual `aggregate-*.md` files.

### Populate Traceability Index

After writing all aggregate files, generate the **Traceability Index** in `context-map.md` (see `templates/context-map-template.md`). This enables reverse lookup from Journey/Feature/US → domain concepts.

**How to populate:** Scan all aggregate `.md` files' Source columns (Commands, Events, Invariants, Fields) and aggregate by:
- **By Journey** — group domain concepts by their J{n}.Phase{m}.PainPoint#{k} source
- **By Feature** — group by F{n}, listing all aggregates/commands/events that trace to it
- **By User Story** — group by US{n}, listing all domain concepts derived from that story's AC

Every Source field in aggregate files MUST use the standard format (`US{n}` / `J{n}.Phase{m}` / `F{n}`) to enable this aggregation.

**Relationships:**
- Within aggregate: containment (`*--`), command targets aggregate (`..> targets`), aggregate raises event (`..> raises`)
- Cross-aggregate: policy listens to event (`..> listens`), policy issues command (`..> issues`)

### Write Directly to Files

**Do NOT display the full domain model or PlantUML diagram in the conversation.** Write all output directly to files:
- BC README.md (with PlantUML embedded)
- Per-aggregate .md files

Then present only a summary panel for confirmation.
Show the Step 4 panel (see `references/state-display-templates.md`).

**Question:** "Domain model files written. Want me to adjust anything?"

---

## Step 5: Final Review + Save Files

### Save Event Storming

Write `event-storming-{journey-slug}.md` using `templates/event-storming-template.md` format.

Domain model files (BC directories + aggregate files) are already written in Step 4.

### Output Location

Write to `product-management/domain/` (or user-specified location):
- `product-management/domain/context-map.md` — cross-BC context map with Mermaid diagram + traceability index
- `product-management/domain/event-storming-{journey-slug}.md` — one per journey (discovery audit trail)
- `product-management/domain/{bc-slug}/` — **one directory per bounded context**, `aggregate-{slug}.md` per aggregate

### Completion Message

Show the Step 5 panel (see `references/state-display-templates.md`).

---

## Answer Drift Handling

Inherited from Harness product-framer. Applied during Steps 3a/3b:

| Type | Strategy |
|------|----------|
| **Off-topic but valuable** | Extract → park (note for later aggregate/invariant) → steer back |
| **Abstraction mismatch** (user gives solution, not problem) | Accept → reverse-engineer the domain need behind it |
| **Information dump** | Extract main threads → confirm → drill into one at a time |
| **Evasion/uncertainty** | Don't insist → reframe from different angle, or mark "TBD" |

---

## Resources

| File | Purpose |
|------|---------|
| `references/interaction-protocol.md` | Detailed Steps A-E interaction protocol with examples |
| `references/scania-ddd-concepts.md` | Full annotation catalog, subtypes, decision guides, Event Storming mapping |
| `references/enrichment-protocol.md` | Domain model formalization rules, quality gates |
| `references/state-display-templates.md` | ASCII state panels for all workflow steps |
| `templates/event-storming-template.md` | Output template for event-storming.md |
| `templates/context-map-template.md` | Output template for top-level context-map.md (cross-BC relationships, Mermaid diagram, traceability index) |
| `templates/bc-index-template.md` | Output template for BC directory README.md (ubiquitous language, PlantUML domain overview, read models) |
| `templates/aggregate-template.md` | Output template for per-aggregate .md files |
