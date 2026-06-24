---
type: drill_item
created_at: 2026-06-24
claim_link:
  - "[[resume/claims/2026-06-24-typex-message-list-virtual-list-claim]]"
  - "[[resume/claims/2026-06-24-xmessagelist-anchor-runtime-claim]]"
  - "[[resume/claims/2026-06-24-ai-agent-platform-claim]]"
question_type: attack
difficulty: hard
next_review: 2026-06-27
memory_status: new
tags:
  - drill
  - typex-pc
  - ai-practice
---

# 攻击：AI 使用、虚拟列表演进和 AI Agent 是否真的可防守

## 面试官问题

你说自己用 AI 完成了虚拟列表库、推动消息列表重构，又做了 AI Agent 底座。这里面哪些是你自己的架构判断，哪些只是 AI 生成代码？如果我让你现在不用 AI 讲清楚核心设计，你能讲到什么程度？

## 为什么危险

- “AI 帮助从零编写”容易被攻击为本人能力不足。
- “性能显著提升”如果没有指标，会被攻击为主观感受。
- “AI Agent 底座”如果边界不清，会被攻击为概念堆砌。
- “团队最早推广 AI”如果没有分享材料和实际影响，会被攻击为不可验证。
- 用户明确说性能对比材料缺失，因此不能用虚构百分比应对追问。
- 早期 `DataStore / DataAccessor / RenderDispatcher / VirtualListBetter` 是过渡阶段，不应被背成最终最佳实践。
- XMessageList 已实现和 typex-pc 已完成接入是两件事，面试时必须区分。

## 强回答结构

- Situation：TypeX PC 的 IM 消息列表存在卡顿和可维护性问题，团队也处在 AI 工具快速发展的阶段。
- Task：我需要在不打断业务迭代的情况下，设计可渐进迁移的消息列表方案，并探索 AI 在复杂前端工程中的正确使用方式。
- Action：先用 React DevTools 梳理组件树和渲染链路，输出优化方案并团队宣讲；第一阶段把消息数据管理、滚动调度和视图渲染拆开，形成 `VirtualListBetter/Core` 过渡层，解决卡顿、跳变和耦合问题；后续随着多列表、多 feed、后台/前台、事件一致性和缓存回收问题增加，演进到应用级 `@virtualList`；再进一步把问题升维成 IM 消息锚点模型，独立实现 `x-message-list`：用 `messageId / position` 表达身份，围绕 before/after/around 加载上下文，用 loaded segment + native scroll + 正常文档流渲染，并通过 transaction measurement 做视觉锚点补偿。AI 主要用于加速实现、辅助拆解和 reviewer，但设计取舍、迁移策略、质量控制由我负责。
- Result：早期材料证明该专项进入 2025-05-16 提前转正述职；git 历史能看到 2025-04-23 到 2025-05-21 的连续提交；后续仍需补充量化性能和上线/评审证据。
- Tradeoff：不把 AI 当成替代判断的工具，而是把它放进可验证的工程闭环里：方案、代码、测试、review、文档、复盘。

## 连环追问

- 你的虚拟列表核心数据结构是什么？如何处理动态高度和滚动锚点？
- 为什么不直接用成熟第三方库？你参考过哪些库，它们哪里不适合 IM 场景？
- 你说“逻辑和视图分离”，这在第一阶段解决了什么问题？后来为什么还要演进到应用级 `@virtualList`？
- `VirtualListBetter` 最后被删除了，这说明当时方案失败了吗？你怎么解释这件事？
- 应用级 `@virtualList` 里的 DataManager、Registry、EventCoordinator 分别解决什么问题？
- 为什么 XMessageList 不继续做通用虚拟列表，而要改成专用 IM 消息列表？
- `messageId / position` 作为 anchor，比 `from/end + count` 强在哪里？
- loaded segment + native scroll 和传统虚拟列表 spacer 模型的根本区别是什么？
- Host Message Event Store、Session Registry、Loaded Segment Store、Viewport Runtime、React Adapter 的边界分别是什么？
- typex-pc 还没完成 SDK 接入时，这条 claim 怎样写才不越界？
- 新旧消息列表并存怎么控制风险？路由拦截方案如何回滚？
- 基准测试怎么设计？测什么指标？如果没有数据，为什么还能说性能提升？
- AI 在这个项目中具体帮了哪些步骤？哪些代码或方案是你重写或否决的？
- AI Agent 底座里 openclaw、MCP、ToolUse、AI Chat 的架构边界是什么？
- `@message` 为什么能叫消息渲染引擎，而不是一组消息组件？
- `@actions` 和普通 util/helper 有什么本质区别？为什么要模仿 VS Code command system？
- `@actions`、`@contextMenu`、`@message` 三者的边界是什么？右键菜单点击一条消息时，数据和命令链路怎么走？
- `@actions` 和 AI runtime tools 是不是一回事？如果不是，为什么它能为后续 AI ToolUse / typex-cli 打基础？
- 写操作 tool 为什么需要跨回合确认？你如何防止模型在同一轮里自己生成 draft 又自己 confirm？
- 如果团队成员质疑 AI 生成代码质量，你如何建立质量控制流程？

## 复习记录

- 2026-06-24：根据第一轮访谈创建。当前记忆状态为 `new`，需要下一轮补时间线、证据和代码路径。
- 2026-06-24：补充入职/述职/转正时间，查到早期 git 提交证据。性能指标仍缺失，回答时必须主动承认没有原始数据，改用“问题消除 + 体验改善 + 基准测试曾建立但数据待找回”的防守方式。
- 2026-06-24：根据用户反馈调整复习重点。早期类名只作为演进证据，不再作为重点背诵内容；新增 [[work/2026-06-24-typex-pc-virtual-list-evolution]]。
- 2026-06-24：补充 XMessageList 阶段。重点改为防守“为什么从通用虚拟列表演进到基于消息锚点的专用滚动容器”，并区分 XMessageList 已实现与 typex-pc 待接入。
- 2026-06-24：补充 `@message / @actions / @contextMenu / @ai` 模块地图。重点防守模块边界、command system 与 ToolUse 的关系，以及 AI 写操作确认机制。
