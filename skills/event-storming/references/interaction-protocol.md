# Interaction Protocol: Steps A-E

> Detailed protocol for interactive Event Storming elicitation. Referenced by SKILL.md Steps 1-3.

---

## Step A: Autonomous Extraction (No User Input)

### Extraction Rules

**From User Story AC — Event extraction:**

| AC Pattern | Extracted Event | Example |
|-----------|----------------|---------|
| "Then a {noun} is created/issued/generated" | `{Noun}Created` / `{Noun}Issued` | "Then a JWT token is issued" → `TokenIssued` |
| "Then the {noun} is updated/modified/changed" | `{Noun}Updated` | "Then the position is updated" → `PositionUpdated` |
| "Then the {noun} is deleted/removed/archived" | `{Noun}Deleted` / `{Noun}Archived` | "Then the alert is archived" → `AlertArchived` |
| "Then an error/alert/notification is {verb}" | `{Noun}{Verb}` | "Then an alert is triggered" → `AlertTriggered` |
| "Then the user is redirected/notified/informed" | `{Actor}{Notified}` | "Then the manager is notified" → `ManagerNotified` |

**From User Story — Command extraction:**

| Story Pattern | Extracted Command | Example |
|--------------|------------------|---------|
| "I want to {verb} a {noun}" | `{Verb}{Noun}` | "I want to update position" → `UpdatePosition` |
| "I want to {verb} {noun} {noun}" | `{Verb}{Noun}{Noun}` | "I want to define a geofence" → `DefineGeofence` |
| "I want to see/view/monitor {noun}" | Read Model, not Command | "I want to see vehicle positions" → `VehiclePositionView` |

**From User Story — Read Model extraction:**

| Story Pattern | Extracted Read Model | Example |
|--------------|---------------------|---------|
| "So that I can {see/monitor/track} {noun}" | `{Noun}View` | "So that I can monitor fleet operations" → `FleetOperationsView` |
| "So that I can {verb} based on {noun}" | `{Noun}View` | "So that I can respond to alerts" → `AlertDashboardView` |

**From Journey — External System extraction:**

| Journey Element | Extracted | Example |
|----------------|-----------|---------|
| System touchpoint in "系统触点" layer | External System + Provider | "Telematics API" → `TelematicsAPI` + `TelematicsProvider` |
| Pain point: "switches between N systems" | Integration opportunity | Note as policy candidate |

### What NOT to Extract

- Technical events ("DatabaseUpdated", "CacheFlushed", "MessagePublished")
- Infrastructure commands ("DeployService", "ScaleCluster")
- UI-only actions with no domain impact ("ToggleSidebar", "SortTable")

---

## Step B: Quick Confirmation

### Presentation Format

Present as a bullet list (not tables — tables are for the final output):

```
I extracted the following from US3, US4, and J1:

**Events (3):** PositionUpdated, GeofenceViolated, AlertCreated
**Commands (3):** UpdatePosition, DefineGeofence, CreateAlert
**Read Models (2):** VehicleTrackingView, AlertDashboardView
**External Systems (1):** TelematicsAPI → TelematicsProvider
**Policy Candidates (2):** 
  - "Vehicle exits geofence" → alert (from J1.Phase3.PainPoint#2)
  - "Position data stale" → mark stale (from J1.Phase5.PainPoint#1)

Does this look right? Anything missing or wrong?
```

### Handling User Response

| Response | Action |
|----------|--------|
| "Looks good" / "Yes" | Assess complexity → Step 3a or 3b |
| "Missing {X}" | Add to extraction, re-present |
| "{X} is wrong" | Correct, re-present |
| "What about {tangent}?" | Park if off-topic, address if relevant |
| Long explanation | Extract threads, confirm understanding, then proceed |

---

## Step C: Complexity Assessment

### Decision Matrix

| Factor | Weight | Fast-Path Signal | Interactive Signal |
|--------|--------|-----------------|-------------------|
| Aggregate count | High | 1-2 likely aggregates | 3+ likely aggregates |
| Cross-aggregate policies | High | No policies span aggregates | Policies trigger across aggregates |
| Invariant clarity | Medium | All invariants obvious from AC | AC doesn't cover key business rules |
| User signal | High | "It's straightforward" | "It's complicated" / asks questions |
| External system count | Low | 0-1 providers | 2+ providers with complex failure modes |

**Default: Interactive.** Only use Fast-Path when ALL signals point to simple.

---

## Step D: Interactive Elicitation Details

### D1: Aggregate Boundary Questions

**Goal:** Determine which commands MUST be transactionally consistent.

**Question template:**
```
I see commands {Cmd1} and {Cmd2} that both touch {concept}.

Option A: Same aggregate — {reasoning for togetherness}
Option B: Separate aggregates — {reasoning for separation}

Which feels right for your domain? Can {action A} happen independently of {action B}?
```

**Boundary heuristics (AI uses internally, not shown to user):**
- Commands with different update frequencies → likely different aggregates
- Commands with different actors → consider different aggregates
- Commands that must see each other's effects → same aggregate
- If deleting one concept should delete another → same aggregate (cascade)
- If concepts have independent lifecycles → different aggregates

### D2: Invariant Discovery Questions

**Goal:** Surface business rules not captured in User Story AC.

**Question template (domain-specific probes):**
```
For the {Aggregate} aggregate, I found these invariants from the AC:
{list existing}

Are there other rules that must ALWAYS hold?
For example:
- {probe based on domain knowledge}?
- {probe based on relationship to other aggregates}?
- {probe based on temporal/state constraints}?
```

**Probe generation strategy:**
- Look at aggregate relationships → "Can a {child} exist without a {parent}?"
- Look at Value Object constraints → "What makes a {VO} invalid?"
- Look at state transitions → "Can a {aggregate} go from {state A} directly to {state C}?"
- Look at quantities → "Is there a maximum number of {children}?"

**2-attempt limit per aggregate.** After 2 rounds, mark remaining as "TBD in Hotspots."

### D3: Policy Rule Questions

**Goal:** Determine exact reactive business rules.

**Question template:**
```
The Journey shows {pain point description}. I'm proposing a {PolicyName}:
when {Event} → {what should happen?}

Some options:
- {option A — immediate}
- {option B — batched/debounced}
- {option C — conditional}

What matches your business needs?
```

### D4: Ubiquitous Language Validation

**Goal:** Ensure domain terms match what the team actually uses.

**Question:**
```
Here's the domain vocabulary I've assembled. These terms will appear in
code. Are they what your team uses?

| Term | My Definition |
|------|--------------|
{terms}

Any corrections? Different names you prefer?
```

---

## Step E: Final Review

Present complete Event Storming output (all tables) and ask:

```
Here's the complete Event Storming result:
{full output tables}

Hotspots remaining: {list or "none"}

Ready to formalize into a Domain Model, or anything to adjust?
```
