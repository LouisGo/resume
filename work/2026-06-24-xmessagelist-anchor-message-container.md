---
type: work_item
created_at: 2026-06-24
role: IM 消息列表基础设施设计与实现负责人
context: TypeX PC 消息列表从通用虚拟列表演进到专用锚点模型滚动容器
action: 实现独立 x-message-list，采用消息锚点模型、loaded segment、native scroll 与 session/runtime 分层
result: 形成接近最终形态的消息滚动容器，后续可通过 SDK adapter 接入 typex-pc
ai_relevance: assisted
evidence_status: code-and-docs
source_links:
  - "[[inbox/2026-06-24-xmessagelist-anchor-model-typex-integration]]"
  - "[[inbox/converted/2026-06-24-im-message-anchor-research]]"
  - "[[work/2026-06-24-typex-pc-virtual-list-evolution]]"
claim_links:
  - "[[resume/claims/2026-06-24-xmessagelist-anchor-runtime-claim]]"
drill_links:
  - "[[drill/2026-06-24-attack-ai-and-virtual-list-claims]]"
tags:
  - work-item
  - x-message-list
  - message-list
  - architecture
---

# XMessageList 锚点模型消息滚动容器

## 背景

TypeX PC 早期消息列表经历过第三方虚拟列表、`VirtualListBetter/Core` 过渡层、`@virtualList` 应用级数据管理等阶段。用户现在补充：当前消息列表已经按“IM 消息锚点方案”推进落地，`x-message-list` 可以认为接近最终形态。

这一次仍然是“逻辑和视图分离”，但分离粒度从组件内部职责拆分，升级为完整的跨层所有权划分。

## 我的角色

用户已经完整实现了独立的 `x-message-list`，项目位于 `/Users/lou/Work/XMessageList`。该项目面向 TypeX 风格聊天界面，不是通用虚拟列表。

## 核心模型

PDF 预研报告中的核心判断：

- `index` 是可变视图坐标，`anchor` 是稳定消息身份。
- `messageId / position` 应作为消息身份锚点。
- `before / after / around` 应围绕锚点取上下文。
- `count / index` 可以保留为统计、水位、兼容和诊断信息，但不能承担消息身份语义。
- SDK 负责消息身份、顺序和上下文读取；前端负责窗口渲染、滚动补偿和交互体验。

XMessageList 当前实现对应的工程模型：

- 正常文档流渲染当前 loaded segment，不用 top/bottom spacer 伪造未加载历史高度。
- 原生滚动条表达真实已挂载 DOM 高度，自定义 overlay 只能镜像 native metrics。
- 上下分页由文档流里的 before / after trigger 触发。
- 视觉稳定依赖 message anchor 的 DOM rect 差值补偿。
- Projection transaction 流程为：capture visual anchor -> publish snapshot -> wait React commit ack -> measure committed DOM -> correct scrollTop -> settle state。

## 架构分层

当前 XMessageList 文档和源码显示分层为：

- Host Message Event Store：宿主拥有 canonical message cache、dirty state、未读和跨列表 fanout。
- MessageList Session Registry：按 `sessionId` 创建和保留 `MessageListSession`，掌管 adapter routing、request bridge、`anchorMemory`、`readReceipts`、keepAlive。
- Loaded Segment Store：维护当前 session 的 loaded segment，负责 merge、dedupe、identity remap、trim、request token。
- Viewport Runtime：掌管 scroll container、DOM refs、measurement、visual anchor、transaction、scrollTop correction、edge need 和 diagnostics。
- React Adapter：负责渲染 DOM skeleton、row wrapper、slot 和 commit ack，不处理数据合并和滚动修正。

这比早期 `VirtualListBetter` 的“组件内数据/滚动/渲染拆分”更高一层：它把业务缓存、加载窗口、视口 runtime、React 投射和 SDK/host 边界都拆开了。

## 公开接入形态

当前包根出口和 README 支撑以下接入模型：

- `createMessageListSessionRegistry`
- `MessageListSessionRegistryProvider`
- `useMessageListSession`
- `useMessageListState`
- `MessageList`
- `MessageListAdapter`

`MessageListAdapter` 暴露：

- `row.getKey`
- `row.getAnchor`
- `row.getVersion`
- `row.getKind`
- `request.loadLatest`
- `request.loadBefore`
- `request.loadAfter`
- `request.loadAround`
- `anchorMemory.load/save`
- `readReceipts`

`MessageListSession` 暴露：

- `commands.scrollToLatest`
- `commands.scrollToMessage`
- `commands.loadBefore`
- `commands.loadAfter`
- `commands.reloadLatest`
- `rows.patch/mutate/replace/resetLatest/resetAround/applyIdentityRemap/clear`
- `tail.local.stage/patch/applyIdentityRemap`
- `tail.remote.append`

## 与 typex-pc 的关系

用户当前判断：

- XMessageList 的消息滚动容器已经完整实现，接近最终形态。
- typex-pc 后续工作主要是对接 SDK 接口，重写数据管理和基于锚点心智模型的配套逻辑。

本轮保守表述：

- 已有独立 XMessageList 容器和当前 docs/source 证据。
- typex-pc 集成、SDK anchor/context API、Host Message Event Store 改造仍是待落地/待验证范围。

## 为什么重要

这条材料比“写了虚拟列表”更强，因为它证明的是：

- 能从 IM 消息身份、滚动体验、SDK 边界和前端 runtime 四个层面重构问题。
- 能识别 `index/count` 模型的抽象泄漏，并提出 anchor/context API 的长期方向。
- 能把复杂交互问题落成独立基础设施，而不是把补丁堆在业务组件里。
- 能把 React 渲染从数据加载、滚动修正和 session 生命周期中解耦。

## 证据

当前证据：

- PDF：[[inbox/converted/2026-06-24-im-message-anchor-research]]
- 仓库：`/Users/lou/Work/XMessageList`
- 当前分支：`next`
- 当前文档：`README.md`、`docs/README.md`、`docs/architecture/module-map.md`、`docs/architecture/layering-and-ownership.md`、`docs/implementation/dom-layout.md`、`docs/implementation/transactions-and-measurement.md`
- 当前源码：`src/x-message-list/core/session-registry/**`、`src/x-message-list/core/runtime/**`、`src/x-message-list/react/**`
- 近期提交：2026-05-13 到 2026-06-08 之间 Lou 有连续提交，覆盖 runtime、React adapter、session registry、e2e、public boundary、performance、docs 和 loaded context contract。

## 简历潜力

- 候选 claim：独立设计并实现面向 IM 的 `x-message-list` 消息滚动容器，以消息锚点为核心抽象，用 loaded segment native scroll 和 transaction measurement 保证动态高度、分页、跳转、恢复、乐观重映射和尾部跟随场景下的视觉稳定。
- 风险：不能写成“已在 TypeX PC 完成线上替换”，除非后续补集成证据。
- 需要补证据：typex-pc 接入分支/PR、SDK anchor API 对接状态、验证结果、发布范围。
