---
type: workflow
status: active
created_at: 2026-06-24
---

# 随机输入摄取协议

目标：允许用户用最低摩擦输入材料，同时保证材料最终能被 AI 提炼到正确位置。

## 输入方式

所有无序输入默认先进入 `inbox/`，不要一开始分类。

常见来源：

- 与 AI 的对谈记录
- 截图或图片
- 直接复制粘贴的文字
- PDF、Word、PPT、Excel
- 网页、文章、JD、面试反馈
- 其他 AI 工具生成的回答、总结、导出文件

## 摄取原则

- 保留原始材料，不要在第一步就改写成结论。
- 对每份材料创建或补充一个 `input_note`。
- 如果是文件或截图，`input_note` 必须链接到原始文件路径或转换后的 Markdown。
- AI 可以提炼、压缩、归类，但不能凭空补事实。
- 任何可进入简历的强表述都必须从 `input_note` 流向 `work_item` / `ai_practice`，再流向 `resume_claim`。
- 高价值或高风险 claim 必须继续生成 `drill_item`。

## 输入到输出的判定

收到随机输入后，AI 必须先判断它属于哪类：

- `source_only`：只作为证据或原始材料保存。
- `extractable`：可以提炼成 `work_item` 或 `ai_practice`。
- `claim_candidate`：有潜力进入简历，需要生成 `resume_claim`。
- `drill_candidate`：适合生成复习或攻防题。
- `discard_or_archive`：价值低、重复、无法确认，暂不进入主流程。

## 对谈记录

对谈是最重要的输入来源。

处理方式：

1. 生成一条 session digest，保存为 `inbox/YYYY-MM-DD-topic.md`。
2. 在 digest 中记录关键事实、候选亮点、待确认问题。
3. 把已确认事实提炼为 `work_item` 或 `ai_practice`。
4. 把可写入简历的表达提炼为 `resume_claim`。
5. 对高风险内容创建 `drill_item`。

## 截图和图片

截图本身不是事实结论，只是来源材料。

处理方式：

1. 截图文件放入 `inbox/attachments/`，或保持原路径并在 `input_note` 中链接。
2. 使用 AI vision 或 OCR 提取文字和可见上下文。
3. 将识别结果写入 `input_note` 的“原始输入”或“转换文本”部分。
4. 标记识别可信度：`high`、`medium`、`low`。
5. 低可信度内容不得直接进入 `resume_claim`。

## 文件材料

PDF、Word、PPT、Excel、HTML、CSV、JSON、XML、ZIP 等文件优先转换成 Markdown，再进入 inbox。

推荐工具：

- 首选：MarkItDown，用于把多种文件转换为 LLM 友好的 Markdown。
- 备选：Pandoc，用于更重视格式保真或传统文档转换的场景。
- 截图/OCR：优先使用 AI vision；批量文件再考虑 MarkItDown OCR 插件或其他 OCR 工具。

转换后的 Markdown 不等于事实确认，只是更容易被 AI 读取的来源材料。

## MarkItDown 使用建议

适合使用：

- PDF、DOCX、PPTX、XLSX
- HTML、CSV、JSON、XML
- ZIP 包中的批量资料
- 需要喂给 AI 分析的长文档

谨慎使用：

- 机密文件
- 来路不明的文件
- 需要高保真排版的文件
- OCR 质量会影响判断的截图或扫描件

推荐输出位置：

```text
inbox/converted/YYYY-MM-DD-source-name.md
```

原始文件如果需要保留，放在：

```text
inbox/attachments/
```

## 标准脚本

本仓库提供轻量脚本：

```bash
scripts/import-file path/to/source.pdf optional-topic-slug
```

脚本会：

1. 调用本机 `markitdown` 命令转换源文件。
2. 输出 Markdown 到 `inbox/converted/`。
3. 创建一条链接转换结果的 `input_note`。
4. 不会自动生成 `work_item`、`resume_claim` 或 `drill_item`，这些必须由 AI 读取材料后判断。

MarkItDown 不作为仓库依赖安装。推荐按需安装：

```bash
uv tool install "markitdown[all]"
```

或：

```bash
pipx install "markitdown[all]"
```

## Agent 处理清单

每次处理 inbox 时，AI 应完成：

1. 识别输入类型和来源。
2. 标记 `processed_status`。
3. 提炼事实、亮点、风险、证据状态。
4. 决定是否生成 `work_item`、`ai_practice`、`resume_claim`、`drill_item`。
5. 保持所有派生内容回链到原始输入。
6. 把无法确认的问题写入“待确认问题”，不要猜。
