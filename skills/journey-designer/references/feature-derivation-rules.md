# Feature Derivation Rules

Rules for deriving Features from Journey pain points and handling product-framer's `~F` (Feature Candidates). This is the analytical bridge from "where users hurt" to "what we should build."

## The Derivation Chain

Every Feature must be produced through this traceable chain:

```
Pain Point (#N, from Journey)
    ↓
Opportunity ("如果能……就能……")
    ↓
Feature Candidate (name + description)
    ↓
~F Cross-Check (against product-framer's ~F list)
    ↓
Feature (F{n}, formalized)
```

No shortcuts. A Feature without a traced Pain Point is invalid.

## Step-by-Step Process

### 1. Identify Derivable Pain Points

From the Journey's Pain Points Summary, select pain points that pass the Boundary Check (✅ in scope). Pain points marked ❌ (out of boundary) do not generate Features.

### 2. Write Opportunity Statements

For each in-scope pain point, write an Opportunity:

Format: **"如果能 [消除/减轻痛点的方式]，[角色] 就能 [达到的效果/节省的时间/避免的风险]。"**

**Good:**
> "如果能在一个界面看到车辆位置、设备状态和驾驶评分，管理员就不需要切换三个系统，每天节省 30 分钟。"

**Bad:**
> "如果能提供更好的用户体验……" — too vague, not actionable

The Opportunity must be specific enough to imply a feature shape, but not so specific that it prescribes implementation.

### 3. Generate Feature Candidate

Each Opportunity maps to one Feature Candidate:

- **Name**: 2-5 word noun phrase (e.g., "统一车辆监控面板")
- **Description**: One sentence describing what the feature does from the user's perspective
- **Source traceability**: Pain Point number, Phase, Step

### 4. ~F Cross-Check

This is the critical reconciliation step. Compare generated Feature Candidates against product-framer's `~F` list from the Capability file.

Every `~F` must end up in one of three states:

| State | Condition | Action |
|---|---|---|
| **✅ Verified** | A Journey pain point maps to this ~F | Formalize as F{n}. Note: "C{x} ~F{n} verified by J{n} Pain Point #{m}" |
| **❓ Not encountered** | No Journey pain point maps to this ~F | Ask the user explicitly. Two possible outcomes: |
| | → User says "we missed a scenario" | Go back and add the scenario to the Journey |
| | → User says "not needed" | Record: "C{x} ~F{n} — confirmed unnecessary" |
| **➕ New** | Journey reveals a pain point with no matching ~F | Create new F{n}. Note: "Journey-discovered, not in original ~F list" |

**Zero tolerance for silent omission.** If a ~F from the Capability file doesn't appear in the Feature Mapping, the derivation is incomplete. Every ~F must be explicitly accounted for.

### 5. Assign Phase (MVP / Later)

Feature priority derives from the pain point's Criticality:

| Criticality | Default Phase | Rationale |
|---|---|---|
| Fatal (致命) | **MVP** | Without this, the Journey breaks |
| Important (重要) | **MVP** (if effort allows) | Significant value, strong case for MVP |
| Minor (一般) | **Later** | Nice-to-have, defer |

The user can override these defaults.

## Boundary Check: Two-Gate Filter

Every pain point must pass two gates before generating a Feature:

### Gate 1: Capability Scope Check

Is this pain point within the target Capability's `Scope.Includes`?

- Check against the Capability file's Includes/Excludes lists
- If the pain point falls under another Capability's scope → note it as "belongs to C{y}, not C{x}" and don't generate a Feature here
- If it's in Excludes → it's explicitly out of scope

### Gate 2: Product Type Boundary

Is this our product's responsibility, or the customer's existing system's?

Questions to determine:
- Does the friction come from **our product's limitations** → ✅ our responsibility
- Does the friction come from **the customer's existing system** → ❌ not our responsibility
- Does the friction come from **the user's process/habits** → ⚠️ gray area — discuss with user

**Examples:**

| Pain Point | Gate 1 | Gate 2 | Result |
|---|---|---|---|
| "车辆位置刷新慢" | C1-实时监控 ✅ | 数据推送是我们的服务 ✅ | ✅ Generate Feature |
| "T-Box 离线导致数据丢失" | C1-实时监控 ✅ | T-Box 是客户硬件 ❌ | ❌ Out of boundary |
| "ERP 系统导出格式不兼容" | C3-安全报告 ✅ | ERP 是客户系统 ❌ | ❌ Out of boundary |
| "管理员不知道该看哪个指标" | C1-实时监控 ✅ | 用户培训问题 ⚠️ | ⚠️ Discuss — could be UX guidance feature |

For ⚠️ gray area items, present to user:

> "这个痛点 [描述] 可能是用户习惯问题，也可能是我们产品引导不足。你觉得这个是否在产品范围内？"

## Multiple Pain Points → One Feature

Sometimes 2-3 related pain points in the same Phase converge on a single feature. This is normal — group them:

```
Pain Points #1 (切换系统找车辆) + #3 (搜索只支持车牌号)
  → Opportunity: 统一搜索入口 + 多维度筛选
  → Feature: F1 智能车辆搜索
```

When grouping, the Feature's Criticality inherits the **highest** Criticality among the grouped pain points.

## One Pain Point → Multiple Features

Rarely, a single large pain point requires multiple features to address. This is a signal the pain point might be too broad — try to decompose it first. If it genuinely needs multiple features, create them and note the shared source:

```
Pain Point #2 (异常发现到处理需要 15 分钟)
  → F2 实时异常告警推送 (reduces discovery time)
  → F3 一键事件处理工单 (reduces response time)
```

## Output Format

The Feature Mapping table in the Journey file should look like:

```markdown
## Feature Mapping

| Phase | Pain Point | Feature | ~F Status |
|---|---|---|---|
| Phase 1 | #1 切换系统找车辆 | F1 智能车辆搜索 | C1 ~F1 ✅ verified |
| Phase 1 | #3 搜索只支持车牌号 | F1 智能车辆搜索 | (grouped with #1) |
| Phase 2 | #2 异常发现慢 | F2 实时异常告警 | C1 ~F2 ✅ verified |
| Phase 2 | #2 响应处理慢 | F3 事件处理工单 | New (Journey-discovered) |
| — | — | — | C1 ~F3 设备诊断 ❓ not encountered |
```
