---
title: "CatCode"
description: "用完即走的临时文件中转站：上传文件生成 6 位取件码，接收方输码即取，到期自动焚毁。"
date: "2026-10-01"
draft: false
showHeroImage: false
tags:
  - TypeScript
  - Node.js
  - Express
  - SQLite
categories:
  - 项目
series: []
comments: false
icon: "box"
iconColor: "var(--paper-accent)"
authors:
  - name: "张胖胖"
links:
  - label: "GitHub 源码"
    href: "https://github.com/JianWeiCat/CatCode"
    kind: "github"
---

CatCode 是一个用完即走的临时文件中转站。发送方上传文件后生成 6 位取件码，接收方输入取件码即可提取文件，文件到期自动焚毁。

## 项目概览

- 仓库创建：2026 年 10 月 1 日
- 项目类型：全栈 Web 应用
- 使用场景：通过简短取件码临时传递文件。

## 核心流程

1. 上传需要临时分享的文件。
2. 将生成的 6 位取件码告知接收方。
3. 接收方输入取件码提取文件，文件到期后自动清理。

## 技术实现

| 层级 | 技术选型 |
| --- | --- |
| 服务端 | Node.js、TypeScript、Express 5 |
| 文件上传 | Multer |
| 数据存储 | SQLite、better-sqlite3 |
| 开发与构建 | tsx、TypeScript 编译、静态资源复制脚本 |

仓库以 `src` 管理服务端代码，`frontend` 管理前端资源；构建通过 TypeScript 编译与资源复制生成运行目录。
