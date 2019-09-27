---
title: kafka
date: 2019-09-24 16:00:44
tags: java,kafka
---

> kafka 学习记录。https://www.bilibili.com/video/av65694302?from=search&seid=5090138521767409943


### 概述

#### 消息队列的好处


- 解耦。
- 可恢复性
- 缓冲。生产大于消费
- 灵活性 & 峰值处理能力

#### 消息队列的两种模式

- 点对点

一个消息只能有一个消费者可以消费。

- 发布订阅

一对多。消费者消费数据之后不会清楚消息

### 架构

- 生产者

- kafka 集群

topic cluster leader follower 

- 消费者

- zookeeper 注册消息


### 入门


#### 安装

https://blog.csdn.net/panchang199266/article/details/82113453


#### 命令行操作

```
bash kafka-topics.sh --zookeeper localhost:2181  --list
```

```
bash kafka-topics.sh --create  --zookeeper localhost:2181 --topic  zero --partitions 2 --replication-factor 2
```


