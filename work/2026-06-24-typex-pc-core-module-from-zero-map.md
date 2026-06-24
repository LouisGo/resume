---
type: work_item
created_at: 2026-06-24
role: 前端架构师 / 核心模块负责人 / AI 应用架构开发者
context: TypeX PC @message / @actions / @contextMenu / @ai 等核心模块从零到一与体系化演进
action: 基于用户口述、当前源码文档和 git 历史建立模块级事实地图
result: 形成消息渲染、全局指令、上下文菜单和 AI runtime 的简历材料池
ai_relevance: high
evidence_status: mixed
source_links:
  - "[[inbox/2026-06-24-typex-pc-core-modules-message-actions-ai]]"
claim_links:
  - "[[resume/claims/2026-06-24-ai-agent-platform-claim]]"
drill_links:
  - "[[drill/2026-06-24-attack-ai-and-virtual-list-claims]]"
tags:
  - work-item
  - typex-pc
  - message-rendering
  - actions
  - context-menu
  - ai-agent
---

# TypeX PC 重点模块从零到一地图

## 背景

用户补充：过去一年多在 `~/Work/typex-pc` 中还完成了 `@message`、`@actions`、`@contextMenu`、`@ai` 等重点模块的从零到一开发。

本文件只做材料收集和证据整理，不直接压缩成最终简历表达。

## 我的角色

候选定位：

- 核心业务前端架构负责人：负责把复杂 IM 消息渲染、动作系统、右键菜单、AI Chat 等从散乱实现演进为稳定模块。
- 团队工程范式建设者：通过 descriptor、registry、manifest、selector、静态配置、测试规范等方式降低后续维护成本。
- AI 应用架构开发者：推动 AI Chat、runtime、ToolUse、provider、多模型、流式输出和写操作确认等能力在桌面端产品中落地。

## 模块事实地图

### 1. `@message`：配置化消息渲染引擎

当前文档把 `src/@message/` 定义为一条完整消息渲染链路，而不是零散消息组件。

主链路：

```text
SDK Raw Message
  -> message type resolution / transformer registry
  -> createMessageTransformer(...)
  -> MessageModel
  -> manifest + static capabilities
  -> facts / policy / layout
  -> MessageRenderProvider
  -> renderer / layout / actions
```

关键设计：

- `MessageModel` 是中心，渲染层消费规范化领域对象，而不是边渲染边猜 SDK 字段。
- `messageTypeManifest` 收敛 renderer、base variant、menu keys、static traits，避免类型知识散落在多张 map 中。
- Transform 和 Render 分层：transform 清洗 SDK 数据，render 只消费领域模型。
- Facts / Policy / Layout 拆开：输入收集、运行时决策和布局结果各自独立。
- 行级渲染使用 `MessageRenderProvider` 与 selector hooks，形成性能边界。
- 列表级 `ambient` 与行级消息数据分层，避免每行重复订阅 feed context、preferences、scene config。

可支撑的简历材料：

- 不是“写了一堆消息组件”，而是把消息渲染升级为领域模型驱动、配置化、可测试、可演进的渲染引擎。
- 能支撑多消息类型、多场景布局、右键菜单、action scope、optimistic、translation/source fallback 等复杂能力。

风险：

- “降维打击”是用户感受，不能直接写进简历；需要改成可解释的架构差异。
- 需要补旧链路问题：旧内容具体差在哪里、维护成本/bug/新增类型成本有什么证据。

### 2. `@actions`：全局指令系统

用户说明：`@actions` 模仿 VS Code 指令系统，为后续 `typex-cli` 做 AI ToolUse 打基础，是通用逻辑复用范式。

当前 `@actions` 文档支持以下事实：

- `@actions` 是渲染端统一全局指令系统。
- `descriptors.ts` 是唯一 source of truth，同时负责：
  - 业务调用面：`actions.message.copy(payload)`
  - runtime 注册源：`id / label / handler`
  - 类型推导源：`payload / result / action id`
- 核心价值是统一 API、统一注册、统一类型入口、统一 discoverability，而不是承载重业务实现。
- action 本身应保持薄入口，业务逻辑回到 `@ai`、`@message`、services、领域 util 或 store。
- authoring 体验强调静态、直接、可跳转，避免字符串 action id 在业务代码中扩散。

当前 descriptor 已覆盖 AI 域和大量 message 域动作，例如：

