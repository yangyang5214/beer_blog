---
title: redis 主从复制
tags: redis,java
notebook: 
---


- slaveof
```
slaveof ip port


1、上一个slave可以作为下一个slave的master
2、中途变更转向，会清除之前的数据，重新建立拷贝新的
```


```
# 对一个从属服务器执行命令  slaverof no one 将使得这个从属服务器关闭复制功能，  并从从属服务器转变回主服务器，原来同步所得的数据集不会被丢弃。

# 利用『 SLAVEOF NO ONE 不会丢弃同步所得数据集』这个特性，可以在主服务器失败的时候，将从属服务器用作新的主服务器，从而实现无间断运行。

slaveof no one 
```

- info
```
# 主从信息
 info replication
```

- sentinel
```
哨兵
自动切换 

```
