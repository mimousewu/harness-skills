# Vision Template

```markdown
# Vision: [Product Name]

## Personas

| ID | 角色 | 类型 | 文件 |
|---|---|---|---|
| P1 | [角色名] | 用户角色 | personas/P1-{name}.md |
| P2 | [角色名] | 审查角色 | personas/P2-{name}.md |

## Business Problems

- [用户当前面临的痛点 1]
- [用户当前面临的痛点 2]
- [现有方案的不足]

## Core Capabilities

- [我们提供的能力 1]
- [我们提供的能力 2]

## Core Values

- [核心价值主张 1]
- [核心价值主张 2]

## Differentiation

- [与现有方案/竞品的差异化 1]
- [与现有方案/竞品的差异化 2]

## Product Type

[产品交付形式：Web 应用 / 移动应用 / 数据服务 (API) / 平台 / SDK / 硬件 / 混合]
[交付边界：什么不提供]

## Product Strategy

[服务分层策略（仅数据服务类产品）]
[L1（统计服务）与 L2（原始数据）的关系]

## Success Metrics

- [指标 1]: [目标值]（例：车辆在线率 > 99%）
- [指标 2]: [目标值]（例：异常事件响应时间 < 5min）

## Non-Goals / 边界排除

- [明确不做的事项 1]
- [容易被误认为在范围内但不做的事项 2]

## Constraints（约束条件）

- [技术约束：必须兼容现有 XXX 系统]
- [业务约束：数据延迟不超过 30 秒]

## Assumptions（前提假设）

- [假设 1：车辆已安装 T-Box 设备]
- [假设 2：客户有稳定的网络连接]

## Capabilities

| ID | Name | Priority | Status |
|---|---|---|---|
| C1 | [Name] | P0 | Planning |
| C2 | [Name] | P1 | Planning |

## Status

Active | Archived

---

*Created: [YYYY-MM-DD]*
*Updated: [YYYY-MM-DD]*
```

## Fields

| Field | Description | Required |
|-------|-------------|----------|
| Personas | Index table referencing persona detail files | Yes |
| Business Problems | Current pain points users are facing | Yes |
| Core Capabilities | High-level capability statement | Yes |
| Core Values | Core value proposition | Yes |
| Differentiation | Unique value vs competitors/existing solutions | Yes |
| Product Type | How the product is delivered to customers | Yes |
| Product Strategy | Service layer strategy (L1/L2) | Data service products only |
| Success Metrics | Measurable targets that define success | Yes |
| Non-Goals | Explicit boundary exclusions | Yes |
| Constraints | Technical/business constraints | Yes |
| Assumptions | Preconditions assumed to be true | Yes |
| Capabilities | Reverse index of Capabilities (auto-generated in Step 5) | Yes |
| Status | Active or Archived | Yes |

## Notes

- **Personas** table only contains index info — detail lives in `personas/P{n}-{name}.md`
- **Capabilities** table is generated from Step 5 output, creating a bidirectional reference (Vision ↔ Capability)
- **Product Strategy** is optional and only relevant for data service products. Omit the section entirely for non-data products.
- **Non-Goals** are critical for downstream Agent work — they define "what is NOT good work"
