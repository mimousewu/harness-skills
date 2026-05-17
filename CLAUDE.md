# Harness Skills — Product Management Pipeline

A chain of elicitation skills that progressively builds product understanding from vision to architecture. Each skill produces structured artifacts that downstream skills consume.

## The Chain

```
product-framer → journey-designer → user-story → event-storming → [sad-generator]
├─── Business Analysis (BA) ────────────────┤├── Technical Architecture ──┤
```

Run them in order. Each skill reads upstream artifacts and refuses to start without them (except product-framer, which is the entry point). `sad-generator` is optional — use it when you need a formal architecture document, not as a required final step.

## When to Use What

### Business Analysis

| Skill | Trigger | Prerequisites |
|-------|---------|---------------|
| `product-framer` | Plan a product, define what to build, create vision | None — always available |
| `journey-designer` | Map user journeys, CUJ, pain points to features | `vision.md`, `personas/`, `capabilities/` |
| `user-story` | Write user stories, break epic into stories, add AC | Standalone OK, or reads `journeys/` + `features/` |

### Technical Architecture

| Skill | Trigger | Prerequisites |
|-------|---------|---------------|
| `event-storming` | Domain modeling, discover aggregates, DDD model | `journeys/J*.md` + `features/F*.md` |
| `sad-generator` | Generate SAD, architecture document | All upstream artifacts |

## Artifact Flow

All skills write to `product-management/` in the working directory:

```
product-management/
  vision.md                          ← product-framer
  personas/P{n}-{name}.md           ← product-framer
  capabilities.yaml                  ← product-framer
  capabilities/C{n}-{name}.md       ← product-framer
  journeys/J{n}-{name}.md           ← journey-designer
  features/F{n}-{name}.md           ← journey-designer
  user-stories/US{n}-{name}.md      ← user-story
  mockups/{name}.md                  ← user-story (optional, UI features)
  domain/context-map.md             ← event-storming
  domain/event-storming-{slug}.md   ← event-storming
  domain/{bc-slug}/aggregate-*.md   ← event-storming
  architecture/sad.md               ← sad-generator
```

Each skill owns its directory and never modifies upstream files. If upstream artifacts need correction, switch to the upstream skill to make changes — then return to continue downstream work.

## How to Start

**Greenfield product:** Start with `/product-framer`. It elicits Vision, Personas, and Capabilities through conversation.

**Existing features, no structure:** Use `/product-framer` in reverse mode — feed it your feature list and it derives Vision + Capabilities bottom-up.

**Just need user stories:** `/user-story` works standalone. It asks clarifying questions if no upstream artifacts exist.

**Jump to domain modeling:** `/event-storming` works from journeys + features. `/sad-generator` synthesizes everything into arc42.

## Interaction Model

All skills are elicitation-first (except sad-generator which is synthesis-heavy):

- They ask questions before generating — never dump output without context
- They have hard gates — won't produce artifacts until they understand enough
- They surface tradeoffs and push back when scope seems wrong
- If a downstream skill discovers upstream problems, it recommends switching to the upstream skill to fix them — never silently modifying upstream files

## Key Principles

- **Progressive depth** — each layer adds specificity without repeating upstream work
- **Conversation over templates** — adapt format to context
- **Artifacts are living** — re-run skills when understanding evolves
- **Ownership boundaries** — each skill writes only to its own directory
