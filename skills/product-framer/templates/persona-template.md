# Persona Template

```markdown
# Persona: [P-ID] [角色名]

## 类型

用户角色 | 审查角色

## 目标

- [目标 1]
- [目标 2]

## 日常工作

[一天的工作是什么样的，典型流程。写 2-3 句话描述日常，不需要长篇大论]

## 当前工具/系统

- [工具/系统 1]：[用途]
- [工具/系统 2]：[用途]

## 痛点

- [什么让他沮丧/低效 1]
- [什么让他沮丧/低效 2]

## 驱动力

- [什么让他有成就感/什么驱动他的决策 1]
- [什么驱动他的决策 2]

## 典型表达

- "[用户在对话中的原话或典型表达]"

## 关联 Capabilities

[C-ID list — 在 Step 5 时填充]

---

*Created: [YYYY-MM-DD]*
*Updated: [YYYY-MM-DD]*
```

## Fields

| Field | Description | When Filled |
|-------|-------------|-------------|
| 类型 | 用户角色 (functional needs) or 审查角色 (non-functional: security, reliability, compliance) | Step 1.5 |
| 目标 | What this persona wants to achieve | Step 1.5 |
| 日常工作 | Brief description of daily workflow | Step 1.5 |
| 当前工具/系统 | Tools and systems currently in use | Step 1.5 |
| 痛点 | What frustrates or slows them down | Step 1.5 |
| 驱动力 | What motivates their decisions, what gives them satisfaction | Step 1.5 |
| 典型表达 | Direct quotes or characteristic expressions from conversation | Step 1.5 |
| 关联 Capabilities | C-ID references (back-link from Capabilities) | Step 5 |

## Design Rationale

This is **not** a UX Persona card. No photos, demographics, fictional names. Only information that downstream skills actually consume:

| Consumer | Fields Used |
|----------|------------|
| product-framer Step 3 | 目标, 痛点 → Capability decomposition dimension selection |
| journey-designer | 日常工作, 当前工具, 痛点 → Phase enrichment and touchpoint definition |
| journey-designer | 驱动力 → Emotion tagging and Opportunity prioritization |
| user-story | (indirect via Journey/Feature) |

Every field earns its place by being consumed downstream. If a field isn't consumed, it doesn't belong here.

## Persona Type Guidelines

### 用户角色 (User Role)

Defines functional requirements — what the product must do:
- Fleet manager, driver, dispatcher, customer service agent
- Focus: daily tasks, workflow, operational pain points

### 审查角色 (Review Role)

Defines non-functional requirements — what "good work" looks like:
- Security auditor, reliability engineer, compliance officer, data privacy officer
- Focus: quality gates, constraints, standards, regulations

Harness methodology emphasizes review roles because they define constraints that downstream Agent implementation must respect. Without them, Agents only optimize for functional correctness and miss security/reliability/compliance requirements.
