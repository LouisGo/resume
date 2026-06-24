# Resume AI Workbench

这个仓库是一个基于 Obsidian 的 AI 简历工作台，用来持续生成高质量中文主简历，并沉淀配套复习、攻防、实战材料。

目标不是收集更多笔记，而是降低输入摩擦，从碎片化工作经历里提炼真正有价值的内容，写出更强的简历表达，并用结构化复习循环完成面试攻防演练。

## 核心流程

```text
Capture -> Extract -> Select -> Write -> Drill -> Review
```

- Capture：把对谈摘要、手动输入、零散资料、其他 AI 输出先丢进 `inbox/`。
- Extract：把原始输入提炼成结构化的 `work_item` 或 `ai_practice`。
- Select：判断哪些内容值得进入简历 claim，哪些只适合复习或补证据。
- Write：在 `resume/` 中生成和打磨中文主简历。
- Drill：在 `drill/` 中生成追问、攻防、STAR 回答和复习卡。
- Review：用 `next_review` 和 `memory_status` 让复习持续发生，而不是一次性题库。

## 最小目录

- `inbox/`：唯一低摩擦输入入口。
- `resume/`：中文主简历、候选 claim、输出草稿。
- `work/`：被提炼后的工作内容、项目、AI 实践、成果和关键故事。
- `drill/`：复习卡、追问链、模拟面试记录和阶段总结。
- `meta/`：模板、Obsidian Bases、Canvas、工作流和 agent 规则。
- `archive/`：历史材料。旧简历作为历史基线保存，不作为新版主结构。

## 当前版本策略

v1 只服务一份中文主简历。多岗位、公司定制、英文版都等核心闭环稳定后再派生。
