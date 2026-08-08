---
title: "工笔画批量重绘（Gongbi Batch Repaint）"
description: "基于 Qwen-Image-Edit-2511 与工笔 LoRA 的单机 GPU 批处理平台，支持批量风格生成、语义编辑和图像分割。"
date: "2025-01-01"
draft: false
showHeroImage: false
tags:
  - AI 图像生成
  - Qwen
  - LoRA
  - FastAPI
  - React
categories:
  - 项目
series: []
comments: false
icon: "sparkles"
iconColor: "var(--paper-accent)"
authors:
  - name: "张胖胖"
links:
  - label: "应用源码"
    href: "https://github.com/SanJin-09/brushPainting"
    kind: "github"
  - label: "LoRA 训练源码"
    href: "https://github.com/SanJin-09/qwen-gongbi-lora"
    kind: "github"
---

工笔画批量重绘是一个将 AI 图像模型能力产品化的 Web 平台：用户可在浏览器中上传多张图片，批量生成中国传统工笔画风格作品，并进一步使用自然语言进行语义编辑或使用 SAM 3 完成文本驱动的图像分割。

## 项目概览

- 项目状态：开发中
- 当前版本：v0.1.0（2025 年启动）
- 项目类型：AI 图像应用
- 目标用户：需要批量将照片转为工笔画风格的设计师、艺术创作者，以及关注 AI 图像生成的技术用户。

项目以已有的 `qwen-gongbi-lora` 工笔画风格 LoRA 为基础，将模型训练、GPU 推理与浏览器端工作流连接起来，让用户无需直接操作命令行或 GPU 服务器即可使用模型能力。

## 核心能力

### 批量工笔画风格生成

支持多图上传与统一批处理，通过 Qwen-Image-Edit-2511 结合工笔风格 LoRA 生成具有传统工笔画特点的图像结果。

### 自然语言语义编辑

用户可通过自然语言描述调整图像内容，例如修改服装颜色，在保留画面主体和风格的基础上完成针对性编辑。

### 图像分割与素材导出

集成 SAM 3 的开放词汇分割能力，可根据文本生成 Mask，并导出透明背景的子图素材，为后续设计加工提供便利。

## 我的职责

该项目在已有基础上由我后期接手，负责将模型能力进一步工程化整合，并持续维护和迭代产品功能。

- 维护并完善 FastAPI 后端 API、SQLite 数据存储，以及 Redis / RQ 异步任务队列。
- 整合 GPU 推理 Worker，包括 DiffSynth-Studio、工笔 LoRA 与 SAM 3 的推理流程。
- 维护前端 SPA，使用 React 19、TypeScript、Zustand 与 React Router 对接完整业务流程。
- 接手并维护 LoRA 训练数据管线：Openverse 数据收集、豆包 SeedEdit 风格转换、人工审核、数据集导出与 DiffSynth-Studio LoRA 训练。
- 完善 API Key、HttpOnly Cookie Session、CSRF Token 等认证与安全防护，并补充 Docker Compose 一键部署与离线部署方案。

## 技术挑战

### 单 GPU 模型显存协调

Qwen 图像编辑模型与 SAM 3 同时加载会产生显存竞争。通过任务队列将 GPU 任务串行化，并在切换模型时主动释放缓存，避免多个大模型并发占用单张 GPU 显存。

### 面向网络发布的安全模型

项目从单机无认证形态扩展至可网络部署的应用：增加强制 API Key 校验、Secure Cookie、CSRF 防护，以及启动时的配置合规检查，降低公开部署的安全风险。

## 技术架构

| 层级 | 技术选型 |
| --- | --- |
| 前端 | React 19、TypeScript、Vite、Zustand、React Router 7、Axios、Playwright E2E |
| 后端 | FastAPI、SQLite、Redis、RQ 任务队列 |
| 模型与推理 | Qwen-Image-Edit-2511、工笔风格 LoRA、SAM 3、DiffSynth-Studio |
| 训练数据 | Openverse、豆包 SeedEdit API、人工审核与数据集导出 |
| 部署 | Docker Compose；支持纯 CPU Mock 模式、GPU 生产模式与离线部署 |

## 当前成果与后续计划

目前后端 API 已覆盖上传、生成、编辑、分割与导出全流程，前端 SPA 已对接全部 API；LoRA 训练管线与安全认证体系已具备，Mock 模式可在无 GPU 环境验证完整流程。

下一步将完成 GPU 生产环境的正式推理验证，测试 SAM 3 权重的实际分割质量与显存表现，并围绕批次上传、生成进度、版本查看、语义编辑和分割体验重构前端 UI。
