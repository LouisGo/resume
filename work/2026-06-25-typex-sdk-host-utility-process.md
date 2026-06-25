---
type: work_item
created_at: 2026-06-25
role: 前端架构师 / Electron SDK Host 治理负责人
context: TypeX PC 原生 SDK 调用集中在 Electron main process，存在同步调用、热路径阻塞、生命周期耦合和故障边界过大的问题
action: 推进 SDK 调用异步化、Bridge invoke/RPC 化，并将 SDK 生命周期与高频 Bridge API 迁移到 utilityProcess SDK Host
result: 主进程职责收敛为窗口、权限、Bridge 编排和进程监管，原生 SDK 风险被隔离到可观测、可回收的独立进程边界
ai_relevance: medium
evidence_status: verified-by-user-and-git-history
source_links:
  - "[[inbox/2026-06-25-typex-utility-process-resume-rewrite]]"
claim_links:
  - "[[resume/claims/2026-06-25-typex-sdk-host-utility-process-claim]]"
drill_links:
  - "[[drill/2026-06-25-attack-typex-sdk-host-utility-process]]"
tags:
  - work-item
  - typex-pc
  - electron
  - sdk-host
  - utility-process
---

# TypeX PC Electron SDK Host 与 utilityProcess 治理

## 背景

TypeX PC 依赖原生 SDK 承接 IM 核心能力。早期 SDK 调用与 Electron main process 耦合较深，存在同步调用、热路径读取、SDK 生命周期、回调事件、退出清理和异常传播等多个风险点。

这类问题不只是 API 封装问题，而是 Electron 桌面端的进程边界和稳定性治理问题：main process 需要保持轻量，不能长期承载高频 SDK 调用和复杂业务协议。

## 我的角色

主导 SDK Host 治理方向，将 SDK 调用链路从 main process 内部封装升级为独立 runtime host：main process 负责窗口、权限、Bridge 编排和监管，utilityProcess 承接 SDK 生命周期、RPC 调用、事件分发和故障隔离。

## 关键行动

- 将同步 SDK Bridge 调用逐步改造为 `invoke` / RPC 异步模型，避免渲染端或 main process 被同步调用链路拖住。
- 设计 utility RPC contract 和 bridge handler helpers，统一参数、返回值、错误和事件的协议边界。
- 将 SDK lifecycle 移入 utilityProcess，补齐启动 readiness、初始化等待、退出 drain、shutdown failure 暴露和 supervisor 监管。
- 将 feed、message、file、me、rest、rtc、device、emoji 等高频 Bridge API 分批路由到 utility runtime。
- 建立 bootstrap logging、failure detail、contract coverage 和关键 invariants 测试，提升故障定位和回归验证能力。

## 结果

- main process 从 SDK 执行者收敛为薄调度层和进程监管层。
- 原生 SDK 的阻塞、崩溃、协议变更和生命周期异常被隔离到 utilityProcess 边界内。
- SDK 调用链路具备更清晰的 contract、日志和测试保护，降低后续扩展 AI tools、消息能力和高频 Bridge API 时继续污染 main process 的风险。

## 为什么重要

这条材料能证明候选人不是只会写 Electron UI，而是能处理桌面端底层进程模型、原生 SDK 风险、异步 RPC 协议、启动退出生命周期和稳定性治理。

## 证据

- 用户确认 utilityProcess 已完成上线，可作为当前简历事实表达。
- `~/Work/typex-pc` git 历史中存在 `lou-utility-process-impl` 相关提交，覆盖 utility process supervisor、utility RPC contracts、SDK lifecycle 迁移、高频 Bridge API 路由、启动/退出异常治理和 contract tests。

## 简历潜力

- 候选 claim：主导 Electron SDK Host 治理，将原生 SDK 从 main process 迁移到 utilityProcess，完成同步调用异步化、RPC contract、生命周期监管和故障隔离。
- 风险：不要写没有证据的性能百分比或崩溃率下降指标。
- 需要补证据：上线范围、线上稳定性反馈、迁移 API 清单、技术分享材料。
