# Elicitation Questions

Architecture decision questions asked during Step 2 (Gap Assessment). Questions are in Chinese (matching Harness business dialogue convention). Maximum 3 questions per SAD generation session.

---

## Question Priority

Questions are asked in priority order. Stop after 3 questions maximum — remaining gaps become `[TBD]`.

| Priority | Question | Trigger Condition |
|----------|----------|-------------------|
| 1 | API Gateway + Auth | No gateway/auth info in any upstream file |
| 2 | Message Middleware | Event-driven patterns found but no middleware specified |
| 3 | Deployment Topology | No deployment info AND defaults insufficient |
| 4 | Database Strategy | Only if data storage mentioned as constraint |

---

## Q1: API Gateway & Authentication (Priority 1)

### Trigger
No API Gateway or authentication mechanism mentioned in:
- `vision.md` Constraints
- Domain model Provider entries
- Any existing architecture documentation

### Question Format

```
┌─────────────────────────────────────────┐
│  已推导完成:                             │
│    ✓ §1 目标与利益相关方                  │
│    ✓ §2 约束                            │
│    ✓ §3 上下文边界                       │
│    ✓ §5 构建块视图                       │
│                                         │
│  需要确认:                               │
│    ⚠ API Gateway 和认证方式 (影响 §3-§8) │
└─────────────────────────────────────────┘
```

**问题：系统的 API Gateway 和认证方式是什么？**

选项:
1. Spring Cloud Gateway + OAuth2.0（Gateway 统一鉴权，下游服务信任 Gateway 传递的 token）
2. Spring Cloud Gateway + JWT（Gateway 校验签名，各服务也独立校验）
3. Kong / Nginx Gateway + OAuth2.0（独立网关，非 Spring 生态）
4. 无 Gateway（服务直接暴露，各自处理认证）

**如果选 1 或 2，追问 token 传播方式（合并在同一轮）：**

> Token 在服务间怎么传递？
> - A: Gateway 解析 token 后通过 Header 传递 userId/roles（下游无需再验证 token）
> - B: 原始 JWT 透传，每个服务独立验证

### Impact Mapping

| Answer | §3 Context | §5 Containers | §6 Sequence | §7 Deploy | §8 Cross-cutting |
|--------|-----------|---------------|-------------|-----------|-----------------|
| SCG + OAuth2 unified | Gateway as entry | Gateway container + Auth Service | Auth flow in sequences | Gateway pod | OAuth2 + token relay pattern |
| SCG + JWT independent | Gateway as entry | Gateway container | JWT verify step per service | Gateway pod | JWT validation pattern |
| Kong/Nginx | External gateway | Gateway as external | Auth delegated | Separate gateway deploy | Gateway-managed auth |
| No gateway | Direct access | No gateway container | Auth per-service | Direct exposure | Per-service auth |

---

## Q2: Message Middleware (Priority 2)

### Trigger
Event-driven patterns detected (domain model has `@DomainEvent` with cross-aggregate Policy subscribers) BUT no middleware technology specified anywhere.

### Question Format

```
┌─────────────────────────────────────────┐
│  已推导完成:                             │
│    ✓ 检测到事件驱动模式                   │
│      - {N} 个跨聚合 Policy               │
│      - {M} 个 Domain Event               │
│                                         │
│  需要确认:                               │
│    ⚠ 事件通过什么中间件传递？             │
└─────────────────────────────────────────┘
```

**问题：领域事件通过什么消息中间件传递？**

选项:
1. Apache Kafka（高吞吐、持久化、适合事件溯源）
2. Spring Cloud Stream 抽象（不指定具体实现，运行时绑定）

### Impact Mapping

| Answer | §5 Containers | §6 Sequence | §7 Deploy | §8 Cross-cutting |
|--------|---------------|-------------|-----------|-----------------|
| Kafka | Kafka cluster container | Async arrows with topic | Kafka StatefulSet | Event sourcing possible, partition strategy |
| Spring Cloud Stream | Broker abstract | Async with channel names | Broker TBD | Binder abstraction layer |

---

## Q3: Deployment Topology (Priority 3)

### Trigger
- No deployment information in any upstream file
- AND the default (K8s) needs confirmation or the project context suggests otherwise

### Question Format

```
┌─────────────────────────────────────────┐
│  已推导完成:                             │
│    ✓ 所有业务相关章节                     │
│                                         │
│  默认假设:                               │
│    Kubernetes 部署                        │
│                                         │
│  需要确认:                               │
│    ⚠ 部署环境确认 (影响 §7)              │
└─────────────────────────────────────────┘
```

**问题：部署环境是否为标准 Kubernetes？有什么特殊约束？**

选项:
1. 标准 K8s（AWS EKS / 阿里 ACK / 自建集群），无特殊约束
2. K8s 但有网络隔离要求（多集群 / 专有网络 / VPN 接入外部系统）
3. 非 K8s 环境（ECS / VM / Docker Compose）
4. 混合部署（部分 K8s + 部分传统 VM）

### Impact Mapping

| Answer | §7 Deployment Diagram | Notes |
|--------|----------------------|-------|
| Standard K8s | Standard K8s diagram (namespace per BC) | Use defaults |
| K8s + network isolation | Add network boundary boxes, VPN tunnels | Show isolation |
| Non-K8s | Host-based deployment diagram | Different notation |
| Hybrid | Split diagram (K8s zone + VM zone) | Show bridge |

---

## Q4: Database Strategy (Priority 4, Confirm Only)

### Trigger
Only asked if Vision.Constraints mentions specific data requirements (e.g., time-series, graph, full-text search) that imply non-standard database choices.

### Question Format

**问题：检测到以下数据需求，数据库选型是什么？**

（列出触发条件中发现的特殊数据需求）

选项:
1. 统一用 MySQL/PostgreSQL（关系型覆盖所有场景）
2. 混合存储（关系型 + {detected need}专用存储）
3. 待定（后续再决定）

---

## Non-Asked Defaults

The following are NEVER asked — they use defaults and are disclosed in §4:

| Concern | Default | Override Signal |
|---------|---------|----------------|
| Service discovery | Consul (current), migrating to K8s native | Never ask |
| Config management | K8s ConfigMap + Secret | "Apollo" or "Nacos Config" in files |
| Logging | ELK / Loki (not specified in SAD) | Only mention if constraint found |
| Monitoring | Prometheus + Grafana (not specified) | Only mention if constraint found |
| CI/CD | Not documented in SAD | Out of scope |
| Code language | Java (Spring Cloud implies) | Other language mentioned |

---

## Question Rules

1. **Maximum 3 questions per session.** After 3, mark remaining gaps as `[TBD]`.
2. **Never ask follow-up questions on the same topic.** Take the answer and move on.
3. **Always show state panel before the question.** User must see what's already done.
4. **Offer concrete options.** No open-ended "what do you prefer?" questions.
5. **Bundle related sub-questions.** Q1 combines Gateway + Auth + Token propagation in one turn.
6. **Skip questions if answer is inferrable.** If `vision.md` says "Kafka" in constraints, don't ask Q2.
