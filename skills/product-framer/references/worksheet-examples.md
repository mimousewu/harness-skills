# Worksheet Examples

Complete examples showing the product-framer workflow with enhanced templates.

---

## Example: Connected Vehicle Fleet Management

### Step 0: Context Intake

User provides: "We have a basic vehicle tracking system. Fleet managers complain about switching between 3 different systems to monitor vehicles."

No formal documents provided → Forward Mode, but with context pre-fill.

### Step 1: Empathize + Open Elicitation

**Phase A — Open Narrative**

Agent: "能告诉我你们目前面临的业务挑战是什么吗？"

User: "我们给物流公司做车联网服务。现在车队管理员每天要盯着好几个系统：一个看车辆位置，一个看设备状态，一个看驾驶行为评分。切来切去特别烦。而且出了问题反应慢，等发现异常的时候司机已经到目的地了。"

**From this narrative, extracted:**
- User: Fleet manager (车队管理员)
- Pain: Information scattered across 3 systems
- Pain: Slow response to anomalies
- Implied user: Driver (司机) — affected but not primary
- Current tools: Vehicle location system, device status system, driving behavior scoring system

**Phase B — Structured Follow-Up**

Agent: "你提到了车队管理员，除了他们还有谁会用这个产品？比如司机本人需要看到什么吗？调度员呢？"

User: "司机不用看，他们只管开车。调度员偶尔会用，主要是管理员。另外客户的安全部门会定期要安全报告。"

Agent: "客户的安全部门定期要报告，那现在这个报告是怎么做的？"

User: "管理员手动从三个系统导数据，拼到 Excel 里。每个月花两三天。"

**[Calibration]**

Agent: "到目前为止我的理解：车队管理员是主要用户，他们在日常监控和异常响应中被三个分散系统拖慢了。安全部门是审查角色，需要定期安全报告但现在靠手工拼 Excel。核心问题是信息分散和响应迟缓。对吗？"

User: "对，差不多。不过还有一点——我们提供的数据延迟现在有 2 分钟，客户觉得太慢。"

### Step 1.5: Persona Confirmation

```
P1 车队管理员 (用户角色)
   目标：实时掌握所有车辆状态，快速处理异常
   日常：早上查看车辆分布，全天监控异常告警，月底出安全报告
   工具：车辆位置系统、设备状态系统、驾驶行为评分系统、Excel
   痛点：三个系统切换、异常发现慢、月报手工拼接
   驱动力：保障车队安全运营、减少事故率

P2 安全审计员 (审查角色)
   目标：定期审查车队安全合规状况
   关注：驾驶行为数据完整性、异常事件处理时效、报告准确性
```

### Step 2: Vision Draft

```markdown
# Vision: 智能车联网服务平台

## Personas

| ID | 角色 | 类型 | 文件 |
|---|---|---|---|
| P1 | 车队管理员 | 用户角色 | personas/P1-fleet-manager.md |
| P2 | 安全审计员 | 审查角色 | personas/P2-safety-auditor.md |

## Business Problems
- 车辆信息分散在 3 个系统，管理员频繁切换
- 异常事件发现和响应滞后（数据延迟 2 分钟）
- 安全报告靠手工从多系统导出拼接，每月耗时 2-3 天

## Core Capabilities
- 统一的车辆状态监控入口
- 实时异常检测与推送
- 自动化安全报告生成

## Core Values
- 一站式监控，告别多系统切换
- 实时响应，缩短异常处理时间
- 自动报告，释放管理员精力

## Differentiation
- 整合位置 + 设备 + 驾驶行为的统一视图
- 亚分钟级数据延迟（目标 < 30 秒）

## Product Type
数据服务（API + Web Dashboard）

## Product Strategy
L1 统计服务为主：提供开箱即用的统计 API 和 Dashboard
L2 原始数据为补充：MQTT 推送原始数据满足定制需求

## Success Metrics
- 车辆在线率 > 99%
- 异常事件响应时间 < 5 分钟
- 安全报告生成时间从 3 天降至 1 小时

## Non-Goals / 边界排除
- 不提供司机端 App（司机不是目标用户）
- 不做路径规划和调度优化（不同产品线）
- 不替代客户的 ERP 或 TMS 系统

## Constraints
- 必须兼容现有 T-Box 设备协议
- 数据存储必须在国内服务器（合规要求）

## Assumptions
- 车辆已安装 T-Box 设备并正常上报
- 客户有稳定的 4G/5G 网络覆盖
```

### Step 3: Collaborative Capability Decomposition

**3a — Dimension Selection**

Agent: "基于 Vision，我看到两种拆法：

**按用户目标拆**：实时监控、异常处理、安全分析
**按数据流程拆**：数据采集、数据展示、数据分析

你觉得哪种更符合业务逻辑？"

User: "按用户目标拆比较好，用户确实是这么用的。"

**3c — Candidates with 3d/3e**

