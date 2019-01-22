### vmstat


```
$ vmstat
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b 交换 空闲 缓冲 缓存   si   so    bi    bo   in   cs us sy id wa st
 0  0      0 775456 233388 3763904    0    0    23    27  225   89 12  5 83  1  0
```
### 参数

 - a
 ```
 Display active and  inactive memory, given a 2.5.41 kernel or better
 ```

### 输出字段意义

#### procs

- r
```
The number of runnable processes (running or waiting for run time).
```

- b
```
The number of processes in uninterruptible sleep.
```

#### memory

```
swpd: the amount of virtual memory used.
free: the amount of idle memory.
buff: the amount of memory used as buffers.
cache: the amount of memory used as cache.
inact: the amount of inactive memory.  (-a option)
 active: the amount of active memory.  (-a option)
```
#### IO

```
bi: Blocks received from a block device (blocks/s).
bo: Blocks sent to a block device (blocks/s).
```

#### system

```
# 每秒的中断数
in: The number of interrupts per second, including the clock.
# 每秒的上下文切换次数
cs: The number of context switches per second.
```




