---
title: "测艺物联网管理平台（Ceyi IoT）"
description: "基于 Spring Boot 与 Vue 3 的物联网设备监测管理平台，覆盖多协议接入、数据采集、告警、GIS 可视化与远程管控。"
date: "2026-08-08"
draft: false
showHeroImage: false
tags:
  - 物联网
  - Vue 3
  - Spring Boot
  - InfluxDB
  - Kafka
categories:
  - 项目
series: []
comments: false
icon: "database"
iconColor: "var(--paper-accent)"
authors:
  - name: "张胖胖"
links:
  - label: "在线体验"
    href: "http://8.137.16.243/"
    kind: "demo"
  - label: "Gitee 源码"
    href: "https://gitee.com/zhang-big-cat/iot-platform-3"
    kind: "platform"
---

测艺物联网管理平台（Ceyi IoT）是一个面向物联网系统集成商、设备运维团队和企业用户的业务中台。项目基于若依快速开发框架（RuoYi-Vue）定制构建，统一整合设备管理、传感器数据采集、告警和可视化能力。

## 项目概览

- 项目状态：开发中
- 当前版本：v3.9.0 / v3.9.1
- 项目类型：物联网平台
- 核心目标：实现物联网设备的统一接入、传感器数据实时监控与远程设备管控。

## 核心能力

### 多协议设备接入与数据采集

支持 MQTT、Netty TCP、WebSocket 等多种设备连接方式。传感器数据写入 InfluxDB 时序数据库，并通过 Kafka 消息队列完成数据流转，为后续的实时分析与告警处理提供基础。

### 设备远程管控与物模型

平台支持闸门绝对值 / 百分比控制、摄像头操控等设备指令下发，并提供物模型定义和数据规则引擎能力，让不同类型设备可以在统一的管理模型下接入与控制。

### 实时大屏与 GIS 可视化

使用 ECharts 构建仪表盘与传感器趋势图，结合 Three.js 3D 地图展示设备分布、运行数据和告警信息，帮助运维人员更直观地了解设备现场状态。

## 技术架构

| 层级 | 技术选型 |
| --- | --- |
| 前端 | Vue 3.5、Vite 6、Element Plus 2.10、Pinia 3、Vue Router 4、Axios |
| 可视化 | ECharts 5、Three.js 0.124 |
| 后端 | Java 8、Spring Boot 2.5、Spring Security + JWT、MyBatis、Maven 多模块 |
| 数据存储 | MySQL、InfluxDB、Redis |
| 消息与接入 | MQTT、Kafka、Netty TCP、WebSocket |
| 文件服务 | MinIO |

## 后续计划

面向麒麟系统服务器环境继续完善部署方案；时序数据库计划评估和迁移至 openGauss，业务数据库计划使用 TiDB 社区版，以增强平台在国产化环境下的适配能力和扩展性。