```
C1: 实时车辆监控
    User Goal: 车队管理员在一个界面实时看到所有车辆的位置和状态
    Personas: P1
    Scope:
      Includes: 车辆地图、实时位置、设备在线状态、车辆详情
      Excludes: 异常告警处理、安全报告

    ~S1 日常车辆监控 — 早上开始工作 — 查看车辆分布、检查离线车辆
    ~S2 车辆详情查看 — 需要了解某辆车 — 查看位置、速度、设备状态

    ~F1 车辆地图实时定位 — 地图上显示所有车辆实时位置 — Scope.Includes
    ~F2 设备在线状态面板 — 展示 T-Box 在线/离线/故障状态 — Scope.Includes
    ~F3 车辆详情页 — 单车的完整状态信息 — ~S2

C2: 异常事件处理
    User Goal: 管理员能及时发现异常并快速响应处理
    Personas: P1
    Scope:
      Includes: 异常检测规则、实时告警推送、事件处理工单、处理历史
      Excludes: 日常监控展示、统计报告

    ~S1 异常事件响应 — 系统推送异常告警 — 查看异常详情、联系司机、记录处理
    ~S2 告警规则配置 — 需要调整告警条件 — 创建/修改规则、设定阈值

    ~F1 实时异常告警 — 超速/偏航/设备故障时实时推送 — Scope.Includes
    ~F2 事件处理工单 — 从告警一键创建处理工单 — ~S1
    ~F3 告警规则管理 — 自定义告警条件和阈值 — ~S2

C3: 安全分析报告
    User Goal: 自动生成安全合规报告，满足审计要求
    Personas: P1, P2
    Scope:
      Includes: 驾驶行为统计、安全评分、自动报告生成、历史趋势
      Excludes: 实时监控、异常告警

    ~S1 月度安全报告 — 月底需要出安全报告 — 选择时间范围、生成报告、导出
    ~S2 驾驶行为分析 — 管理员查看某司机驾驶表现 — 查看评分、历史趋势、对比

    ~F1 自动安全报告 — 按模板自动生成月度/季度安全报告 — ~S1
    ~F2 驾驶行为评分看板 — 个人/车队的驾驶行为统计 — ~S2
    ~F3 安全趋势分析 — 历史安全数据趋势图 — Scope.Includes
```

### Step 4: Coverage Check

```
Coverage:
[x] P1 车队管理员 → C1, C2, C3
[x] P2 安全审计员 → C3
[x] 问题：信息分散 → C1 (统一入口)
[x] 问题：响应滞后 → C2 (实时告警)
[x] 问题：手工报告 → C3 (自动报告)
[x] 价值：一站式监控 → C1
[x] 价值：实时响应 → C2
[x] 价值：自动报告 → C3
[x] 差异化：统一视图 → C1
[x] 差异化：亚分钟延迟 → C1, C2
[x] MECE: C1(监控) / C2(异常) / C3(分析) 无重叠 ✓

Priorities:
C1: P0 — 核心基础，所有用户每天使用
C2: P0 — 核心价值差异化，解决最大痛点
C3: P1 — 高价值但使用频率低（月度），可稍后交付

Dependencies:
C2 → C1 (需要车辆数据作为异常检测输入)
C3 → C1 (需要历史数据进行统计分析)
```

### Step 5: Output

**capabilities.yaml (lightweight index):**

```yaml
capabilities:
  - id: C1
    name: 实时车辆监控
    priority: P0
    status: Planning
    file: capabilities/C1-vehicle-monitoring.md
    conceptual_scenes: 2
    feature_candidates: 3
  - id: C2
    name: 异常事件处理
    priority: P0
    status: Planning
    file: capabilities/C2-incident-handling.md
    conceptual_scenes: 2
    feature_candidates: 3
  - id: C3
    name: 安全分析报告
    priority: P1
    status: Planning
    file: capabilities/C3-safety-analytics.md
    conceptual_scenes: 2
    feature_candidates: 3
```

**Files saved:**
- `product-management/vision.md`
- `product-management/personas/P1-fleet-manager.md`
- `product-management/personas/P2-safety-auditor.md`
- `product-management/capabilities.yaml`
- `product-management/capabilities/C1-vehicle-monitoring.md`
- `product-management/capabilities/C2-incident-handling.md`
- `product-management/capabilities/C3-safety-analytics.md`

---

## Example: Reverse Mode (Bottom-Up)

### Step 0: Context Intake

User: "我有一份 feature list，但一直没梳理 Vision 和 Capability。"

Agent reads the feature list:

```
1. 车辆实时定位
2. 设备在线状态查询
3. 超速告警
4. 围栏告警
5. 驾驶评分看板
6. 月度安全报告
7. 车辆轨迹回放
8. 设备故障诊断
```

**Agent extraction:**

```
从 feature list 中提炼：

角色线索：
- 有人在监控车辆 → 管理员角色
- 有安全报告 → 安全/合规角色

问题线索：
- 告警类 feature → 异常检测需求
- 报告类 feature → 审计/合规需求
- 实时定位 + 轨迹回放 → 监控需求

能力聚类：
- 监控类：车辆实时定位、设备在线状态、车辆轨迹回放
- 告警类：超速告警、围栏告警
- 分析类：驾驶评分看板、月度安全报告
- 运维类：设备故障诊断
```

→ Proceed to Step 1 with pre-filled context, focusing Empathize on validating and deepening the extracted understanding.
