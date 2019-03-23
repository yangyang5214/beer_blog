---
title: awk
date: 2019-03-20 19:10:57
tags: shell
---

> 处理文本文件的语言。

默认按照空格或者TAB分割

### 数据准备

cat netstat 
```
Proto Recv-Q Send-Q  Local Address          Foreign Address        (state)    
tcp4       0      0  localhost.54752        202.108.23.152.https   ESTABLISHED
tcp4       0      0  localhost.54751        del01s08-in-f206.https SYN_SENT   
tcp4       0      0  localhost.54750        del01s08-in-f206.https SYN_SENT   
tcp4       0      0  localhost.54748        del01s08-in-f206.https SYN_SENT   
tcp4       0      0  localhost.54747        112.65.69.50.https     ESTABLISHED
tcp4       0      0  localhost.54744        del01s08-in-f206.https SYN_SENT   
tcp4       0      0  localhost.54739        59.81.65.17.https      ESTABLISHED
```

### 基本使用

awk '{print $1,$4}' netstat 
```
# 打印第 1 列 and 第 4 列
Proto Local
tcp4 localhost.54752
tcp4 localhost.54751
tcp4 localhost.54750
tcp4 localhost.54748
tcp4 localhost.54747
tcp4 localhost.54744
tcp4 localhost.54739
```

awk '{print $0}' netstat 
```
# $0 表示整个行，效果类似于 cat 
Proto Recv-Q Send-Q  Local Address          Foreign Address        (state)    
tcp4       0      0  localhost.54752        202.108.23.152.https   ESTABLISHED
tcp4       0      0  localhost.54751        del01s08-in-f206.https SYN_SENT   
tcp4       0      0  localhost.54750        del01s08-in-f206.https SYN_SENT   
tcp4       0      0  localhost.54748        del01s08-in-f206.https SYN_SENT   
tcp4       0      0  localhost.54747        112.65.69.50.https     ESTABLISHED
tcp4       0      0  localhost.54744        del01s08-in-f206.https SYN_SENT   
tcp4       0      0  localhost.54739        59.81.65.17.https      ESTABLISHED
```
### 格式化

awk '{printf "%-20s %-10s\n", $1,$4}' netstat 
```
Proto                Local     
tcp4                 localhost.54752
tcp4                 localhost.54751
tcp4                 localhost.54750
tcp4                 localhost.54748
tcp4                 localhost.54747
tcp4                 localhost.54744
tcp4                 localhost.54739
```

### NR

上面的第三列，应该为 Local Address ,但是解析为了  Local。所以，如果不想输出第一行。

awk 'NR !=1 {print $1,$4}' netstat

```
tcp4 localhost.54752
tcp4 localhost.54751
tcp4 localhost.54750
tcp4 localhost.54748
tcp4 localhost.54747
tcp4 localhost.54744
tcp4 localhost.54739
```
### 指定分隔符

指定以 . 为分隔符(结果比价乱，但是是对的)

awk -F . '{print $1,$4}' netstat 
```
Proto Recv-Q Send-Q  Local Address          Foreign Address        (state)     
tcp4       0      0  localhost 23
tcp4       0      0  localhost 
tcp4       0      0  localhost 
tcp4       0      0  localhost 
tcp4       0      0  localhost 69
tcp4       0      0  localhost 
tcp4       0      0  localhost 65
```
### 综合例子

### 输出第 n 行的记录

[LeetCode 195.TenthLine](https://www.hexianwei.com/2019/03/16/195-TenthLine/)
```shell
awk 'NR == 2' netstat
```
#### 统计文件夹的大小

```shell
ls -l | awk '{sum+=$5} END  {print sum}'
```

#### 9x9 乘法表

```shell
seq 9 | sed 'H;g' | awk -v RS='' '{for(i=1;i<=NF;i++)printf("%dx%d=%d%s", i, NR, i*NR, i==NR?"\n":"\t")}'
```

#### 找出长度大于 30 的行

```shell
awk 'length > 30' netstat 
```