- AI：`ai.askMessageFromMessage`、`ai.prepareChatAttachments`
- 消息：回复、转发、编辑、删除、撤回、重发、收藏、pin、提醒、加急、话题、reaction、已读详情、媒体预览、多选、翻译、发送到 AI 等

可支撑的简历材料：

- 设计并落地类似 VS Code command pattern 的前端全局动作系统，把菜单、快捷入口、跨组件命令和 AI ToolUse 潜在调用面统一为 typed command API。
- 这能体现“从组件交互抽象到命令系统”的架构能力。

风险：

- “为 typex-cli 打基础”目前主要来自用户口述，需要找设计文档、cli 接入计划或后续调用证据。
- 需要明确 `@actions` 和 AI runtime tools 的边界：一个是渲染端 typed command API，一个是 main runtime 暴露给 LLM 的 tool set，不能混写。

### 3. `@contextMenu`：分布式定义，统一消费

当前 `@contextMenu` 文档支撑的设计：

- 架构原则是“分布式定义，统一消费”。
- 使用“OS vs App”的能力注入模式：
  - Bubble 层作为 OS，统一挂载右键事件、提供运行时环境和标准能力接口。
  - Message Type 作为 App，定义菜单结构、组合 Action、调用标准能力。
- `onContextMenu` 挂在 Bubble 层，避免具体 renderer 各自重复处理事件、坐标、震动反馈和 padding 点击盲区。
- 每个消息类型可以在自己的 `contextMenu.ts` 中静态定义菜单结构，避免静态定义文件混入 hooks、store 或具体 API 副作用。

可支撑的简历材料：

- 把右键菜单从“每种消息类型各写各的交互逻辑”升级为可配置、可注入、可复用的交互系统。
- 这和 `@message`、`@actions` 合在一起，构成消息渲染引擎的交互层能力。

风险：

- 需要确认从零到一的时间线和贡献边界。
- 可单独作为简历 bullet 的价值可能不如 `@message + @actions` 高，更适合作为消息渲染引擎的支撑细节。

### 4. `@ai`：AI Chat 与 runtime / ToolUse 底座

当前 renderer `@ai` README 显示：该模块用于 AI Chat 侧边栏前端实现，采用增量开发方式，不破坏现有 `@message` 渲染体系。

当前 AI Function Call 文档显示完整链路：

```text
用户自然语言
  -> Renderer startChat
  -> Main 构建 prompt 和 tools
  -> LLM 判断是否 tool call
  -> AI SDK 执行 tool
  -> tool result 回到 LLM
  -> LLM 生成最终回答
  -> Renderer 展示流式结果
```

模块分工：

- Renderer：发起请求、维护本地 streaming 状态、消费 stream event。
- Main Runtime Service：接收请求、组织执行、转发 stream event。
- acceptedExecution：构建 prompt、上下文、provider model、tools。
- requestRunner：调用 AI SDK `streamText`，处理 stream、reasoning、tool loop、token usage、错误。
- tools/registry：根据 execution context 创建并过滤 tools。
- tool builtins：定义 schema、execute、toModelOutput。
- SDK / source：提供内部数据和动作能力。

当前 main `@ai/tools` 里已有 builtin：

- `grep_search`
- `find_chat_entity`
- `chatting_records_retrieval`
- `get_me`
- `get_contact_requests`
- `approve_contact_requests`
- `create_reminder`
- `send_text_message`
- `add_group_members`

关键安全/稳定设计：

- `allowedTasks` 按 task 过滤工具；
- `safeToAutoRun=false` 的写工具进入确认闸门；
- 写操作草稿按 `sessionId + feedId` 分桶并设置 TTL；
- 同一 requestId 不能 draft + confirm 自我放行；
- tool loop 有 step/call 上限，避免模型无限工具调用；
- provider stream 有 idle timeout 和错误归一化。

可支撑的简历材料：

- 能讲清 AI 产品不是简单套 Chat UI，而是 renderer/main/runtime/tool/source 的分层架构。
- 能讲清 ToolUse 的 guardrail：工具过滤、写操作确认、跨回合确认、防同轮自放行、流式事件和错误处理。

风险：

- openclaw、MCP 相关证据本轮尚未核验，暂不写成已完成事实。
- 需要补充 AI Chat / ToolUse 的实际上线范围、使用反馈、与 typex-cli 的关系。

## 时间线证据

本轮只读查看 `/Users/lou/Work/typex-pc` 当前分支 `release/1.7.0`。

