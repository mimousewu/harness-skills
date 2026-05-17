# State Display Templates

All ASCII state panels used during the Event Storming workflow.
Show the appropriate panel before every question to give the user context.

---

## Step 0: US Completeness Check (when gaps found)

```
╔══════════════════════════════════════════════════════════════╗
║  USER STORY COMPLETENESS CHECK                               ║
╠══════════════════════════════════════════════════════════════╣
║                                                              ║
║  Features in scope: F{n}, F{m}, F{k}                        ║
║                                                              ║
║  ✅ F{n} {name}: {count} US with AC                          ║
║  ❌ F{m} {name}: no User Stories found                       ║
║  ❌ F{k} {name}: no User Stories found                       ║
║                                                              ║
║  Impact: Event/Command/ReadModel extraction will be          ║
║  incomplete for uncovered Features. Invariants will           ║
║  rely entirely on interactive elicitation.                    ║
║                                                              ║
║  Recommendation: run user-story skill for F{m}, F{k} first  ║
╚══════════════════════════════════════════════════════════════╝
```

---

## Step 0: Context Loaded

```
╔══════════════════════════════════════════════════════════════╗
║  EVENT STORMING                                Step 0       ║
╠══════════════════════════════════════════════════════════════╣
║                                                              ║
║  Journey:    J{n} {name}                                     ║
║  Capability: C{n} {name}                                     ║
║  Features:   F{n}, F{m} (from journey pain points)           ║
║  Stories:    US{n}, US{m} ({total} AC scenarios)              ║
║  US Coverage: ✅ complete / ⚠️ partial ({n} gaps)             ║
║  Personas:   P{n} {name}, P{m} {name}                        ║
║                                                              ║
║  Status: Context loaded. Starting extraction...              ║
╚══════════════════════════════════════════════════════════════╝
```

---

## Step 2: Quick Confirmation

```
╔══════════════════════════════════════════════════════════════╗
║  EVENT STORMING                                Step 2       ║
╠══════════════════════════════════════════════════════════════╣
║                                                              ║
║  Extracted from User Stories and Journeys:                   ║
║                                                              ║
║  Events:    {list}                              ({count})    ║
║  Commands:  {list}                              ({count})    ║
║  Read Models: {list}                            ({count})    ║
║  External Systems: {list}                       ({count})    ║
║  Policy Candidates: {list}                      ({count})    ║
║                                                              ║
║  Complexity Assessment:                                      ║
║    Likely aggregates: {count}                                ║
║    Cross-aggregate policies: {yes/no}                        ║
║    → Recommended path: {Fast-Path / Interactive}             ║
╚══════════════════════════════════════════════════════════════╝
```

---

## Step 2.5: Bounded Context Framing

```
╔══════════════════════════════════════════════════════════════╗
║  BOUNDED CONTEXT FRAMING                     Step 2.5       ║
╠══════════════════════════════════════════════════════════════╣
║                                                              ║
║  Bounded Contexts identified:                                ║
║                                                              ║
║  BC1: {Name} (from C{n} {CapabilityName})                    ║
║    Events:   {events belonging to this BC}                   ║
║    Commands: {commands belonging to this BC}                 ║
║                                                              ║
║  BC2: {Name} (from C{m} {CapabilityName})       [if multi]   ║
║    Events:   {events belonging to this BC}                   ║
║    Commands: {commands belonging to this BC}                 ║
║                                                              ║
║  Context Map:                                                ║
║    {BC1} ←[ACL]— {ExternalSystem}                            ║
║    {BC1} —[OHS]→ {BC2}                                       ║
║                                                              ║
║  Language check: {passed / N terms diverge}                  ║
╚══════════════════════════════════════════════════════════════╝
```

---

## Step 3a: Fast-Path Confirmation

```
╔══════════════════════════════════════════════════════════════╗
║  EVENT STORMING — FAST PATH                    Step 3a      ║
╠══════════════════════════════════════════════════════════════╣
║  Proposed Domain Model:                                      ║
║                                                              ║
║  Aggregate: {Name1} (root: {Entity}, id: {IdType})           ║
║    Commands: {list}                                          ║
║    Events: {list}                                            ║
║    Invariants: {list}                                        ║
║                                                              ║
║  Aggregate: {Name2} ...                                      ║
║                                                              ║
║  Policy: When {Event} → {Action}                             ║
║                                                              ║
║  Read Model: {Name}View (updated by: {events})               ║
╚══════════════════════════════════════════════════════════════╝
```

---

## Step 3b: Interactive Elicitation — Aggregate Boundaries

```
╔══════════════════════════════════════════════════════════════╗
║  EVENT STORMING — AGGREGATE BOUNDARIES         Step 3b      ║
╠══════════════════════════════════════════════════════════════╣
║                                                              ║
║  ✅ Events:     {confirmed_count} confirmed                  ║
║  ✅ Commands:   {confirmed_count} confirmed                  ║
║  🔍 Aggregates: determining boundaries...                    ║
║  ⏳ Policies:   pending                                      ║
║  ⏳ Invariants: pending                                      ║
╚══════════════════════════════════════════════════════════════╝
```

---

## Step 4: Domain Model Formalization

```
╔══════════════════════════════════════════════════════════════╗
║  DOMAIN MODEL FORMALIZATION                    Step 4       ║
╠══════════════════════════════════════════════════════════════╣
║                                                              ║
║  Bounded Context: {ContextName}                              ║
║    → {bc-slug}/README.md                                     ║
║                                                              ║
║  Aggregates written:                                         ║
║    {Agg1} → {bc-slug}/aggregate-{agg1-slug}.md               ║
║    {Agg2} → {bc-slug}/aggregate-{agg2-slug}.md               ║
║                                                              ║
║  Summary:                                                    ║
║    Entities: {count}  |  VOs: {count}  |  Commands: {count}  ║
║    Events: {count}    |  Services: {count}                   ║
║    Read Models: {count}                                      ║
║    PlantUML: ✅ Domain Overview in README.md                  ║
║                                                              ║
║  Hotspots Resolved: {resolved}/{total}                       ║
╚══════════════════════════════════════════════════════════════╝
```

---

## Step 5: Completion

```
╔══════════════════════════════════════════════════════════════╗
║  EVENT STORMING COMPLETE                       Step 5       ║
╠══════════════════════════════════════════════════════════════╣
║                                                              ║
║  ✅ context-map.md                                           ║
║  ✅ event-storming-{journey-slug}.md                         ║
║  ✅ {bc-slug}/ (×{bc_count} BCs, {agg_count} aggregates)    ║
║                                                              ║
║  Domain model ready for implementation                       ║
╚══════════════════════════════════════════════════════════════╝
```
