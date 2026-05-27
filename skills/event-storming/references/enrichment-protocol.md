# Enrichment Protocol: Domain Model Formalization

> Rules for formalizing Event Storming discoveries into a structured domain model. Referenced by SKILL.md Step 4.

---

## Formalization Steps (In Order)

### 1. Resolve Hotspots

Every open question from Event Storming MUST have one of:
- **Decision** — what we decided and why
- **Deferred** — explicitly marked with what additional information would resolve it

No silent ignoring. Every hotspot gets a disposition.

### 2. Define Ubiquitous Language

| Rule | Example |
|------|---------|
| Every domain term gets a precise, unambiguous definition | "Vehicle: A tracked transport unit identified by VehicleId, belonging to exactly one Fleet" |
| Terms must match what the team actually uses (validated in Step 3b.4) | Not "Transport Asset" if the team says "Vehicle" |
| These terms MUST appear in code identically | `VehicleId` in domain-model.md = `VehicleId.java` |
| Source traceability: link to Harness artifact where term originated | "Vehicle → C1 Vehicle Monitoring" |

### 3. Assign Annotations

Map every discovered concept to a DDD annotation.
See `references/ddd-concepts.md` for the full decision guide.

**Checklist per aggregate:**
- [ ] Root entity has `@AggregateRoot`
- [ ] Identity field has `@AggregateIdentifier` using `@ValueObject(Type.ID)` type
- [ ] Child entities have `@Entity`
- [ ] All immutable composites are `@ValueObject` with correct subtype
- [ ] All commands are `@DomainCommand` with VO-typed parameters
- [ ] All events are `@DomainEvent` with VO-typed payload
- [ ] All reactive rules are `@DomainService(Type.POLICY)`
- [ ] All business rule predicates are `@DomainService(Type.SPECIFICATION)`
- [ ] Persistence interface is `@Repository`
- [ ] Complex creation is `@Factory`
- [ ] External adapters are `@Provider`

### 4. Detail Value Objects

For each Value Object:
- **Type.ID**: Define format constraints (e.g., UUID, prefix pattern)
- **Type.VALUE**: Define immutability rules, validation constraints
- **Type.CRITERIA**: Define which fields are filterable
- **Type.ENUM**: Define all valid values AND valid transitions (if stateful)

### 5. Detail Domain Services

For each service:
- **DOMAIN**: Define which aggregates it spans and why a single aggregate can't handle it
- **SPECIFICATION**: Define `isSatisfiedBy()` signature and composability
- **POLICY**: Define trigger event, resulting command, and exact business rule

### 6. Define Write-Side Repositories

For each aggregate:
- `findById({AggregateId})` — always
- `save({Aggregate})` — always
- Domain-specific queries only if needed by command handlers

### 7. Define Read Models (CQRS)

For each Read Model:
- `@ReadModel` projection class with fields
- Which events update it (event handler mapping)
- `@Repository` query-only interface (no save/delete)
- `@ValueObject(Type.CRITERIA)` for query parameters

### 8. Identify Factories

Only create `@Factory` when aggregate creation is non-trivial:
- Multiple required parameters with validation
- Cross-aggregate coordination during creation
- Complex initialization sequences

Simple `new Aggregate(id)` does NOT need a factory.

### 9. Define Providers

For each external system:
- What domain concept it provides (in domain terms, not API terms)
- Failure strategy (fallback behavior when unavailable)
- ACL boundary (what it shields the domain from)

### 10. Draw Context Map

Only if multiple bounded contexts exist:
- ACL (Anti-Corruption Layer): downstream translates upstream's model
- OHS (Open Host Service): upstream provides a clean API
- Conformist: downstream uses upstream's model as-is
- Partnership: both sides evolve together

---

## Quality Gates

### Structural Validity

| Check | Pass Criteria |
|-------|--------------|
| Aggregate identity | Every aggregate has exactly one `@AggregateIdentifier` using `@ValueObject(Type.ID)` |
| Invariant presence | Every aggregate has >= 1 invariant |
| Command preconditions | Every command has explicit preconditions (or "none") |
| Event payloads | Every event has defined payload using VO types |
| Ubiquitous Language | Every term in tables appears in UL section |
| Traceability | Every concept traces to US, Feature, or Journey Pain Point |
| Size constraint | No aggregate > ~10 direct concepts; review for splitting if larger |
| CQRS separation | Read Models are separate from Aggregates; no save() on read-side repos |

### Completeness

| Check | Pass Criteria |
|-------|--------------|
| All Event Storming elements formalized | Every event, command, aggregate, policy, read model, external system has an annotation |
| All hotspots resolved or deferred | No silent gaps |
| UL validated with user | Step 3b.4 completed (or fast-path confirmed) |