代表性 git 证据：

- 2026-01-27 到 2026-01-31：右键菜单、action、global actions、contextMenu refactor、actions settled 等提交，说明 `@actions/@contextMenu` 在这一阶段成形。
- 2026-03-11：`Message Rendering V2` 文档、本地化、测试 harness、message type/layout 语义测试、message type metadata、transformer contract、ambient list context、selector-based feed/preference scope、action scope 等连续提交，说明 `@message` V2 进入体系化阶段。
- 2026-04-19：`remove v1 message flow and rename v2 model`、`unify actions descriptors`，说明旧消息流被移除，actions descriptor 收敛。
- 2026-03-24 到 2026-03-31：AI side container、DeepSeek tuning、AI chat list、request move to main 等 AI Chat 初期开发。
- 2026-04-14 到 2026-04-17：AI Agent docs、runtime phase、repository adapters、provider registry、request runner、runtime diagnostics、renderer AI 边界拆分、image input、多模态和 prompt 调整。
- 2026-04-22 到 2026-04-25：AI request proxy、AI functions、tool/proxy 相关问题、AI chat functions。
- 2026-05-11 到 2026-05-13：ask AI by message、media message、selection、AI i18n。
- 2026-05-29 到 2026-06-03：AI custom provider/custom mode、AI selector、timeout、i18n 和 bugfix。

## 为什么重要

这些模块合在一起，能支撑用户“前端架构师 + 团队效率体系构建者 + AI 落地开发者”的整体定位：

- `@message`：复杂业务渲染引擎和领域建模能力。
- `@actions`：跨组件、跨菜单、跨业务入口的命令系统抽象能力。
- `@contextMenu`：交互系统配置化和能力注入能力。
- `@ai`：AI 应用 runtime、ToolUse、streaming、guardrail、provider/model 管理能力。

## 证据

本轮已读：

- `/Users/lou/Work/typex-pc/packages/typex-pc-render/src/@message/README.md`
- `/Users/lou/Work/typex-pc/packages/typex-pc-render/src/@message/ARCHITECTURE.md`
- `/Users/lou/Work/typex-pc/packages/typex-pc-render/src/@actions/README.md`
- `/Users/lou/Work/typex-pc/packages/typex-pc-render/src/@actions/AUTHORING.md`
- `/Users/lou/Work/typex-pc/packages/typex-pc-render/src/@actions/descriptors.ts`
- `/Users/lou/Work/typex-pc/packages/typex-pc-render/src/@contextMenu/README.md`
- `/Users/lou/Work/typex-pc/packages/typex-pc-render/src/@ai/README.md`
- `/Users/lou/Work/typex-pc/packages/typex-pc-render/src/@ai/docs/ai-function-call-runtime-flow.md`
- `/Users/lou/Work/typex-pc/packages/typex-pc-main/src/@ai/tools/registry.ts`
- `/Users/lou/Work/typex-pc/packages/typex-pc-main/src/@ai/tools/executor.ts`
- `/Users/lou/Work/typex-pc/packages/typex-pc-main/src/@ai/tools/types.ts`
- `/Users/lou/Work/typex-pc/packages/typex-pc-main/src/@ai/tools/writeConfirmation.ts`
- `/Users/lou/Work/typex-pc/packages/typex-pc-main/src/@ai/requestRunner.ts`
- `git log --author='Lou'` for `@message`、`@actions`、`@contextMenu`、`@ai`

## 简历潜力

- 候选 claim：主导 TypeX PC 消息渲染体系升级，将 SDK 原始消息处理为 `MessageModel` 驱动的配置化渲染引擎，并配套统一 actions/context menu 交互体系，支撑多消息类型、多场景、多交互能力的稳定演进。
- 候选 claim：设计并落地渲染端全局指令系统 `@actions`，以 descriptor 作为 API、runtime registry 和类型推导的单一来源，沉淀跨组件/跨菜单/跨 AI 场景的 typed command 复用范式。
- 候选 claim：参与/主导 TypeX PC AI Chat 与 ToolUse runtime 建设，完成 renderer/main 分层、provider registry、request runner、tool registry、stream event 和写操作确认等关键能力。
- 风险：模块很多，不能在简历里全部展开；后续需要选 1-2 条最能体现架构高度和 AI 能力的主线。
- 需要补证据：上线版本、PR/评审、旧链路对比、协作边界、typex-cli 关联证据、openclaw/MCP 代码路径。
