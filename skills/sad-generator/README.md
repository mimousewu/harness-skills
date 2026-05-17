# SAD Generator

Generate a Software Architecture Document (SAD) from Harness Engineering chain outputs. This skill synthesizes existing `product-management/` artifacts — domain models, journeys, event storming sessions, capabilities, and personas — into a unified architecture document following the arc42 template structure with C4 model diagrams.

**Scope:** All upstream Harness outputs → SAD (single `architecture/sad.md` file). Upstream: `product-framer`, `journey-designer`, `user-story`, `event-storming`. Downstream: implementation teams + development Agents.

---

## Methodology Foundations

### Position in the Analysis Chain

SAD-generator owns the architecture documentation layer — the synthesis of all upstream business analysis into a technical blueprint:

| Layer | Artifact | Owner | sad-generator role |
|---|---|---|---|
| Vision | Vision + Personas | product-framer | Reads for goals, constraints, stakeholders |
| Capability | Capability + ~S + ~F | product-framer | Reads for system scope boundary |
| Journey | Journey (Phases + Pain Points) | journey-designer | Reads for runtime scenarios |
| Feature | Feature definitions | journey-designer | Reads for functional decomposition |
| User Story | US + Acceptance Criteria | user-story | Reads for scenario detail |
| Domain Model | BC + Aggregates + Events | event-storming | Reads for logical structure |
| **Architecture** | **SAD (arc42 + C4)** | **sad-generator** | **✅ Produces** |

This terminal position means sad-generator has the richest input context of any skill in the chain. It consumes everything upstream and produces a unified architectural view.

### Harness Engineering: SAD as Agent Implementation Blueprint

Under Harness Engineering, the SAD serves as the **system-level structural guide for development Agents:**

> Agents need to know not just "what aggregate to implement" (from domain model files) but "how services communicate", "where they deploy", and "what cross-cutting patterns to follow." The SAD provides this system-level context that individual domain model files cannot.

The SAD bridges the gap between:
- **Domain model files** (per-aggregate, per-BC scope) → tell Agents WHAT to build
- **SAD** (system-wide scope) → tells Agents HOW the pieces fit together

This dual-level guidance (domain model for component internals, SAD for system integration) is what enables Agents to produce architecturally coherent implementations across service boundaries.

### Arc42: Template as Thinking Framework

The skill adopts arc42 (Starke & Hruschka, 2005) as the documentation structure — not because it prescribes a methodology, but because it provides:

1. **A complete shelf:** Every architecture concern has a named home (section)
2. **No-duplication principle:** Each fact lives in exactly one section
3. **Progressive filling:** Sections can be incomplete — `[TBD]` is valid
4. **Docs-as-code native:** Designed for Markdown/AsciiDoc in git repositories

The skill uses a **simplified arc42** (10 sections, no ADR chapter) optimized for the Harness Engineering context where architecture decisions are traceable through upstream artifacts rather than standalone ADR files.

### C4 Model: Zoom Levels for Diagrams

Diagrams follow Simon Brown's C4 model (2011) — hierarchical zoom levels:

| Level | What It Shows | When Generated |
|---|---|---|
| System Context (L1) | System boundary, actors, external systems | Always |
| Container (L2) | Services, databases, message brokers | Always |
| Component (L3) | Aggregates and cross-aggregate services within a container | Per modeled BC |
| Code (L4) | Class-level detail | Never (domain model files serve this role) |

C4 Level 4 is intentionally omitted — the per-aggregate `.md` files from event-storming already provide this level of detail. The SAD adds system-level views (L1-L3) that don't exist elsewhere.

### 4+1 Thinking: Ensuring View Coverage

While arc42 provides the structure, the 4+1 view model (Kruchten, 1995) provides the **completeness check** — ensuring no stakeholder perspective is missed:

| 4+1 View | arc42 Section | Stakeholder |
|---|---|---|
| Logical | §5 Building Block View | Developers |
| Process | §6 Runtime View | System engineers |
| Development | §5 (package level) | Developers |
| Physical | §7 Deployment View | Operations |
| Scenarios | §6 (selected flows) | All |

