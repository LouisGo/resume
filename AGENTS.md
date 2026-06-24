# Agent Rules

这个 vault 是简历 AI 工作台。所有 agent 行为都要服务低摩擦输入、强提炼、强表达和面试准备。

## Prime Directive

不要把 vault 做成大型分类学系统。每个新增文件、字段、视图都必须服务以下目标之一：

- 降低用户输入成本
- 从碎片化输入中提炼有价值的简历材料
- 提升简历 claim 的质量和精确度
- 在面试前暴露 claim 风险
- 生成或调度有效的复习和攻防练习

## Allowed AI Behavior

- 可以把用户原始输入或对谈摘要转成结构化笔记。
- 只有在事实有链接支撑或明确标记为待验证时，才可以提出简历表达。
- 每条强 claim 必须标记为 `ready`、`needs-proof`、`needs-rewrite`、`drill-only` 或 `rejected`。
- 要主动模拟面试官，从真实性、深度、业务价值、技术清晰度发起攻击。
- 要创建带 `next_review` 和 `memory_status` 的复习项。

## Forbidden AI Behavior

- 不要编造工作事实、指标、奖项、技术栈或业务影响。
- 不要把历史简历文案默默升级成当前事实。
- 不要把未处理材料埋到 `inbox/` 之外。
- 不要在工作流真正需要前创建大量目录或 schema。
- 不要覆盖归档源材料。

## Processing Standard

对于任何重要输入：

1. 先保存或总结为 `inbox/` 中的 `input_note`。
2. 把有用事实提炼到 `work_item` 或 `ai_practice`。
3. 只有可能进入简历的内容才创建 `resume_claim`。
4. 对高价值、高风险、记忆薄弱的主题创建 `drill_item`。
5. 保持 input、work、claim、drill 之间的链接。

## Obsidian Usage

- 事实内容使用 Markdown。
- `.base` 只作为管理视图。
- `.canvas` 只作为主流程图。
- 优先使用 wikilink，不要重复复制摘要。
- 可以使用 Obsidian CLI，但 vault 不能依赖 CLI 才可用。
