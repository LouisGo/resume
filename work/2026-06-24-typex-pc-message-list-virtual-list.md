---
type: work_item
created_at: 2026-06-24
role: 消息列表优化专项负责人
context: TypeX PC IM 消息列表卡顿、视图跳变与代码耦合
action: 设计并迭代 VirtualListCore / VirtualListBetter，拆分消息数据管理与列表视图容器
result: 支撑提前转正述职，形成后续消息列表重构基础
ai_relevance: assisted
evidence_status: mixed
source_links:
  - "[[inbox/2026-06-24-role-shift-ai-typex-pc-interview-1]]"
  - "[[inbox/converted/2026-06-24-lou-early-regularization]]"
claim_links:
  - "[[resume/claims/2026-06-24-typex-message-list-virtual-list-claim]]"
drill_links:
  - "[[drill/2026-06-24-attack-ai-and-virtual-list-claims]]"
tags:
  - work-item
  - typex-pc
  - virtual-list
  - message-list
---

# TypeX PC 消息列表与虚拟列表专项

## 背景

用户 2025-04-02 入职 TypeX PC 项目，2025-05-16 做提前转正述职，2025-05-20 提前转正。

当时消息列表已简单引入第三方虚拟列表库，但没有针对 IM 场景做优化。核心问题包括：

- 数据获取方式与虚拟列表滚动模型不匹配；
- 滚动卡顿；
- 重渲染粗暴且频繁；
- 不定高长消息、图片等内容容易导致视图跳变；
- 小 bug 多；
- 数据、滚动、消息业务逻辑耦合在少数文件中，只达到“能用”状态。

## 我的角色

候选定位：消息列表优化专项负责人。

用户负责梳理现有消息列表渲染逻辑、制定优化方案、团队宣讲，并在实现层面推动虚拟列表核心库、React 容器、消息数据管理和迁移策略。

## 关键行动

### 1. 先拆问题，不直接重写 UI

早期述职材料显示，用户先借助 React DevTools 梳理消息列表组件树，输出渲染树和优化方案，并做团队宣讲。

### 2. 做逻辑和视图分离

用户补充的核心设计是：将逻辑和视图彻底分离，让滚动容器专注于虚拟列表容器角色并做针对性调优，同时配套做完整的消息列表数据和业务逻辑管理。

历史代码证据可支持这条设计线：

- `e88308ef9` `feat: refactor message store`：引入/重构 `MessageDataStore`，集中处理 feed、分页数据、加载页、LRU、初始化、滚动命令和视口索引。
- `df1505866` `feat: refactor message list container`：将 `MessageContentNext` 拆成 `MessageContentContainer`、`MessageListContext`、`MessageItem`、`useMessageActions`、`useMessageEvents`、`useReadMessages`、`useScrollRestoration` 等结构。
- `d25718cf2` 附近的 `VirtualListBetter`：存在 `DataStore` / `DataAccessor` / `RenderDispatcher`，将数据访问、视口同步、滚动事件和 UI 渲染拆开。

### 3. 面向 IM 场景做滚动调优

历史提交显示 2025-05-19 到 2025-05-21 有连续 scrollbar 和滚动相关迭代：

- `67cc8b013` `feat: scrollbar stash`
- `62b933619` `feat: scrollbar style`
- `407833ae9` `feat: scrollbar finish`
- `d13c7bf28` `feat: scrollbar optimize`

涉及文件包括 `VirtualListCore/CustomScrollbar.tsx`、`VirtualListCore/Virtualizer.tsx`、`VirtualListCore/core/scroller.ts`、`VirtualListBetter/index.tsx`、`useReadMessages.ts` 等。

### 4. 用 AI 加速，但保留工程控制权

用户描述：AI 帮助填充用户搭好骨架后的部分核心模块实现，并作为 reviewer 帮助提前发现和解决问题。

这条不能写成“AI 从零替我写完”，更适合表达为：

- 用户负责问题建模、架构骨架、迁移节奏和质量控制；
- AI 作为实现加速器和 reviewer，帮助补齐局部实现、发现边界问题。

## 结果

已可支撑：

- 该专项进入 2025-05-16 提前转正述职材料。
- PDF 中明确记录：消息列表优化方案、虚拟列表核心库和 React 滚动容器、新旧列表并存、checklist、基准测试和性能提升。
- git 历史能看到 2025-04-23 到 2025-05-21 围绕消息数据、列表容器、虚拟列表和滚动条的连续提交。

仍不能支撑：

- 具体性能提升百分比、FPS、渲染耗时、内存变化等量化数据。
- “公司内部第一次提前转正”仍需要 HR、主管评价或其它来源证据。

## 为什么重要

这是当前主简历最强代表项目之一，因为它同时证明：

- 复杂 IM 场景下的前端性能和滚动体验问题处理能力；
- 能从业务场景中抽象底层基础设施；
- 能做渐进式迁移，而不是冒险大重写；
- 能正确使用 AI：让 AI 加速实现和审查，但不替代架构判断。

## 证据

来源材料：

- [[inbox/converted/2026-06-24-lou-early-regularization]]
- [[inbox/2026-06-24-role-shift-ai-typex-pc-interview-1]]

本轮本地 git 证据：

- `/Users/lou/Work/typex-pc` 当前分支：`release/1.7.0`
- `e88308ef9` `2025-04-28 feat: refactor message store`
- `df1505866` `2025-05-13 feat: refactor message list container`
- `d25718cf2` `2025-05-14 fix: typo`，涉及 `VirtualListBetter/core/datastore`、`dispatcher`、`hooks`、`index.tsx`
- `67cc8b013` `2025-05-19 feat: scrollbar stash`
- `407833ae9` `2025-05-20 feat: scrollbar finish`

## 简历潜力

- 候选 claim：主导 TypeX PC IM 消息列表性能与架构重构，设计虚拟列表核心和滚动容器，拆分数据管理、滚动调度和视图渲染，以渐进式迁移支撑高风险核心模块优化。
- 演进材料：[[work/2026-06-24-typex-pc-virtual-list-evolution]]
- 风险：性能指标缺失；不能写具体倍数或百分比。早期 `DataStore / DataAccessor / RenderDispatcher / VirtualListBetter` 只是过渡方案，不应被表述为最终最佳实践。
- 需要补证据：benchmark 原始材料、截图、评审反馈、主管评价、早期分支/PR 链接。
