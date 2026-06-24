---
type: work_item
created_at: 2026-06-24
role: 虚拟列表与消息列表架构演进负责人
context: TypeX PC IM 场景中的虚拟列表从组件级过渡实现演进到应用级数据管理体系
action: 基于 git 历史重建 VirtualListBetter/Core 到 @virtualList 的多阶段演进路线
result: 形成可用于简历和面试攻防的演进叙事材料
ai_relevance: assisted
evidence_status: inferred-from-git
source_links:
  - "[[inbox/2026-06-24-role-shift-ai-typex-pc-interview-1]]"
  - "[[work/2026-06-24-typex-pc-message-list-virtual-list]]"
claim_links:
  - "[[resume/claims/2026-06-24-typex-message-list-virtual-list-claim]]"
drill_links:
  - "[[drill/2026-06-24-attack-ai-and-virtual-list-claims]]"
tags:
  - work-item
  - typex-pc
  - virtual-list
  - architecture-evolution
---

# TypeX PC 虚拟列表演进路线重建

## 背景

用户明确补充：早期 `DataStore / DataAccessor / RenderDispatcher / VirtualListBetter` 只是演化过程中的过渡状态，不代表最终形态或最佳实践。它们不应该成为重点复习内容，而应作为“当时如何拆分问题、解决了什么、又暴露了什么、后续如何优化”的演进材料。

本文件基于 `/Users/lou/Work/typex-pc` 当前分支 `release/1.7.0` 的 git 历史和当前代码重建，不假设用户仍记得每个历史类的写法。

## 演进路线

### 阶段 0：第三方虚拟列表的 IM 适配不足

用户口述的起点：

- 只是简单引入第三方虚拟列表库；
- 没有针对 IM 场景优化；
- 数据获取方式和滚动模型不匹配；
- 滚动卡顿、重渲染频繁；
- 不定高长消息和图片导致视图跳变；
- 逻辑耦合在少数文件里，只是“能用”。

这一阶段的问题不是“缺一个虚拟列表组件”，而是缺少一套面向 IM 的数据、滚动、事件、缓存和恢复策略。

### 阶段 1：VirtualListBetter/Core 过渡版

时间大致集中在 2025-04 下旬到 2025-05 下旬。

代表提交：

- `e88308ef9` `2025-04-28 feat: refactor message store`
- `df1505866` `2025-05-13 feat: refactor message list container`
- `d25718cf2` `2025-05-14 fix: typo`
- `67cc8b013` `2025-05-19 feat: scrollbar stash`
- `407833ae9` `2025-05-20 feat: scrollbar finish`

这个阶段的有效价值：

- 把原本耦合在消息列表组件里的数据管理、滚动事件、视口同步、消息渲染拆出来；
- 用 `MessageDataStore` 处理 feed、分页数据、加载页、LRU、初始化、滚动命令和视口索引；
- 用 `MessageContentContainer`、`MessageListContext`、`useMessageActions`、`useMessageEvents`、`useReadMessages`、`useScrollRestoration` 拆分消息列表容器职责；
- 用 `DataStore / DataAccessor / RenderDispatcher / VirtualListBetter` 这类结构试图建立数据访问、视口同步、滚动调度和 UI 渲染之间的边界；
- 针对 scrollbar、read message、indicator、scroll restoration 做连续修复。

这个阶段解决的问题：

- 从“组件里堆逻辑”推进到“数据管理和视图容器分离”；
- 支撑了早期消息列表专项和提前转正述职；
- 形成了后续继续抽象的经验基础。

这个阶段暴露的问题：

- 仍然偏组件级思维，数据生命周期和组件挂载/卸载关系较重；
- 类和概念较多，容易形成局部复杂度；
- 对多列表、多 feed、多场景的统一缓存、预取、事件合并、实例回收支持不足；
- 滚动、视口、数据事件之间仍需要大量补丁式迭代。

面试时不需要背这些类的实现，只需要能讲出：这是从耦合列表代码走向分层治理的第一轮过渡，不是最终答案。

### 阶段 2：应用级 ListManager / @virtualList 方向

2025-09 开始出现新的 `src/@virtualList` 路线。

代表提交：

- `f556c6346` `2025-09-04 feat: init`：初始化 `@virtualList`，出现 `DataManager`、`DataManagerRegistry`、事件总线、hook、manager。
- `fc30d4759` `2025-09-17 feat: 优化 push/pull/update 事件等处理流程，确保重渲染次数稳定`
- `82af37ec3` `2025-10-17 feat: registry lru manage`
- `c57565e60` `2025-11-07 feat: vl domain refractor`

`.doc/list-manager-architecture.md` 和 `.doc/listmanager-core-architecture.md` 体现了这次思想升级：

