---
title: redis 发布/订阅
tags: redis,java
notebook: 
---

### 基本命令
- subscribe
 ```
 # 订阅一个或者多个频道
subscribe c1 c2  
 ```

- psubscribe
 ```
 # (通配符的方式)订阅一个或者多个频道
psubscribe c*  
 ```

- publish 
```
# 将消息发送到指定的频道
publish channel message
```