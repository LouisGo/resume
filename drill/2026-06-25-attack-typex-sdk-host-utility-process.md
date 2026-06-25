---
type: drill_item
created_at: 2026-06-25
claim_link: "[[resume/claims/2026-06-25-typex-sdk-host-utility-process-claim]]"
question_type: attack
difficulty: hard
next_review: 2026-06-28
memory_status: new
tags:
  - drill
  - typex-pc
  - electron
  - sdk-host
---

# 攻防：TypeX SDK Host 为什么要迁移到 utilityProcess

## 面试官问题

你说把 SDK 从 main process 迁移到 utilityProcess，这听起来像 Electron API 替换。为什么这算架构治理？迁移前具体痛点是什么，迁移后怎么证明更稳定？

## 为什么危险

这个 claim 很强，但如果只回答「为了不卡 main」会显得浅。需要讲清楚同步 SDK 调用、热路径读取、原生崩溃边界、Bridge 协议、生命周期、启动 readiness、退出 drain、日志和 contract tests。

## 强回答结构

- Situation：TypeX PC 依赖原生 SDK，早期 SDK 调用和生命周期集中在 main process，高频 Bridge 调用、回调事件、初始化/退出和异常传播都挤在一个进程边界里。
- Task：目标不是简单换 API，而是把 main process 收敛为轻调度层，建立可监管、可回收、可测试的 SDK Host。
- Action：先把同步调用改为 `invoke` / RPC；定义 utility RPC contract；迁移 SDK lifecycle；分批路由 feed、message、file、me、rest、rtc 等 API；补 readiness、shutdown drain、failure detail、logging 和 contract tests。
- Result：SDK 阻塞和异常被隔离到 utilityProcess；main process 保持窗口、权限、Bridge 编排和 supervisor；后续高频 SDK API 和 AI 工具接入不再继续污染 main process。
- Tradeoff：多了一层 RPC 和进程管理复杂度，所以必须用 contract tests、统一错误模型、启动超时和日志链路抵消复杂度。

## 连环追问

- 为什么不用 worker thread，而是 utilityProcess？
- SDK 初始化失败时 renderer 会看到什么错误？
- utilityProcess 崩了，main process 如何恢复或降级？
- RPC contract 如何做向后兼容？
- 哪些 API 最适合迁移，哪些应该留在 main？
- 迁移期间如何避免一次性大爆炸？

## 复习记录
