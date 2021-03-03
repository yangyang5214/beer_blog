---
title: read large file line by line with python 
date: 2021-03-03 08:18:40
tags: python
---

用python逐行读取大文件


> 由一个脚本引起的服务器内存不足, 然后引起的一系列报错，日志越来越大，导致磁盘空间不足，然后引起 es 搜索结果缺失 😂

python 为例子


### 初始化

我的树莓派没跑什么东西，内存基本稳定

![](https://beef-1256523277.cos.ap-chengdu.myqcloud.com/bed/20210303083903.png)

准备文件 test.txt (自己生成) 1.1G

```
pi@raspberrypi:~$ ls -lh | grep test.txt
-rw-r--r-- 1 pi   pi   1.1G Mar  3 00:17 test.txt
```

<!--more-->

### 几种方式

#### f.readlines()

```
with open('test.txt', 'r') as f:
    for line in f.readlines():
        print line
```

执行上列脚本之后， 内存直接到了 2.84G (增加了2.5G), 

可以看出是因为 *f.readlines()* 的执行，导致数据都 load 到了内存， 终端看到*print line* 执行期间，内存没变化，稳定在 2.84G

![](https://beef-1256523277.cos.ap-chengdu.myqcloud.com/bed/20210303082456.png)


kill 掉脚本，内存恢复正常

![](https://beef-1256523277.cos.ap-chengdu.myqcloud.com/bed/20210303082925.png)

```
# 官网
If you want to read all the lines of a file in a list you can also use list(f) or f.readlines().
```

#### f.readline()


```
with open('test.txt', 'r') as f:
    for line in f:
        print line

# 等价
with open('test.txt', 'r') as f:
    while True:
        line = f.readline()
        print line
        if not line:
            break
```

逐行读取内存，每次只有一行数据在内存中
 
可以看到，除了 cpu 变化，内存基本没变化。（但是会慢）

![](https://beef-1256523277.cos.ap-chengdu.myqcloud.com/bed/20210303083903.png)


### Reference

https://docs.python.org/3.4/tutorial/inputoutput.html#methods-of-file-objects

https://www.blopig.com/blog/2016/08/processing-large-files-using-python/


