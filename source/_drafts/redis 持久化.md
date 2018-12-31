---
title: redis 持久化
tags: reids,java
notebook: 
---


### rdb

redis database

> 在指定的时间间隔内将内存中的数据集快照写入磁盘。redis单独创建一个子进程(fork)来进行持久化。RDB的缺点是最后一次持久化的数据可能丢失。

##### fork

复制一个与当前进程一样的进程。新进程的所有数据（变量、环境变量、程序计数器等）数值都和原进程一致。是原进程的子进程。

```
save 900 1 
save 300 100
save 60 10000
```

- save 自动化的备份
-  stop-writes-on-bgsave-error yes/no 
-  rdbcompression yes/no 压缩存储
- rdbchecksum yes/no 当存储快照后，还可以让redis使用crc64算法来进行数据教研，但是会增加10%的性能消耗
- dbfilename 备份的数据文件名
- dir 目录文件


#### 触发rdb 的配置文件

- 配置文件的默认快照配置
- 命令save/bgsave(bgsave后台异步save)


> 优势：对大规模的数据恢复；对数据的完整性和一致性要求不高

> 劣势：丢失最后一次的修改；fork时候，内存的数据被克隆一份，2倍的膨胀性能需要考虑


### aof

append only file 

> 以日志文件方式来记录每个写操作，只追加文件但是不修改文件

保存的文件为 appendonly.aof

#### appendfsync

- always  同步持久化，每次发生数据变更会被立即记录到磁盘，性能差但是完整性比较好
- 异步操作，每秒记录

#### redis-check-aof --fix

#### rewrite 

AOP 采用追加的方式，文件会越来越大。新增了重写机制。当文件的大小超过所设定的阀值时。redis 就会启动AOF文件的内容压缩。只保留可以恢复数据的最小的指令集，可以使用命令 bgwriteaof

> 优势：每秒同步。

> 劣势：相同数据集而言，aof的文件要远大于rdb文件，恢复速度慢于rdb。运行效率慢于rdb。
