# Mockup Calibration Protocol

Use low-fi mockup thinking to surface AC gaps in user stories that involve user-facing interfaces. This is a calibration tool, not a design phase — the goal is to discover missing AC, not to produce a design artifact.

## Entry Gate

Silently assess: do any stories in the current batch involve a user-facing interface (dashboard, form, notification panel, map view, list/table, etc.)?

- **All stories are backend/API/data** → skip this protocol entirely, return to main flow.
- **UI stories detected + context already indicates UI focus** (e.g., user explicitly stated UI interaction scope) → skip Level 1, proceed directly to Level 2.
- **UI stories detected but unclear if user wants mockup calibration** → proceed to Level 1.

## Level 1 — Offer (only when UI intent is ambiguous)

> "这几个故事涉及用户界面交互。如果你能描述一下关键界面的大致布局（文字描述或手绘草图都行），可以帮我发现遗漏的交互状态和边界情况。要试试吗？不做也完全没问题，我们直接进入验证。"

If the user declines → return to main flow (Step 5). Don't push.

## Level 2 — Guide (if user engages)

Two paths depending on tooling availability:

### Path A — Visual (brainstorming Visual Companion available)

If the brainstorming skill's **Visual Companion** is installed (browser-based wireframe tool from Superpowers), offer it:

> "我可以在浏览器里画一个简单的线框草图给你看，你直接标注哪里有问题或遗漏，比纯文字描述更直观。要试试吗？"

If the user accepts:

1. Pick the most complex UI story from the batch
2. Use Visual Companion to generate a low-fi wireframe showing:
   - Key areas / modules on the screen
   - Primary action buttons and their positions
   - Data display zones (lists, tables, maps, charts)
   - Navigation elements
3. Present the wireframe and ask the user to review:
   - "哪里不对？"
   - "缺了什么？"
   - "这些区域在各种状态下应该怎么显示？"
4. Walk through each area using the **6-State Checklist** below

If the user declines Visual Companion → fall through to Path B.

### Path B — Text (default fallback)

Ask about the most complex UI story first. One question at a time:

1. "这个界面上主要有哪几个区域/模块？"
2. "用户进来第一眼看到的是什么？主操作在哪？"
3. "数据还没加载出来时显示什么？没有数据时呢？"

From the user's text description, mentally construct the layout, then walk through each described area using the **6-State Checklist** below.

## 6-State Checklist

Both paths converge here. For each UI area identified (from wireframe or text), check whether the generated stories' AC cover these states:

| UI State | What to check | Example question |
|---|---|---|
| **Loading** | 数据加载中显示什么？骨架屏？转圈？进度条？ | "车辆列表加载时用户看到什么？" |
| **Empty** | 没有数据/结果时显示什么？空白？引导？ | "搜索结果为空时显示什么？" |
| **Error** | 请求失败/超时时怎么提示？能重试吗？ | "API 调用失败时用户看到什么？" |
| **Overflow** | 数据量很大时怎么处理？分页？虚拟滚动？聚合？ | "1000 辆车在地图上怎么显示？" |
| **Partial** | 部分数据到了、部分没到怎么显示？ | "位置数据到了但设备状态还在加载？" |
| **Permission** | 用户没权限看到某些内容时怎么处理？ | "普通用户看不到管理员功能时界面什么样？" |

### How to use the checklist

For each state:
1. Check if the current AC already covers it → if yes, move on
2. If not covered, ask the user what the expected behavior is
3. If the user confirms it matters → draft a new AC scenario and propose adding it to the story
4. If the user says "不重要" or "后面再说" → note as Open Question, don't force

**Don't exhaustively apply all 6 states to every UI area.** Use judgment — a simple form probably doesn't need Overflow or Partial checks. A data-heavy dashboard probably needs all six.

## Multiple UI Stories

If the batch has several UI stories, don't run the full checklist on each one. Prioritize:

1. **Most complex UI** — run full checklist
2. **Similar UI patterns** — "这几个都是列表类界面，同样的状态处理规则适用吗？" → batch confirm
3. **Simple UI** — "这个比较简单，状态处理有什么特殊的吗？" → one question covers it

## Level 3 — Feed Back

After running the checklist, compile findings:

> "从界面描述中，我发现这些情况 AC 还没覆盖：
> 1. [US-X] 缺少空状态场景 — 车辆列表为空时用户看到什么？
> 2. [US-Y] 缺少加载状态 — 地图渲染 100+ 车辆时的等待体验
>
> 要补充进去吗？"

If yes → add missing scenarios to the relevant stories' AC.
If no → note as Open Questions in the output file.

## Output

This protocol does NOT produce a design artifact for its own sake, but the mockup created during calibration has reference value for downstream implementation.

### Save mockup (if generated)

If a mockup was produced during Level 2 (either path), save it alongside the user stories:

**Path A (Visual Companion):** Save the wireframe HTML to `product-management/mockups/{feature-name}-wireframe.html`

**Path B (Text):** Save the layout description to `product-management/mockups/{feature-name}-layout.md` using this minimal format:

```markdown
# Mockup: {Feature Name}

**Related Stories**: US-{n}, US-{n}, ...

## Layout

[界面区域描述 — 从 Level 2 对话中整理]

## States Discussed

| Area | Loading | Empty | Error | Notes |
|---|---|---|---|---|
| [区域 1] | [描述] | [描述] | [描述] | [补充说明] |
| [区域 2] | ... | ... | ... | ... |

---
*Created: [YYYY-MM-DD]*
```

Only save when the mockup discussion produced substantive content. A one-sentence answer like "就一个简单表格" doesn't warrant a file.

### AC findings

Regardless of whether the mockup is saved:
- Additional AC scenarios added to existing stories
- New edge-case stories split from existing ones
- Open Questions noted for later resolution
