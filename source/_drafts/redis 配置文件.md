---
title: redis 配置文件
tags: redis,java
notebook: 
---

### 启动

Redis must be started with the file path as first argument:
```
./redis-server /path/to/redis.conf
```

大小写不敏感
units are case insensitive so 1GB 1Gb 1gB are all the same.

### include

 指定包含其它的配置文件，可以在同一主机上多个Redis实例之间使用同一份配置文件，而同时各个实例又拥有自己的特定配置文件

### tcp-backlog

 ### timeout 
 ### keepalive 
 ### 日志级别 
 ### 数据库的存放位置
 ### 最大内存限制
 ### 清除key(过期策略)
 
 
