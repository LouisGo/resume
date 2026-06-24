---
type: drill_item
created_at: 2026-06-24
claim_link:
  - "[[resume/claims/2026-06-24-typex-message-list-virtual-list-claim]]"
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

# 攻击：AI 使用、虚拟列表和 AI Agent 是否真的可防守

## 面试官问题

你说自己用 AI 完成了虚拟列表库、推动消息列表重构，又做了 AI Agent 底座。这里面哪些是你自己的架构判断，哪些只是 AI 生成代码？如果我让你现在不用 AI 讲清楚核心设计，你能讲到什么程度？

## 为什么危险

- “AI 帮助从零编写”容易被攻击为本人能力不足。
- “性能显著提升”如果没有指标，会被攻击为主观感受。
- “AI Agent 底座”如果边界不清，会被攻击为概念堆砌。
- “团队最早推广 AI”如果没有分享材料和实际影响，会被攻击为不可验证。

## 强回答结构

- Situation：TypeX PC 的 IM 消息列表存在卡顿和可维护性问题，团队也处在 AI 工具快速发展的阶段。
- Task：我需要在不打断业务迭代的情况下，设计可渐进迁移的消息列表方案，并探索 AI 在复杂前端工程中的正确使用方式。
- Action：先用 React DevTools 梳理组件树和渲染链路，输出优化方案并团队宣讲；再抽象 `VirtualListCore` 和 `VirtualListBetter`，覆盖动态高度、消息加载、新旧列表并存、checklist 和基准测试；AI 主要用于加速实现、辅助拆解和验证，但设计取舍、迁移策略、质量控制由我负责。
- Result：早期材料证明该专项进入提前转正述职，并支撑消息列表重构；后续仍需补充量化性能、代码路径和上线证据。
- Tradeoff：不把 AI 当成替代判断的工具，而是把它放进可验证的工程闭环里：方案、代码、测试、review、文档、复盘。

## 连环追问

- 你的虚拟列表核心数据结构是什么？如何处理动态高度和滚动锚点？
- 为什么不直接用成熟第三方库？你参考过哪些库，它们哪里不适合 IM 场景？
- 新旧消息列表并存怎么控制风险？路由拦截方案如何回滚？
- 基准测试怎么设计？测什么指标？数据在哪里？
- AI 在这个项目中具体帮了哪些步骤？哪些代码或方案是你重写或否决的？
- AI Agent 底座里 openclaw、MCP、ToolUse、AI Chat 的架构边界是什么？
- 如果团队成员质疑 AI 生成代码质量，你如何建立质量控制流程？

## 复习记录

- 2026-06-24：根据第一轮访谈创建。当前记忆状态为 `new`，需要下一轮补时间线、证据和代码路径。