- 从组件级数据管理转向应用级数据中心；
- 数据生命周期不再完全跟随组件挂载/卸载；
- 以业务事件驱动数据操作，而不是组件自己拉数据；
- 把虚拟列表本质问题重新定义为：稀疏数据、视口驱动、数据一致性、性能边界；
- 引入 registry、focus/background、LRU、prefetch、invalidate、dirty range、viewport、scrolling 等更系统的概念。

当前 `@virtualList` 中可见的最终方向：

- `DefaultDataManager` 组合 `CacheStore / PolicyManager / LruReclaimer / ViewportTracker / PrefetchPlanner / PrefetchExecutor`；
- `DefaultRegistry` 按 `message:` / `feed:` 域管理实例，包含 message/feed 容量、focused pinned、background grace、LRU 回收；
- `eventCoordinator` 将 push/pull/update/delete/scroll/forceRefresh 事件按 key 合并，滚动中只处理视口相关事件，非视口事件进入 pending；
- debug API / metrics / memory snapshot 提供可观测性。

### 阶段 3：移除过渡实现，收敛到新体系

代表提交：

- `7189eb017` `2025-11-17 feat: remove virtualListBetter`

该提交删除了 `VirtualListBetter` 大量代码，包括 datastore、dispatcher、hooks、types、index 等，说明早期过渡层最终被替换，不应被包装成“最终架构”。

这反而是一个很强的架构信号：用户不是只做了一个一次性组件，而是在真实业务反馈中持续迭代，把早期方案中不合适的部分淘汰掉。

### 阶段 4：稳定性、调度和可观测性增强

代表提交和材料：

- `0739486a8` `2025-12-05 feat(vl): AIMD smart control`：引入自适应并发/调度阈值、pending store 等。
- `.doc/virtualList-pending冲刷兜底修复.md`：处理滚动中 pending 事件未被 forceRefresh 冲刷导致数据旧值、missing 或内存累积的问题。
- `.doc/virtualList-订阅器泄漏修复.md`：处理订阅者未清理、已卸载回调残留、通知异常和性能下降。
- `0a60fa888` `2026-04-21 fix(virtual-list): harden message manager remount recovery`：强化消息 manager 重挂恢复。
- `7686865f4` `2026-05-01 feat: reconcile message list v2 updates`：新增 message list runtime / adapter 相关测试和桥接。

这一阶段体现的是工程成熟度：不仅解决“能滚动”，还处理后台/前台、快速切换、pending 冲刷、订阅泄漏、内存估算、debug 面板、事件风暴、重挂恢复等真实应用里的长期稳定性问题。

## 面试叙事口径

可以这样讲：

> 早期我做的不是一次到位的完美虚拟列表，而是一个逐步演进的 IM 列表基础设施。第一阶段先把耦合在消息列表里的数据、滚动、渲染逻辑拆开，解决卡顿、跳变和重渲染失控问题，支撑了当时的交付和提前转正。后续随着场景复杂度上升，我逐步意识到组件级 store 不够，于是演进到应用级 `@virtualList`：用 DataManager/Registry/EventCoordinator 管理稀疏数据、视口驱动、事件合并、LRU、预取、focus/background 和 debug metrics。最终还移除了早期 `VirtualListBetter` 过渡层，说明这套东西不是停留在 demo，而是在真实业务反馈里持续收敛。

## 攻防重点

不要把旧类名当成最佳实践背诵。真正要防守的是：

- 为什么第三方虚拟列表不够；
- 为什么早期先做逻辑/视图分离；
- 为什么后续要从组件级转向应用级；
- 如何处理 IM 场景里的动态高度、滚动恢复、数据一致性、事件合并、缓存回收；
- 为什么删除 `VirtualListBetter` 是架构成熟，而不是失败；
- AI 在其中是加速实现和 review，不替代问题建模和演进判断。

## 证据

已查证据：

- `/Users/lou/Work/typex-pc` 当前分支：`release/1.7.0`
- 当前代码：`packages/typex-pc-render/src/@virtualList/**`
- 当前文档：`.doc/list-manager-architecture.md`、`.doc/listmanager-core-architecture.md`、`.doc/virtualList-pending冲刷兜底修复.md`、`.doc/virtualList-订阅器泄漏修复.md`
- 历史提交：`e88308ef9`、`df1505866`、`d25718cf2`、`f556c6346`、`fc30d4759`、`82af37ec3`、`c57565e60`、`7189eb017`、`0739486a8`、`0a60fa888`、`7686865f4`

## 待补

- 哪些阶段真正上线，分别覆盖哪些用户或版本；
- 是否有 PR/评审链接；
- 是否有故障复盘、用户反馈或团队评价；
- 当前 `@virtualList` 与后续 `@messageList` / v2 runtime 的边界，需要下一轮单独梳理。
