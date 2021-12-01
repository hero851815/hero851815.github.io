---
title: "星环TDH 笔记"
date: 2021-12-01T15:50:00+08:00
lastmod: 2021-12-01T15:50:00+08:00
draft: false
author: "hero851815"
authorLink: ""
description: "星环TDH"

tags: ["tdh"]
categories: ["documentation"]

lightgallery: true
hiddenFromHomePage: true

toc:
  auto: true
---

{{< admonition info "">}}
Transwarp Data Hub是基于Hadoop技术架构产生的大数据平台
{{</ admonition >}}

**TDH架构图**
![架构图](/images/tdh_architecture_diagram.png "架构图")

## 1 基础概念
### 服务
>基于TDH6.0整理

| 服务 | 描述 |
| ------ | ----------- |
| Zookeeper | 分布式应用 |
| HDFS | 分布式文件系统 |
| YARN | 分布式资源调度 |
| Inceptor | 分布式SQL引擎 |
| Inceptor-Gateway | 连接客户端和Inceptor服务的中间件 |
| Hyperbase | NoSQL数据库 |
| Discover | 数据科学分析探索工具 |
| Slipstream | 支持事件驱动和微批处理的流处理引擎 |
| Slipstream Studio | 流处理引擎的监控管理平台 |
| Search | 可扩展的分布式全文搜索和分析引擎 |
| Workflow | 工作流调度平台 |
| Transporter | 数据etl工具 |
| Pilot | 轻量级自助式分析BI工具 |
| Governor | 元数据管理和数据治理工具 |
| Rubik | Cube设计工具 |
| TxSQL | 强一致、高可用的分布式数据库系统 |
| Kafka | 分布式消息队列 |
| Codis | 分布式Redis解决方案 |
| 日志分析组件 | 分布式日志收集和分析系统 |
| Shiva | 内置存储框架 |
| Transwarp Manager | 部署、管理和运维TDH集群的管理组件 |
| Kerberos | 认证类型 |
| Guardian | 平台安全管控组件 |
| TCOS Market | 云操作系统市场 |

___

## 2 安装
由星环工程师负责安装
### 环境描述


___

## 3 配置


___


## to be continued