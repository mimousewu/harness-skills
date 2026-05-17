# BC Index Template

<!--
Use this template for the README.md in each bounded context directory.
Contains BC-level overview, ubiquitous language, cross-aggregate services,
read models, context map, hotspots, and PlantUML class diagram.

File path: product-management/domain/{bc-slug}/README.md
-->

# {BCName}

> Source: C{n} {CapabilityName}
> Core Responsibility: {what this context owns}

## Aggregates

| Aggregate | Root Entity | Key Invariants | File |
|-----------|-------------|----------------|------|
| {AggregateName} | {EntityName} | {summary} | `aggregate-{aggregate-slug}.md` |

---

## Ubiquitous Language

| Term | Definition | Harness Source |
|------|-----------|----------------|
| | | |

---

## Cross-Aggregate Domain Services

<!--
ONLY services that span multiple aggregates belong here:
- POLICY: reacts to events from Aggregate A, issues commands to Aggregate B
- DOMAIN: logic that reads/coordinates across multiple aggregates

Aggregate-scoped services (SPECIFICATION, single-aggregate POLICY/DOMAIN)
belong in the aggregate's own .md file, NOT here.
-->

| Service | Type | Trigger | Action | Business Rule |
|---------|------|---------|--------|---------------|
| | `POLICY` | {EventName} from {Agg1} | {CommandName} on {Agg2} | |
| | `SPECIFICATION` | | | |
| | `DOMAIN` | | | |

---

## Cross-Aggregate Read Models — CQRS Read Side

<!--
ONLY read models that combine events from MULTIPLE aggregates belong here.
Read models projecting a single aggregate's events belong in that aggregate's .md file.
If all read models are single-aggregate, this section can be removed.
-->

### ReadModel: {Name}View `@ReadModel`

| Field | Type | Updated By Events (from multiple aggregates) |
|-------|------|----------------------------------------------|
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

## Context Map (this BC)

<!--
Only relationships involving THIS bounded context.
Full cross-BC context map is in the top-level context-map.md.
-->

| Upstream | Downstream | Pattern | Notes |
|----------|-----------|---------|-------|
| | | | |

---

## Hotspot Resolutions

| Hotspot | Decision | Rationale |
|---------|---------|-----------|
| | | |

---

## Domain Overview (PlantUML)

<!--
Class diagram showing the domain's core structure across ALL bounded contexts.
Package by aggregate. Only include:
- Entities (AggregateRoot + child Entities)
- Domain Services (aggregate-scoped and cross-aggregate)

Do NOT include: Commands, Domain Events, Value Objects, Repositories,
Factories, Providers, Read Models. Those details live in individual aggregate .md files.
-->

```plantuml
@startuml domain-overview
!theme plain
skinparam linetype ortho
skinparam packageStyle rectangle

' ══════════════════════════════════════
' {BCName}
' ══════════════════════════════════════

package "{Agg1Name}" <<Aggregate>> #F5F5DC {
  class {Agg1Name} <<AggregateRoot>> {
  }

  class {EntityName} <<Entity>> {
  }

  class {SpecName} <<Specification>> {
  }

  {Agg1Name} *-- {EntityName}
}

package "{Agg2Name}" <<Aggregate>> #F5F5DC {
  class {Agg2Name} <<AggregateRoot>> {
  }
}

' -- Cross-Aggregate Domain Services --

class {CrossPolicyName} <<Policy>> {
}

@enduml
```
