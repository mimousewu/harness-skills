# Capability Template

```markdown
# Capability: [{C-ID}] [Name]

## User Goal

[用户使用这个 Capability 要达成什么目标 — 具体、从用户视角描述]

## Service Personas

[P-ID] - [角色名], [P-ID] - [角色名]

## Core Values Contributed

- [Core Value 1]
- [Core Value 2]

## Scope

**Includes:**
- [能力 1]
- [能力 2]

**Excludes:**
- [排除项 1 — 说明属于哪个 Capability 或为什么排除]

## Dependencies

[C-ID list 或 None]

## Priority

[P0 / P1 / P2]

## Priority Justification

[为什么是这个优先级]

## Conceptual Scenes（概念化场景）

| ID | 场景名称 | 触发情境 | 关键活动 |
|---|---|---|---|
| ~S1 | [名称] | [什么情境触发这个场景] | [2-3 个用户会做的活动] |
| ~S2 | [名称] | [触发情境] | [关键活动] |

> `~S` 前缀表示概念化 Scene，进入 journey-designer 后正式化为 Journey Phases。
> journey-designer 完成后，本节替换为正式 Journeys 引用。

## Feature Candidates（候选功能）

| ID | 功能名称 | 一句话描述 | 来源 |
|---|---|---|---|
| ~F1 | [名称] | [功能描述] | [Scope.Includes / ~S 推导] |
| ~F2 | [名称] | [功能描述] | [来源] |

> `~F` 前缀表示候选 Feature，进入 journey-designer 后正式编号为 F{n}。
> journey-designer 完成后，本节替换为正式 Features 引用。

## Status

Planning | In Progress | Completed

## Service Vision

[V-ID]
```

## Fields

| Field | Description | Source |
|-------|-------------|--------|
| User Goal | Concrete user-facing goal (not abstract "description") | Step 3c |
| Service Personas | P-ID references to persona files | Step 3c |
| Core Values Contributed | Which Vision values this Capability delivers | Step 4 |
| Scope | What's included/excluded | Step 3c |
| Dependencies | Preceding Capabilities | Step 4 |
| Priority | P0/P1/P2 with justification | Step 4 |
| Conceptual Scenes | 2-3 ~S entries validating Capability boundaries | Step 3d |
| Feature Candidates | ~F entries derived from Scope and ~S | Step 3e |
| Status | Current workflow stage | Step 5 |
| Service Vision | V-ID back-reference | Step 5 |

## Lifecycle of ~S and ~F

### Before downstream skills

Capability files contain `Conceptual Scenes` and `Feature Candidates` tables with `~S` / `~F` prefixed IDs.

### After journey-designer completes

Replace `Conceptual Scenes` section with:

```markdown
## Journeys

| ID | Journey Name | File |
|---|---|---|
| J1 | [名称] | journeys/J1-{name}.md |
| J2 | [名称] | journeys/J2-{name}.md |
```

### After journey-designer completes (Features)

Replace `Feature Candidates` section with:

```markdown
## Features

| ID | Feature Name | File |
|---|---|---|
| F1 | [名称] | features/F1-{name}.md |
| F2 | [名称] | features/F2-{name}.md |
```

### After US archival (capability-feature-tracker)

Append `Delivered Features` section:

```markdown
## Delivered Features

| Feature | Problem Solved | Delivered Value | Archived US |
|---------|---------------|-----------------|-------------|
| [Feature 1] | [Problem] | [Value] | [US-ID] |
```

> This section is managed by `capability-feature-tracker`, not by product-framer.

## Notes

- **User Goal** replaces the old "Description" field. It must be concrete enough that a user reading it can say "yes, that's what I want to achieve" or "no, that's not it."
- **Conceptual Scenes** are a validation tool: if you can't think of 2-3 usage scenarios for a Capability, it's either too abstract or too narrow.
- **Feature Candidates** make Scope.Includes concrete — they're easier for users to confirm than abstract capability statements.
