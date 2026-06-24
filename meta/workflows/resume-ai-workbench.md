---
type: workflow
status: active
created_at: 2026-06-24
---

# Resume AI Workbench Workflow

## 1. Capture

所有内容先进入 `inbox/`。好的 inbox 笔记可以很粗糙，只要保留足够上下文，方便后续提炼。

适合进入 inbox 的来源：

- 与 AI 的对谈摘要
- 项目记忆
- 复制来的工作总结
- 面试反馈
- 其他 AI 输出

## 2. Extract

把有价值的原始输入转成：

- `work_item`：普通项目、职责、成果或关键故事
- `ai_practice`：AI 应用架构、AI 前端提效、个人 AI 产品经验

尽早标记不确定性。事实不稳时使用 `evidence_status: needs-proof` 或 `unverified`，不要把弱事实先包装成强表达。

## 3. Select

只有具备简历价值的材料才创建 `resume_claim`。

每条 claim 都要回答：

- 它证明了候选人的什么能力？
- 它为什么对高级前端/架构岗位重要？
- 有哪些证据支撑？
- 面试官会攻击哪里？

## 4. Write

只把通过筛选的 claim 放进 `resume/中文主简历.md`。

高质量简历表达应该：

- 具体
- 紧凑
- 结果导向
- 技术上可防守
- 能在压力面试中解释清楚

## 5. Drill

对重要 claim 创建 `drill_item`。

重点演练：

- 真实性
- 技术深度
- 架构取舍
- 业务价值
- 个人贡献边界
- 可量化结果

## 6. Review

给 drill item 设置 `next_review`，练习后更新 `memory_status`。

目的不是背诵文本，而是让答案在真实面试压力下保持稳定。
