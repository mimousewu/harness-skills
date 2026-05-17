# Capability Decomposition Patterns

## Decomposition Dimensions

When presenting decomposition options to the user (Step 3a), choose 2-3 of the following dimensions based on product context. Present them as options, not decisions — the user picks.

### 1. User Role Dimension

Decompose by who uses the product:

- Each role gets Capabilities aligned with their goals
- Works well when roles have clearly different needs
- Example: Fleet managers need monitoring; drivers need navigation; dispatchers need scheduling

**Best for:** Products serving 2+ distinct user roles with minimal goal overlap.

### 2. User Flow / Journey Dimension

Decompose by the user's end-to-end workflow:

- Each stage of the journey becomes a Capability
- Works well for process-oriented products
- Example: Onboarding → Daily Operations → Exception Handling → Reporting

**Best for:** Products where users follow a natural sequence of activities.

### 3. Capability Area Dimension

Decompose by product capability areas:

- Core capabilities (primary value delivery)
- Supporting capabilities (enable core)
- Value-added services (differentiation)

**Best for:** Products with clear primary/secondary capability tiers.

### 4. Value Proposition Dimension

Decompose by core value statements:

- Each value proposition maps to Capabilities that deliver it
- Works well when values are distinct and non-overlapping
- Example: "Efficiency" → automation tools; "Safety" → monitoring + alerts; "Compliance" → audit + reporting

**Best for:** Products where business value is the primary organizing principle.

## How to Present Options

Don't present all four dimensions. Pick 2-3 that fit the product context and explain them using the user's own terminology:

```
"基于 Vision，我看到几种拆法：

**按用户角色拆**：[管理员] 关心 [监控和分析]，[司机] 关心 [导航和任务]
**按用户目标拆**：[实时掌握车辆状态]、[发现和处理异常]、[事后分析改进]

你觉得哪种更符合业务逻辑？或者有其他维度？"
```

## Capability Count Guidelines

- **3-5 Capabilities:** Simple products or MVP
- **5-7 Capabilities:** Medium complexity
- **7+ Capabilities:** Complex products (consider grouping)

If decomposition yields 8+ Capabilities, check whether some should be merged or whether the product scope is too broad.

## Scope Boundary Patterns

### Includes

- Primary user actions in this Capability
- Essential supporting features
- Core integrations required

### Excludes

- Capabilities belonging to other modules
- Advanced features for later phases
- Out-of-scope user actions

The Excludes list is as important as Includes — it prevents scope creep and clarifies boundaries between Capabilities.

## Dependency Patterns

| Pattern | Description | Example |
|---------|-------------|---------|
| Sequential | B depends on A existing | C2 needs C1 data |
| Shared | Multiple Capabilities share a dependency | C2 and C3 both need C1 |
| Optional | Can start alone but improves with another | C4 works but enhances with C2 |
| None | Fully independent | C3 has no dependencies |

## Product Strategy: Service Layer (Data Service Products Only)

For products that deliver data services, define the service layer strategy:

| Layer | Type | Description | Use Case |
|-------|------|-------------|----------|
| **L1 统计服务** | API | Pre-computed statistics | Daily use, no extra computation needed |
| **L2 原始服务** | MQTT/Stream | Raw data push | Custom analytics, data reconciliation |

**Strategy patterns:**

- **L1-primary:** Statistics API as core, raw data as supplement
- **L1 + L2 balanced:** Both out-of-box stats and raw data for customization
- **L2-primary:** Raw data only, customer computes everything

When this applies, each Capability should note which service layer(s) it provides. This affects downstream Feature definition.
