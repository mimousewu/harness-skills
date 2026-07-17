# Persona Template

```markdown
# Persona: [P-ID] [角色名]

## 类型

用户角色 | 审查角色

## 角色背景

[1-2 sentences: who, in what context, what they care about.
Scene-neutral — applies to B2B, B2C, C2C alike.

Example — B2B: Developer at a fleet company, responsible for integrating FMS data into their own system.
Example — B2C: A daily commuter (45 min each way), manages family trips via mobile app.]

## 价值流（Value Stream）

[End-to-end business flow where this role creates value. Each node is a value stage.
Derived via Lean Value Tree: role name → value stream → goals → capabilities (bets).

Derivation:
- User roles: ask "what business outcomes does this role's work ultimately impact?"
- Review roles: ask "what SLA/compliance metrics suffer if this role fails?"

Example — Fleet Manager: vehicle availability → cost control → safety management → breakdown response → compliance audit]

## 目标

- [Goal 1 — business outcome, NOT feature description. Example: "Improve vehicle utilization rate", NOT "Real-time vehicle tracking"]
- [Goal 2]

## 日常工作

[一天的工作是什么样的，典型流程。写 2-3 句话描述日常，不需要长篇大论]

## 当前工具/系统

- [工具/系统 1]：[用途]
- [工具/系统 2]：[用途]

## 痛点

- [Business-level obstacle 1 — e.g. "utilization stats lag 3 days due to delayed location data", NOT UX complaints like "UI is confusing"]
- [Business-level obstacle 2]

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
| 角色背景 | 1-2 sentence context: who, scene, what they care about. Scene-neutral. | Step 1.5 |
| 价值流 | End-to-end business value flow. Derivation basis for goals. | Step 1.5 |
| 目标 | Business-outcome goals derived from value stream stages. NOT feature descriptions. | Step 1.5 |
| 日常工作 | Brief description of daily workflow | Step 1.5 |
| 当前工具/系统 | Tools and systems currently in use | Step 1.5 |
| 痛点 | Business-level obstacles, not UX complaints. Example: "utilization stats lag 3 days due to delayed location data", not "UI is confusing" | Step 1.5 |
| 驱动力 | What motivates their decisions, what gives them satisfaction | Step 1.5 |
| 典型表达 | Direct quotes or characteristic expressions from conversation | Step 1.5 |
| 关联 Capabilities | C-ID references (back-link from Capabilities) | Step 5 |

## Design Rationale

Only fields that downstream skills actually consume:

| Consumer | Fields Used |
|----------|------------|
| product-framer Step 3 | 价值流, 目标, 痛点 → Capability decomposition |
| journey-designer | 角色背景, 日常工作, 当前工具, 痛点 → Phase enrichment and touchpoint definition |
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