The skill verifies all five perspectives are addressed before considering the SAD complete.

---

## Design Principles

### 1. Synthesis Over Elicitation

This is the fundamental distinction from upstream skills. Upstream skills discover NEW information through progressive conversation (80% elicitation, 20% generation). SAD-generator DERIVES architecture from existing structured artifacts (70% synthesis, 30% minimal elicitation).

The user has already invested significant effort in the Harness chain. The SAD skill's job is to extract maximum value from that investment, not to re-ask questions already answered upstream.

### 2. Derive What You Can, Ask What You Must

The skill follows a strict hierarchy:
1. **Auto-derive** from upstream files (preferred)
2. **Infer** from patterns and conventions (acceptable)
3. **Use defaults** for common technology choices (acceptable with disclosure)
4. **Ask the user** only for decisions that cannot be derived, inferred, or defaulted (last resort)

### 3. No Use Case Diagrams

Use Case diagrams are explicitly excluded. In the Harness context:
- Journeys already describe user interaction paths (richer than use case ovals)
- User Stories already define Given/When/Then scenarios (more testable)
- Use Case diagrams add visual noise without new information

Scenarios are represented through sequence diagrams (showing architectural component interaction) and cross-references to journey/story files.

### 4. Diagrams Must Add Information

Every diagram must convey something not already clear from the text. Criteria:
- Does this diagram show RELATIONSHIPS between components? → Generate
- Does this diagram show SEQUENCE of interactions? → Generate
- Does this diagram merely label things already described in text? → Skip

### 5. TBD Is Better Than Fabrication

When information is genuinely unavailable:
- ✅ `[TBD — deployment topology pending infrastructure team decision]`
- ❌ Inventing plausible-looking deployment diagrams from assumptions

TBD sections serve as explicit signals to the user about what architectural decisions remain open.

### 6. Default Technology Stack

For Spring Cloud + K8s ecosystems (the dominant pattern in the target domain), the skill assumes reasonable defaults rather than asking obvious questions:

| Default | Assumption | Disclosed In |
|---|---|---|
| Spring Cloud | Microservice framework | §4 Solution Strategy |
| Kubernetes | Container orchestration | §7 Deployment |
| REST + Events | Communication pattern | §4, §6 |
| K8s DNS | Service discovery | §7 |

Defaults are clearly labeled as assumptions. The user can override during review.

### 7. Single File, Complete Picture

The SAD is one file because:
- Architecture must be understood holistically (splitting defeats the purpose)
- Cross-references between sections are frequent
- A developer/Agent reads ONE file to get the complete system picture
- Per-BC detail already lives in domain model files (no need to duplicate)

### 8. Bidirectional References

The SAD references upstream artifacts by relative path. Upstream artifacts don't reference the SAD (it's terminal). This maintains the unidirectional flow of the Harness chain while enabling navigation from SAD back to source material.

---

## Key Design Decisions

### Why arc42 Over Pure 4+1

| Factor | arc42 | 4+1 |
|---|---|---|
| **Structure** | 12 named sections (fill-as-you-go) | 5 views (must define scope of each) |
| **Cross-cutting** | Dedicated §8 | No natural home |
| **Constraints** | Dedicated §2 | No natural home |
| **Risks** | Dedicated §10/§11 | Not covered |
| **Markdown-native** | Yes (designed for it) | Requires adaptation |
| **Completeness signal** | Missing sections are visible gaps | Missing views are silent |

Arc42 subsumes 4+1 (every view has a section) while adding architecture concerns that 4+1 doesn't address (constraints, cross-cutting, risks, quality attributes).

### Why Not TOGAF 4A

TOGAF's Architecture Domains (Business, Data, Application, Technology) are enterprise-governance scoped:
- Business Architecture is already covered by the Harness chain (Vision, Capabilities, Journeys)
- Data Architecture doesn't capture behavioral DDD concepts (commands, events, policies)
- Application Architecture operates at portfolio level, not system internals
- The target audience is developers/Agents, not architecture review boards

