# Aggregate Template

<!--
Use this template for each aggregate file within a bounded context directory.
One file per aggregate. Contains everything owned by this aggregate.

File path: product-management/domain/{bc-slug}/aggregate-{aggregate-slug}.md
-->

# Aggregate: {AggregateName}

> Bounded Context: {BCName}

**Root Entity:** {EntityName} `@AggregateRoot`
**Aggregate Identifier:** {fieldName}: {IdType} `@AggregateIdentifier` `@ValueObject(Type.ID)`
**Invariants:**
1. {rule — source: US{n} AC / J{n}.Phase{m}.PainPoint#{k}}

### Fields

<!--
Derived from: Value Objects table (VOs = properties), Entity table (child entity references),
Command parameters (state being modified), Event payloads (state that changed),
User Story AC "Given..." clauses (preconditions imply state).
-->

| Field | Type | Source | Notes |
|-------|------|--------|-------|
| {fieldName} | {IdType} `@AggregateIdentifier` | — | Identity |
| {field} | {VOType} | Command: {CommandName} / Event: {EventName} | {why this field exists} |
| {field} | {EntityName} | Entity table | Child entity reference |
| {field} | {EnumType} | US{n} AC "Given..." | State field |

### Behaviors

<!--
Derived from: Commands table (each command = a method), Invariants (guard logic),
ENUM VO state transitions (activate/deactivate), Specifications (business rule predicates).
Each behavior corresponds to a command and enforces relevant invariants.
-->

| Method | Triggered By | Invariants Enforced | Events Raised |
|--------|-------------|--------------------:|---------------|
| {verbNoun}({params}) | Command: {CommandName} | #{n} {invariant summary} | {EventName} |

---

## Entities `@Entity`

<!--
Derived fields from: Event payloads (entity-specific data), Command parameters (operations on entity),
AC "Given..." (entity state preconditions), Value Objects (entity properties).
-->

| Entity | Relationship to Root | Source |
|--------|---------------------|--------|
| {EntityName} | {how it's owned by root} | US{n} / J{n}.Phase{m} |

### {EntityName} Fields

| Field | Type | Source | Notes |
|-------|------|--------|-------|
| {field} | {VOType} | Event: {EventName} payload | |
| {field} | {type} | Command: {CommandName} param | |
| {field} | {EnumType} | US{n} AC "Given..." | State field |

---

## Value Objects `@ValueObject`

| Value Object | Properties | VO Type | Constraints |
|-------------|-----------|---------|-------------|
| {Name}Id | value: String | `ID` | |
| | | `VALUE` | |
| | | `ENUM` | |

---

## Commands `@DomainCommand`

<!--
Source format: US{n} "I want to..." clause, or J{n}.Phase{m} user action.
Every command MUST have a Source for traceability index.
-->

| Command | Parameters | Preconditions | Events Raised | Source |
|---------|-----------|---------------|---------------|--------|
| {CommandName} | {params with VO types} | {what must be true} | {EventName} | US{n} / J{n}.Phase{m} |

---

## Domain Events `@DomainEvent`

<!--
Source format: US{n} AC "Then..." clause, or derived from Command.
Every event MUST have a Source for traceability index.
-->

| Event | Payload | Subscribers | Source |
|-------|---------|-------------|--------|
| {EventName} | {data using VO types} | {Policies / Read Models} | US{n} AC / J{n}.Phase{m} |

---

## Domain Services (Aggregate-Scoped)

<!--
Services that operate WITHIN this aggregate's boundary:
- SPECIFICATION: composable business rule predicate (e.g., isSatisfiedBy)
- POLICY: reactive rule where both trigger event and resulting command belong to THIS aggregate
- DOMAIN: logic involving only this aggregate's entities/VOs

Cross-aggregate services (spanning multiple aggregates) go in the BC README.md, not here.

Dependencies derived from: what aggregates/repos the service reads, what specs it uses.
Methods derived from: Policies table (handle signature), Spec standard signature, cross-entity coordination needs.
-->

### Specification: {SpecName} `@DomainService(Type.SPECIFICATION)`

**Dependencies:**

| Dependency | Type | Why Needed |
|-----------|------|-----------|
| | | |

**Methods:**

| Method | Parameters | Returns | Business Rule |
|--------|-----------|---------|---------------|
| isSatisfiedBy({params}) | {what's being evaluated} | boolean | {condition being checked} |

### Policy: {PolicyName} `@DomainService(Type.POLICY)`

**Dependencies:**

<!--
Derived from: what data the policy needs to make decisions.
Repositories for reading aggregate state, Specifications for evaluating conditions.
-->

| Dependency | Type | Why Needed |
|-----------|------|-----------|
| {Name}Repository | `@Repository` | Read aggregate state for decision |
| {SpecName} | `@DomainService(SPECIFICATION)` | Evaluate business condition |

**Methods:**

<!--
Derived from: Policies table (trigger event → resulting command = handle signature).
Internal logic from Step 3b.3 elicitation (conditions, branching, debounce, severity).
-->

| Method | Trigger Event | Resulting Command | Logic | Source |
|--------|--------------|-------------------|-------|--------|
| handle({EventName}) | {EventName} | {CommandName} | {conditions/branching from elicitation} | J{n}.Phase{m}.PainPoint#{k} |

### Domain Service: {ServiceName} `@DomainService(Type.DOMAIN)`

**Dependencies:**

| Dependency | Type | Why Needed |
|-----------|------|-----------|
| | | {why it spans entities within this aggregate} |

**Methods:**

| Method | Parameters | Returns | Business Rule |
|--------|-----------|---------|---------------|
| {operationName}({params}) | {VO types} | {result type} | {what cross-entity logic it performs} |

---

## Repository — Write Side `@Repository`

| Repository | Operations |
|-----------|-----------|
| {Name}Repository | findById({Id}), save({Aggregate}) |

---

## Factory `@Factory`

| Factory | Creates | Why Non-Trivial |
|---------|---------|-----------------|
| | | |

---

## Provider `@Provider`

<!--
Methods derived from: External Systems table (data/service provided → domain-facing method),
Commands that depend on external data (→ method purpose),
Failure strategy (→ exception/fallback behavior).
-->

| Provider | External System | Failure Strategy |
|----------|----------------|-----------------|
| {Name}Provider | {external API name} | {fallback behavior} |

### {Name}Provider Methods

| Method | Parameters | Returns | Domain Concept | Failure Behavior |
|--------|-----------|---------|---------------|-----------------|
| {fetchX}({params}) | {VO types} | {domain VO type} | {what it provides in domain terms} | {throw / return Optional / use cached} |

---

## Read Models — CQRS Read Side

<!--
Read models that are projections of THIS aggregate's events belong here.
Cross-aggregate read models (combining events from multiple aggregates) go in the BC README.md.
-->

### ReadModel: {Name}View `@ReadModel`

| Field | Type | Updated By Event |
|-------|------|-----------------|
| | | |

**Query Repository** `@Repository` (query-only):

| Operation | Parameters | Returns |
|-----------|-----------|---------|
| findBy{Id}({IdType}) | | {Name}View |
| findByCriteria({CriteriaType}) | | List<{Name}View> |

**Query Criteria** `@ValueObject(Type.CRITERIA)`:

| Criteria | Parameters |
|----------|-----------|
| {Name}SearchCriteria | |

---

## Aggregate Diagram (PlantUML)

<!--
Internal structure of THIS aggregate.
Only include: Entities (AggregateRoot with behaviors, child Entities),
Commands, Domain Events, Domain Services (with methods), Providers (with methods).

Do NOT include: Value Objects, Repositories, Factories, Read Models.
-->

```plantuml
@startuml aggregate-{aggregate-slug}
!theme plain
skinparam linetype ortho
skinparam packageStyle rectangle

package "{AggregateName}" <<Aggregate>> #F5F5DC {
  class {AggregateName} <<AggregateRoot>> {
    {verbNoun}({params})
    {verbNoun2}({params})
  }

  class {EntityName} <<Entity>> {
  }

  class {CommandName} <<DomainCommand>> {
  }

  class {EventName} <<DomainEvent>> {
  }

  class {SpecName} <<Specification>> {
    isSatisfiedBy({params}): boolean
  }

  class {PolicyName} <<Policy>> {
    handle({EventName}): {CommandName}
  }

  class {Name}Provider <<Provider>> {
    {fetchX}({params}): {VOType}
  }

  {AggregateName} *-- {EntityName}
  {CommandName} ..> {AggregateName} : targets
  {AggregateName} ..> {EventName} : raises
  {PolicyName} ..> {EventName} : listens
  {PolicyName} ..> {CommandName} : issues
  {PolicyName} ..> {SpecName} : uses
}

@enduml
```
