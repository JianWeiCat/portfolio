---
title: "GitCat"
description: "纯 Rust + Tauri 2 跨平台 Git 仓库增强桌面工具箱，提供安全、高效的图形化 Git 管理体验。"
date: "2026-07-01"
draft: false
showHeroImage: false
tags:
  - Rust
  - Git
  - Tauri
  - Git 可视化
  - 开发者工具
  - Agent
categories:
  - 项目
series: []
comments: false
icon: "terminal"
iconColor: "var(--paper-accent)"
authors:
  - name: "张胖胖"
links:
  - label: "GitHub 源码"
    href: "https://github.com/JianWeiCat/Git-Helper"
    kind: "github"
---

GitCat 是“Git 可视化图形操作平台”的正式项目名称。它是一个由 Rust 与 Tauri 2 构建的跨平台 Git 仓库增强桌面工具：以原生图形界面替代分散的 Shell 脚本和命令行操作，帮助开发者更安全、高效地管理多个本地 Git 仓库。

## 项目概览

- 项目状态：开发中
- 开始时间：2026 年 7 月
- 项目类型：开发者工具
- 目标用户：需要频繁管理多个 Git 仓库的开发者。

项目关注四类日常痛点：手动拼接 `git log` 统计并编写周报繁琐；逐仓库 `cd && git pull` 效率低；分支删除和 `git reset --hard` 缺少安全兜底；传统 Shell 脚本在 Windows 上难以直接复用。

## 核心能力

### 贡献统计与仓库洞察

一键查看代码行数、作者排行和语言分布，并支持导出 Markdown / CSV 报表，减少手工整理提交记录的时间。

### 多仓库批量运维

跨目录发现 Git 仓库，并发执行 status、pull、gc 等任务；每个仓库独立报告结果，用户可勾选授权实际操作。

### 安全守护架构

针对分支删除与 `reset --hard` 提供 dry-run 预演、白名单保护、自动备份分支与追加式审计日志；所有破坏性操作都需经过 Rust 后端二次校验。

## 我的职责

作为独立开发者，负责 GitCat 的整体架构、核心功能与桌面端实现。

- 负责整体架构设计，以及 Rust 核心库中的 gix 纯 Rust Git 操作、统计引擎、安全层与多仓库并发扫描 / 执行能力。
- 开发 Tauri 桌面端：React 19 + TypeScript 前端与 Rust Tauri command 后端协作，覆盖仓库发现、变更、提交历史、分支、远端同步、仓库洞察和批量任务等功能区。
- 编写 87 条工程约束规范，以及 Agent 模块技术方案与验收边界。

## 技术挑战

### 不依赖系统 Git 的跨平台实现

使用 gix（gitoxide）完成纯 Rust 的仓库读取、提交遍历、引用解析与 Diff 统计，不依赖系统 Git 二进制，从而保持 Windows、macOS 和 Linux 的行为一致性。

### 破坏性 Git 操作的安全架构

针对 `reset --hard`、分支删除和 force push 等高风险操作，构建自动备份分支、不可覆写的追加式审计日志、dry-run 预览和 Tauri command 层二次校验，确保安全策略不只依赖前端确认弹窗。

### Rust 与 React 的异步状态同步

通过异步请求序号绑定机制，避免用户快速切换仓库时旧请求结果覆盖新仓库状态，提升桌面端的数据一致性。

## 技术架构

| 层级 | 技术选型 |
| --- | --- |
| 核心 | Rust 2021 Edition、gix（gitoxide）、rayon、walkdir |
| 桌面端 | Tauri 2、React 19、TypeScript、@lobehub/ui、Ant Design 6、Lucide React、Motion、Vite 8 + Rolldown |
| 本地数据 | SQLite（规划用于 Agent 模块与审计存储） |
| 安全与发布 | `cargo tauri build` 单文件可执行程序、LTO fat、strip；不依赖系统 Git |
| Agent 规划 | OpenAI-compatible API；仅限已注册的结构化工具，所有写操作均需人工审批 |

## 当前成果与后续计划

Tauri 桌面端已覆盖八大功能区域：仓库发现、变更（分组 Diff、暂存与提交）、提交历史（多泳道拓扑图、引用标签与父提交关联）、分支管理（安全删除与保护标识）、远端同步、仓库洞察、批量任务，以及已完成技术方案设计的 Agent 模块。项目同时沉淀了 87 条工程规范，覆盖数据边界、安全边界、性能门禁及前后端交互验收标准。

后续将先实现 Agent Phase 0：提供流式对话、Diff 审查与提交说明生成等只读能力，所有写操作必须经人工审批，且 Agent 不执行任意 Shell 命令。随后会完善全盘扫描的可见进度与取消能力，以及批量任务的阶段进度、失败重试、单仓库取消和 GitHub / GitLab API 远端仓库浏览能力。