### Why C4 Container = Bounded Context

In DDD-based systems, the natural service boundary is the Bounded Context. The skill maps:
- 1 BC → 1 Container (microservice)
- Aggregate + Cross-Aggregate Domain Service → Component within that container
- Context Map relationship → Container-to-container communication

This mapping is explicit and documented. If the user's architecture deviates (e.g., multiple BCs in one service), the elicitation step catches it.

### Why Maximum 5 Sequence Diagrams

Too many sequence diagrams create noise rather than clarity. The selection criteria prioritize:
1. **Cross-BC flows** — show service integration (highest value)
2. **Event-driven chains** — show async patterns
3. **External system interactions** — show ACL/Provider patterns
4. **Complex branching** — show policy decision logic

Single-aggregate, single-command flows (like simple CRUD) don't warrant sequence diagrams — the domain model file is sufficient.

### Why PlantUML C4 for Architecture, Mermaid for Sequence

| Diagram | Tool | Reason |
|---|---|---|
| C4 Context | PlantUML C4 | Graphviz layout, directional control (`Rel_Down`, `Rel_Right`) |
| C4 Container | PlantUML C4 | Same, plus `System_Boundary()` nesting |
| C4 Component | PlantUML C4 | Consistent with above, aggregate collaboration |
| Sequence | Mermaid | Clean linear message syntax, native GitHub rendering |
| Deployment | PlantUML | Node/cloud nesting, deployment-specific notation |

PlantUML C4 uses the [C4-PlantUML](https://github.com/plantuml-stdlib/C4-PlantUML) standard library which provides superior layout control over Mermaid C4 (which lacks directional relationship macros and produces tangled connections with >4 elements). Mermaid is kept only for sequence diagrams where its linear top-to-bottom flow is ideal.

---

## Workflow Overview

```
Step 0:   Inventory (scan product-management/, classify artifacts, generate coverage)
Step 1:   Auto-Derive (generate all sections derivable from files alone)
Step 2:   Gap Assessment + Elicit (1-3 questions for critical decisions)
Step 3:   Generate SAD (assemble document, embed diagrams)
Step 4:   Review + Save (present, iterate, write file)
```

See `SKILL.md` for detailed step-by-step instructions.

---

## Output Specification

```
product-management/
  architecture/
    sad.md              # Complete Software Architecture Document (single file)
```

The SAD file contains:
- Generation metadata header (date, source directory)
- 10 arc42 sections (simplified, no ADR)
- Embedded diagrams as fenced code blocks (PlantUML or Mermaid)
- Relative-path references to upstream artifacts
- `[TBD]` markers for unresolved sections

---

## Methodology Concept-to-Source Traceability

| Skill Concept | Origin | Key Source |
|---|---|---|
| Arc42 template | Gernot Starke & Peter Hruschka (2005) | Pragmatic software architecture documentation template |
| C4 Model | Simon Brown (2011) | Hierarchical zoom levels for architecture diagrams |
| 4+1 View Model | Philippe Kruchten (1995) | Multi-stakeholder architecture views |
| Synthesis-over-elicitation | Original to this skill | Adaptation of Harness interaction model for terminal-position skill |
| Default technology assumptions | Spring Cloud ecosystem conventions | Industry-standard microservice patterns |
| TBD-over-fabrication | Architecture documentation best practice | "Document decisions, not assumptions" principle |
| Bounded Context = Service | DDD strategic design (Eric Evans, 2003) | BC as natural microservice boundary |

---

## Resources

| File | Purpose |
|------|---------|
| `SKILL.md` | Complete step-by-step execution instructions |
| `references/derivation-rules.md` | How each SAD section derives from upstream artifacts |
| `references/elicitation-questions.md` | Categorized architecture decision questions (Chinese) |
| `references/diagram-conventions.md` | PlantUML/Mermaid syntax, C4 notation, styling rules |
| `references/state-display-templates.md` | ASCII state panels for workflow steps |
| `templates/sad-template.md` | Complete SAD output template (single file) |
