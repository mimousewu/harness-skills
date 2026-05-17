# Phase Enrichment Protocol

Rules for expanding a Journey Phase from a one-line name into a detailed behavioral description. This protocol is the core analytical engine of journey-designer, migrated and adapted from scene-mapper's situation-behavior-problem framework.

## The Enrichment Sequence

For each Phase in a Journey, follow this sequence:

```
1. Draft Steps (Agent generates, user confirms)
2. Discover Problems (per confirmed step)
3. Apply Layered Display (user / touchpoint / product support)
4. Tag Emotion (anchored to Persona)
```

## Step 1: Draft Steps

The agent generates step drafts based on three sources:

1. **Persona's daily workflow** — how does this role typically work?
2. **Persona's current tools** — what systems do they interact with?
3. **~S activity descriptions** — what activities did product-framer identify?

Draft 3-6 steps per Phase. Each step should be a concrete user action, not an abstract goal.

**Good steps:**
- "打开车辆监控系统首页"
- "在地图上筛选目标区域"
- "点击车辆图标查看详情"

**Bad steps (too abstract):**
- "了解车辆状态" — this is a goal, not a step
- "分析数据" — what data? how?

Present the draft:

> "基于 [Persona] 的日常工作和当前工具，我推断 Phase [N] 的步骤是：
> 1. [步骤 1]
> 2. [步骤 2]
> 3. [步骤 3]
>
> 哪里需要修正？有遗漏的步骤吗？"

**If user corrects significantly** — the draft was wrong. Restate the corrected version and confirm.
**If user says "差不多"** — proceed to problem discovery on each step.
**If user adds steps** — incorporate and confirm the expanded list.

## Step 2: Discover Problems

For each confirmed step, probe for friction:

> "在 [步骤 X: 具体描述] 这一步，[角色] 现在做的时候有什么困难、不便、或者容易出错的地方吗？"

### Problem Discovery Patterns

Draw from these categories when probing (don't ask about all — use judgment):

| Category | What to listen for | Example question |
|---|---|---|
| **Efficiency friction** | Too many clicks, too slow, manual work | "这一步大概要花多长时间？" |
| **Information gap** | Missing data, wrong granularity, stale data | "做这一步的时候信息够用吗？" |
| **Tool switching** | Context switch between systems | "需要切换到其他系统吗？" |
| **Error-prone steps** | Easy to make mistakes, no undo | "这一步容易出错吗？出错了怎么办？" |
| **Waiting/blocking** | Dependency on others, async handoff | "这一步需要等谁或等什么吗？" |
| **Workarounds** | Excel, WeChat groups, sticky notes | "有没有什么变通办法在用？" |

### Hard Rules

- **Problems must come from behaviors.** If the user hasn't described doing the step, you cannot claim there's a problem in it. "I think users might struggle with X" is fabrication.
- **"没什么问题" is a valid answer.** Not every step has friction. Accept it and move on. Don't push for problems that don't exist.
- **Maximum 2 attempts per step.** If the first question yields nothing, try one different angle (e.g., switch from "困难" to "耗时"). After 2 attempts, move on.
- **Record the problem at the step level.** Every problem must be attached to a specific step, not floating at the Phase level.

## Step 3: Apply Layered Display

For each Phase (after steps and problems are confirmed), structure the information into three layers:

### User Action Layer
What the person physically does — decisions, clicks, calls, observations.

### System Touchpoint Layer
What existing tools/systems are involved. **Must cross-reference Persona's "当前工具/系统" field.** Don't invent touchpoints that aren't in the Persona's tool list.

If a step involves a tool not in the Persona file, ask:

> "这一步用到了 [工具名]，但 Persona 的工具列表里没有。是遗漏了还是这个工具只在特定情况下使用？"

### Product Support Layer
What our product currently provides at this step. Three possibilities:
- **Existing capability** — our product already supports this. Describe the interaction path: what the user triggers → what our product does → what result comes back. For example: "用户点击刷新 → 平台通过 API 拉取最新位置 → 2分钟延迟后地图更新". This interaction path is critical context for downstream Agent development — it documents how users interact with our APIs and services.
- **Partial** — our product covers part of this step. Describe what's covered and what's not.
- **"无"** — greenfield, our product doesn't touch this step yet

**Critical:** Don't put aspirational capabilities here. This is the as-is state. What our product *should* provide belongs in Features (Step 3). The interaction path describes how things work TODAY, not how they should work.

## Step 4: Tag Emotion

After a Phase's steps, problems, and layers are confirmed, assign one emotion tag:

| Tag | Meaning | When to use |
|---|---|---|
| **↑ 正向** | User feels in control, efficient, satisfied | Steps flow smoothly, tools work well |
| **→ 中性** | Routine, no strong feeling | Standard process, neither good nor bad |
| **↓ 负向** | Frustrated, anxious, blocked | Friction points, workarounds, time pressure |

**Anchoring rule:** The emotion tag must connect to Persona data:
- ↓ negative → reference a Persona pain point or a discovered problem
- ↑ positive → reference a Persona driver being satisfied
- → neutral → no strong signal from Persona context

Format: `↓ 负向 — 需要切换三个系统才能确认一辆车的状态（对应 Persona 痛点: 信息分散）`

Don't guess emotions. If there's no clear signal, use → 中性.

## Pace Control

### Normal pace (default)
One Phase at a time. Complete all 4 sub-steps (draft → problems → layers → emotion) before moving to the next Phase.

### Fast pace (user signals "快一点" or answers are brief and confident)
Draft 2-3 Phases at once. Let user batch-confirm/correct. Still probe for problems per step, but combine the layered display into the initial draft.

### Slow pace (user is unsure, gives long answers, asks for clarification)
Break steps into finer granularity. Spend more time on problem discovery. Add mid-conversation calibration after every Phase (not every 2-3).

Read the user's communication style and adjust. Don't force a pace.

## Common Pitfalls

| Pitfall | Why it happens | Fix |
|---|---|---|
| **Fabricating problems** | Agent fills silence with imagined friction | Only record problems the user confirms |
| **Aspirational touchpoints** | Agent writes "our dashboard" as a current touchpoint | Only list tools from Persona's current tool list |
| **Abstract steps** | Agent writes goals instead of actions | Rewrite: "了解状态" → "打开系统首页查看车辆列表" |
| **Skipping "无问题" steps** | Agent assumes every step must have a problem | Accept "没问题" — not all steps are friction points |
| **Emotion fabrication** | Agent writes "用户可能感到焦虑" without evidence | Anchor to Persona data or discovered problems |
