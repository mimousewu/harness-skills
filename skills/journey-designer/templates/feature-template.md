# Feature Template

```markdown
# Feature: [{F-ID}] [Name]

## Description

[一段话描述这个功能做什么 — 从用户视角，不是技术实现]

## Source

- **Capability**: [C-ID] - [Name]
- **Journey**: [J-ID] Phase [N] - [阶段名]
- **~F Reference**: [C{x} ~F{n} Name — ✅ verified] 或 "New (Journey-discovered)"

## Problem Solved

[痛点描述，追溯到 Journey 的具体 Phase 和 Step]
（引用 Pain Points Summary 中的编号，如 "J1 Pain Point #2"）

## User Value

[用户视角的价值陈述 — 解决这个问题后用户能做什么、省多少时间、避免什么风险]

## Scope

**Includes:**
- [功能点 1]
- [功能点 2]

**Excludes:**
- [不包含的内容 — 说明原因或归属]

## Phase

MVP | Later

## Dependencies

[F-ID list 或 None]

## Status

Candidate | Confirmed

---

*Created: [YYYY-MM-DD]*
*Updated: [YYYY-MM-DD]*
```

## Fields

| Field | Description | When Filled |
|-------|-------------|-------------|
| Description | User-facing capability description | Step 3 |
| Source | Traceability chain: Capability → Journey → ~F reference | Step 3 |
| Problem Solved | Specific pain point with Journey step reference | Step 3 |
| User Value | What the user gains | Step 3 |
| Scope | Includes / Excludes boundary | Step 3 |
| Phase | MVP or Later — derived from Criticality | Step 4 |
| Dependencies | Other Features this depends on | Step 4 |
| Status | Candidate (draft) or Confirmed (user-approved) | Step 4 |

## Phase Assignment Rule

Feature priority derives from the pain point's Criticality in the Journey:

| Criticality | → Feature Phase |
|---|---|
| Fatal (致命) | MVP |
| Important (重要) | MVP (if effort allows) or Later |
| Minor (一般) | Later |

## Design Notes

- **No Key Scenarios or Acceptance Hints.** These are the responsibility of the downstream `user-story` skill, which reads the Journey + Feature context and discovers scenarios through its own elicitation process.
- **Source traceability is mandatory.** Every Feature must link back to a specific Journey Phase and Pain Point. A Feature without this chain is invalid.
- **~F Reference records the disposition** of product-framer's Feature Candidates — was it verified by Journey analysis, or is this a new discovery? This maintains the audit trail from upstream `~F` to formal `F{n}`.
