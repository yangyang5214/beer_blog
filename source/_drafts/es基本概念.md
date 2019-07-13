---
title: es基本概念
date: 2019-07-13 19:27:43
tags: [elasticsearch,Elasticsearch核心技术与实战]
---

> es 的一些基本概念。

### 索引

index. 类比为 mysql 数据库。

### 文档

document. 类比为 mysql 记录行。


### 节点

一个 es 的实例。本质是一个 JAVA 进程。

#### data node

保存数据的节点。


#### coordinating node 

接受 client 请求，并将请求分发到合适的节点。最终把结果汇总。没个节点都是coordinating node 的职责。

### 分片

#### 主分片

primary shard

在索引创建时指定，后续不允许修改，处分 Reindex

#### 副本分片

replica shard



