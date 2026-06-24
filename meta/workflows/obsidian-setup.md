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

## GitHub 账号绑定

这个仓库使用 `LouisGo/resume.git`，本机默认 `git@github.com` 可能会命中另一个 GitHub 账号。

当前仓库必须使用这个 remote：

```bash
git@github-louisgo:LouisGo/resume.git
```

本机 `~/.ssh/config` 中需要存在：

```sshconfig
Host github-louisgo
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_ed25519_typey
  IdentitiesOnly yes
```

验证命令：

```bash
ssh -T github-louisgo
```

期望输出里应出现：

```text
Hi LouisGo!
```

本仓库已设置：

```bash
git remote set-url origin git@github-louisgo:LouisGo/resume.git
git config --local user.name LouisGo
git config --local user.email xlzgogogo@foxmail.com
git push -u origin main
```

## Obsidian Git 推荐设置

安装并启用 Obsidian Git 后，建议：

- Auto pull on startup：开启。
- Pull updates on startup：开启，名称随插件版本可能略有差异。
- Auto backup interval：10 到 30 分钟。
- Commit message：`vault backup: {{date}}`。
- Pull before push：开启。
- Push after commit：开启。
- Disable push：关闭。

不要在移动端把 Obsidian Git 当主同步方案。移动端 Git 插件稳定性和认证能力都弱于桌面端。

## 日常同步流程

- 日常 Obsidian 笔记变更：让 Obsidian Git 自动 commit/pull/push。
- 重要对谈沉淀、脚本、模板、目录结构变更：使用普通 Git CLI 明确 commit。
- 如果出现冲突：暂停 Obsidian Git 自动同步，用普通 Git CLI 解决冲突，再恢复插件。
- 每次开始重要工作前：先 `git pull --ff-only`。
- 每次结束重要工作后：确认 `git status --short` 干净，或创建明确 commit。

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
