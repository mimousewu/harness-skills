# DDD Concepts: Annotation Reference

> Source: `ddd-concepts`
> License: MIT (Tao Wu, 2022)

---

## Write Side (Command)

| Annotation | `@Target` | Retention | Subtypes | Purpose |
|-----------|-----------|-----------|----------|---------|
| `@AggregateRoot` | TYPE | Source | — | Root entity of an aggregate (transactional consistency boundary) |
| `@AggregateIdentifier` | FIELD | Source | — | Field serving as aggregate unique identity |
| `@Entity` | TYPE | Source | — | Child entity within aggregate; has identity, not independently accessible |
| `@ValueObject` | TYPE | **Runtime** | `ID`, `VALUE`, `CRITERIA`, `ENUM` | Immutable object defined by attributes |
| `@DomainEvent` | TYPE | Source | — | Past-tense record of something that happened |
| `@DomainCommand` | TYPE | Source | — | Intent to change domain state |
| `@DomainService` | TYPE | Source | `DOMAIN`, `SPECIFICATION`, `POLICY` | Stateless domain operation |
| `@Repository` | TYPE | Source | — | Collection-like interface for aggregate persistence (load + save) |
| `@Factory` | TYPE, METHOD | Source | — | Complex object/aggregate creation logic |
| `@Provider` | TYPE | Source | — | External system adapter (anti-corruption layer) |

## Read Side (CQRS)

| Annotation | `@Target` | Retention | Purpose |
|-----------|-----------|-----------|---------|
| `@ReadModel` | TYPE | Source | Denormalized projection built from domain events |
| `@Repository` | TYPE | Source | Query-only interface (find methods, NO save/delete) |
| `@ValueObject(Type.CRITERIA)` | TYPE | Runtime | Query/filter parameter object |

---

## ValueObject Subtypes Decision Guide

| Subtype | When to Use | Naming Convention | Example |
|---------|------------|-------------------|---------|
| `ID` | Typed identity replacing primitive String/Long | `{Aggregate}Id` | `VehicleId`, `GeofenceId`, `OrderId` |
| `VALUE` | Immutable composite with domain meaning | Domain noun | `GeoPosition`, `Money`, `Address` |
| `CRITERIA` | Encapsulates query/filter parameters | `{Domain}SearchCriteria` | `VehicleSearchCriteria`, `AlertFilterCriteria` |
| `ENUM` | Domain-meaningful finite set of states | `{Concept}Status` / `{Concept}Type` | `VehicleStatus`, `AlertSeverity` |

## DomainService Subtypes Decision Guide

| Subtype | When to Use | Naming Convention | Example |
|---------|------------|-------------------|---------|
| `DOMAIN` | Logic spanning multiple aggregates | `{Action}Service` | `RouteOptimizationService` |
| `SPECIFICATION` | Composable business rule predicate | `{Rule}Spec` / `{Rule}Specification` | `GeofenceContainmentSpec` |
| `POLICY` | Reactive: event triggers command | `{Trigger}Policy` | `GeofenceCheckPolicy`, `AlertCreationPolicy` |

## Factory Target Note

`@Target({ElementType.TYPE, ElementType.METHOD})` — can be applied to:
- **Class level**: dedicated factory class for complex aggregate creation
- **Method level**: factory method within an aggregate root or service

## Provider: Custom Extension

Not part of Evans' original DDD. Represents the implementation side of an anti-corruption layer — shields the domain from external system changes.

---

## Event Storming → Annotation Mapping

| Event Storming Element | Color | Annotation | CQRS Side |
|----------------------|-------|-----------|-----------|
| Domain Event | Orange | `@DomainEvent` | Write |
| Command | Blue | `@DomainCommand` | Write |
| Aggregate | Yellow | `@AggregateRoot` + `@AggregateIdentifier` | Write |
| Policy | Lilac | `@DomainService(Type.POLICY)` | Write |
| External System | Pink | `@Provider` | Write |
| Read Model | Green | `@ReadModel` + `@Repository` (query-only) | Read |
| Actor | Small yellow | Mapped to Harness Persona (P{n}) | — |
| Hotspot | Red/Pink | No annotation — tracked as Open Questions | — |

---

## Domain Model → Java Code Mapping

```
domain-model.md concept               Java artifact                          CQRS Side
─────────────────────────────────────  ───────────────────────────────────── ──────────
Aggregate: Vehicle                     @AggregateRoot class Vehicle           Write
  Identifier: vehicleId               @AggregateIdentifier VehicleId         Write
  Entity: VehicleRegistration          @Entity class VehicleRegistration      Write
  VO: VehicleId (ID)                   @ValueObject(Type.ID) record          Both
  VO: GeoPosition (VALUE)              @ValueObject(Type.VALUE) record        Both
  VO: VehicleStatus (ENUM)             @ValueObject(Type.ENUM) enum           Both
  VO: VehicleSearchCriteria (CRITERIA) @ValueObject(Type.CRITERIA) record     Read
  Command: UpdatePosition              @DomainCommand record                  Write
  Event: PositionUpdated               @DomainEvent record                    Write→Read
  Service: GeofenceCheckPolicy         @DomainService(Type.POLICY) class      Write
  Service: GeofenceContainmentSpec     @DomainService(Type.SPECIFICATION)     Write
  Repository: VehicleRepository        @Repository interface (load+save)      Write
  Factory: VehicleFactory              @Factory class                         Write
  Provider: TelematicsProvider         @Provider class                        Write
  ReadModel: VehicleTrackingView       @ReadModel class                       Read
  Repository: ViewRepository           @Repository interface (query-only)     Read
```
