---
type: input_note
source_type: ai_conversation
created_at: 2026-06-24
processed_status: done
topic_hint: resume-ai-workbench-v1
tags:
  - resume-workbench
  - ai-workflow
---

# 2026-06-24 Resume AI Workbench Session

## 原始摘要

用户希望这个仓库成为通过 Obsidian 和 Git 维护的个人简历工作台。

系统不应该从大型分类开始，而要先做减法，聚焦核心流水线：

```text
Capture -> Extract -> Select -> Write -> Drill -> Review
```

## 输入

- 用户整理的资料
- 与 AI 的对谈记录
- 用户手动输入
- 其他 AI 生成的补充内容

## 输出

- 完整可用的中文主简历
- 结构化复习材料
- 面试攻防演练
- 面试实战总结

## 关键决策

- 第一版只服务一份中文主简历。
- 后续大部分输入可能来自与 AI 的对谈，而不是预先整理好的文件。
- 2025-04 旧简历只作为历史基线。
- AI 必须成为提炼、claim 质量、风险发现和复习调度的核心能力。

## 处理结果

- 已提炼为工作台操作模型。
- 已创建轻量模板、Bases、Canvas 和 agent 规则。
- 后续真实工作内容仍应从新的 inbox 输入开始。
