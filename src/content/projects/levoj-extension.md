---
title: "LevOJ 浏览器扩展"
description: "面向 LevOJ 题目页的 AI 编程助手，自动抓取题面、调用兼容接口并展示 C++ 参考代码。"
date: "2026-10-11"
draft: false
showHeroImage: false
tags:
  - JavaScript
  - 浏览器扩展
  - Manifest V3
  - AI
categories:
  - 项目
series: []
comments: false
icon: "code-2"
iconColor: "var(--paper-accent)"
authors:
  - name: "张胖胖"
links:
  - label: "GitHub 源码"
    href: "https://github.com/JianWeiCat/LevOJ--extension"
    kind: "github"
---

LevOJ 浏览器扩展是面向南信大学子的 Chrome / Edge 扩展。在题目页抓取题面，通过用户配置的 OpenAI 兼容接口请求 AI，并在页面悬浮窗中展示可复制的 C++ 参考代码。

## 项目概览

- 仓库创建：2026 年 10 月 11 日
- 项目类型：浏览器扩展
- 运行方式：Manifest V3，无需独立后端与构建步骤。

## 核心能力

- 优先通过题目接口抓取内容，接口不可用时回退到 DOM 抓取。
- 使用 Shadow DOM 隔离悬浮窗样式，支持拖动、折叠和代码复制。
- 由后台 Service Worker 代理 AI 请求，API Key 保存在本地扩展存储中。
- 遇到 429 限流时根据 `Retry-After` 退避重试，并显示中文错误提示。
- 在 `/exam`、`/contest`、`/compete` 页面自动禁用助手。

## 技术架构

| 层级 | 实现 |
| --- | --- |
| 扩展规范 | Chrome / Edge Manifest V3 |
| 页面交互 | JavaScript、Content Script、Shadow DOM |
| 后台请求 | Service Worker、Fetch、扩展消息通信 |
| 模型接入 | 用户配置的 OpenAI 兼容接口 |

请求链路为：Content Script 抓取题面 → 扩展消息通信 → 后台请求模型接口 → 悬浮窗展示结果。

生成代码用于参考和理解解题思路；项目 README 提醒使用者不要直接将其作为作业提交。
