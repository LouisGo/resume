---
type: workflow
status: active
created_at: 2026-06-24
---

# Obsidian Setup

保持设置轻量。这个 vault 必须在“普通 Markdown + Obsidian 核心视图”的条件下可用。

## 必需

- 把当前仓库作为 Obsidian vault 打开。
- 启用核心插件：
  - Properties
  - Bases
  - Canvas
  - Backlinks
  - Graph view

## 推荐

- 如果本地 Obsidian 版本支持，启用 Obsidian CLI：
  - Settings -> General -> Command line interface
  - 用于搜索、读取笔记、截图和自动化。
- 从社区插件市场安装 Obsidian Git：
  - Plugin id: `obsidian-git`
  - 如果想在 Obsidian 内同步 Git，可启用桌面端自动 commit-and-sync。
  - 严肃恢复仍以普通 Git CLI 为准。

## 可选 AI Skills

kepano Obsidian skills 对 agent 工作有帮助，但默认应安装到 agent 环境，不要复制进这个 vault。

有用的 skills：

- `obsidian-markdown`
- `obsidian-bases`
- `json-canvas`
- `obsidian-cli`

## 不要做

- 没有强理由时，不要把社区插件源码 vendoring 到这个 vault。
- 不要让插件配置成为写简历和复习材料的前置阻塞。
