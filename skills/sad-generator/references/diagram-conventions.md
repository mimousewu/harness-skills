# Diagram Conventions

Standards for diagram generation in the SAD. PlantUML C4 library for architecture diagrams (superior layout control), Mermaid for sequence diagrams (clean message syntax).

---

## Tool Selection

| Diagram Type | Tool | Reason |
|---|---|---|
| C4 System Context | PlantUML C4 | Graphviz layout engine, directional control (`Rel_Down`, `Rel_Right`) |
| C4 Container | PlantUML C4 | Same, plus `System_Boundary()` nesting |
| C4 Component | PlantUML C4 | Consistent with Context/Container, aggregate collaboration |
| Sequence | Mermaid | Clean message syntax, good layout for linear flows |
| Deployment | PlantUML | Node/cloud nesting, deployment-specific notation |

---

## PlantUML C4 Diagrams

All C4 diagrams use the [C4-PlantUML](https://github.com/plantuml-stdlib/C4-PlantUML) standard library via `!include <C4/C4_Context>`, `!include <C4/C4_Container>`, `!include <C4/C4_Component>`.

### Layout Control

| Macro | Effect |
|---|---|
| `LAYOUT_TOP_DOWN()` | Flow from top to bottom (default for Context) |
| `LAYOUT_LEFT_RIGHT()` | Flow from left to right (good for pipelines) |
| `LAYOUT_LANDSCAPE()` | Left-to-right without rotating directional rels |
| `Rel_Down(from, to, ...)` | Force arrow downward |
| `Rel_Right(from, to, ...)` | Force arrow rightward |
| `Rel_Left(from, to, ...)` | Force arrow leftward |
| `Rel_Up(from, to, ...)` | Force arrow upward |
| `SHOW_LEGEND()` | Add color legend at bottom |

**Layout strategy:**
- Actors at top → `Rel_Down` to system
- System in center
- External systems at right → `Rel_Right` from system
- Within boundaries, let Graphviz auto-layout

### System Context Diagram (§3)

```plantuml
@startuml context
!include <C4/C4_Context>

LAYOUT_TOP_DOWN()

Person(p1, "{Persona 1}", "{Brief role}")
Person(p2, "{Persona 2}", "{Brief role}")

System(sys, "{Product Name}", "{One-line description}")

System_Ext(ext1, "{External System 1}", "{What it provides}")
System_Ext(ext2, "{External System 2}", "{What it provides}")

Rel_Down(p1, sys, "{Action verb}", "HTTPS")
Rel_Down(p2, sys, "{Action verb}", "HTTPS")
Rel_Right(sys, ext1, "{Integration purpose}", "REST")
Rel_Right(sys, ext2, "{Integration purpose}", "MQTT")

SHOW_LEGEND()
@enduml
```

**Rules:**
- Maximum 6-8 elements (beyond that, group personas)
- Use directional rels to keep actors on top, externals on right
- Relationship labels = verb phrases + protocol
- Always include `SHOW_LEGEND()` for readability

### Container Diagram (§5 Level 1)

```plantuml
@startuml container
!include <C4/C4_Container>

LAYOUT_TOP_DOWN()

Person(user, "{Primary Persona}", "{role}")

System_Boundary(boundary, "{Product Name}") {
  Container(gw, "API Gateway", "Spring Cloud Gateway", "Routing, auth, rate limiting")
  Container(svc1, "{BC1 Name} Service", "Spring Boot", "{Capability summary}")
  Container(svc2, "{BC2 Name} Service", "Spring Boot", "{Capability summary}")
  ContainerDb(db1, "{BC1} DB", "MySQL/PostgreSQL", "Stores {domain} data")
  ContainerDb(db2, "{BC2} DB", "MySQL/PostgreSQL", "Stores {domain} data")
  ContainerQueue(mq, "Message Broker", "Kafka", "Domain events")
}

System_Ext(ext1, "{External System}", "{description}")

Rel_Down(user, gw, "Uses", "HTTPS")
Rel_Down(gw, svc1, "Routes", "HTTP")
Rel_Down(gw, svc2, "Routes", "HTTP")
Rel_Right(svc1, mq, "Publishes events")
Rel_Left(mq, svc2, "Delivers events")
Rel_Right(svc1, ext1, "Fetches data", "REST")
Rel_Down(svc1, db1, "Reads/Writes", "JDBC")
Rel_Down(svc2, db2, "Reads/Writes", "JDBC")

SHOW_LEGEND()
@enduml
```

**Rules:**
- One container per BC (1 BC = 1 service)
- Infrastructure containers: Gateway, Message Broker, databases
- BCs without domain models still appear (label: "{Name} Service [planned]")
- Database per service (even if type is TBD)
- Use `Rel_Down` for user→gateway→services, `Rel_Right` for service→external

### Component Diagram (§5 Level 2, per BC)

```plantuml
@startuml component-{bc-slug}
!include <C4/C4_Component>

LAYOUT_TOP_DOWN()

Container_Boundary(bc, "{BC Name} Service") {
  Component(agg1, "{Aggregate1}", "Aggregate", "{Core responsibility}")
  Component(agg2, "{Aggregate2}", "Aggregate", "{Core responsibility}")
  Component(policy1, "{CrossAggregateService}", "Policy", "{What it orchestrates}")
}

Rel_Right(agg1, policy1, "Event: {EventName}")
Rel_Down(policy1, agg2, "Command: {CommandName}")
Rel(agg1, agg2, "uses snapshot", "")

SHOW_LEGEND()
@enduml
```

**Rules:**
- Only TWO component types: Aggregates + Cross-Aggregate Domain Services
- Relationships show event flows and command issuance between components
- Snapshot/reference dependencies shown with `Rel()` (no forced direction)
- If aggregate count > 5, group by functional subdomain
- Maximum 10 components per diagram

---

## Mermaid Diagrams

### Sequence Diagrams (§6)

```mermaid
sequenceDiagram
  actor User as {Persona Name}
  participant GW as API Gateway
  participant Svc1 as {BC1} Service
  participant Agg1 as {Aggregate1}
  participant Svc2 as {BC2} Service
  participant Ext as {External System}

  User->>GW: POST /api/{resource}
  GW->>GW: Validate token
  GW->>Svc1: {CommandName}
  activate Svc1
  Svc1->>Agg1: handle({CommandName})
  Note over Agg1: Invariant: {rule}
  Agg1->>Agg1: apply({EventName})
  Svc1-->>GW: 202 Accepted
  deactivate Svc1
  
  Note over Svc1,Svc2: Async via Kafka
  Svc1-)Svc2: Event: {EventName}
  activate Svc2
  Svc2->>Ext: {Provider.method()}
  Ext-->>Svc2: {response data}
  Svc2->>Svc2: apply({ResultEvent})
  deactivate Svc2
```

**Rules:**
- Title each diagram with the flow name: `## {Flow Name}`
- Use `->>` for sync calls, `-)` for async messages
- Show `activate`/`deactivate` for request scope
- Include invariant checks as `Note over` when they affect flow
- Show auth step at Gateway level
- Maximum 8 participants per diagram (merge if needed)
- Use HTTP verbs for external-facing calls, Command names for internal

---

## PlantUML Deployment Diagram (§7)

```plantuml
@startuml deployment
!theme plain
skinparam linetype ortho

title Deployment View - {Product Name}

cloud "Kubernetes Cluster" as k8s {
  
  node "Ingress" as ingress {
    artifact "API Gateway" as gw
  }
  
  frame "{BC1} Namespace" as ns1 {
    artifact "{BC1} Service" as svc1
    database "{BC1} DB" as db1
  }
  
  frame "{BC2} Namespace" as ns2 {
    artifact "{BC2} Service" as svc2
    database "{BC2} DB" as db2
  }
  
  node "Infrastructure" as infra {
    artifact "Kafka" as mq
    artifact "MQTT Broker" as mqtt
  }
}

node "Vehicle Side" as vehicle {
  artifact "T-Box" as tbox
  artifact "{Agent}" as agent
  artifact "{ECU}" as ecu
}

node "External Systems" as external {
  artifact "{ExtSystem}" as ext
}

ingress --> svc1 : "HTTP"
ingress --> svc2 : "HTTP"
svc1 --> mq : "publish"
mq --> svc2 : "subscribe"
svc2 --> mqtt : "publish config"
mqtt --> tbox : "MQTT/TLS"
tbox --> agent : "IPC"
agent --> ecu : "CAN/DoIP"
tbox --> mqtt : "report status"
svc1 --> ext : "REST/HTTPS"
svc1 --> db1 : "JDBC"
svc2 --> db2 : "JDBC"

@enduml
```

**Rules:**
- K8s cluster = `cloud` notation
- Namespace per BC = `frame` notation
- Vehicle side = separate `node` zone (physical boundary: different hardware/network)
- MQTT Broker = bridge between cloud and vehicle zones
- External systems outside both zones
- Show protocol on every relationship (especially cloud↔vehicle boundary)
- If network isolation was specified, add boundary boxes
- Keep simplified — no replica counts, no resource specs

---

## General Styling Rules

### Naming Conventions

| Element | Naming Style | Example |
|---------|-------------|---------|
| Services | {BC Name} Service | Offering Management Service |
| Databases | {BC} DB | Offering DB |
| Aggregates | {AggregateName} | Offering, FpcCode |
| External systems | Full name from context-map | OAS (Offering Administration System) |
| Message broker | Technology name | Kafka |
| API Gateway | "API Gateway" (fixed) | API Gateway |

### Complexity Limits

| Diagram Type | Max Elements | If Exceeded |
|---|---|---|
| Context | 8 | Group minor actors |
| Container | 12 | Split into zones |
| Component | 10 per BC | Focus on key aggregates |
| Sequence | 8 participants | Merge services |
| Deployment | 15 nodes | Simplify to logical view |

---

## Embedding in Markdown

### PlantUML

````markdown
```plantuml
{diagram content}
```
````

### Mermaid

````markdown
```mermaid
{diagram content}
```
````

PlantUML requires a rendering plugin or server (VS Code PlantUML extension, GitLab native, or PlantUML server). Mermaid renders natively in GitHub, GitLab, and Obsidian.
