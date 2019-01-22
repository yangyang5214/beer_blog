### wc

> wc , word count , 统计指定文件中的字节数、字数、行数，并将统计结果显示输出

### 命令格式

wc [选项] 文件

### 命令参数

- c 统计字节数
- l  统计行数
- m 统计字符数
- w 统计字数
- L 打印最长行的长度

### 命令实例

wc  readme
```
#  readme 文件的统计
$ wc readme 
 8  6 91 readme
 行数  单词数   字节数 
```


wc  -l readme    行数
```
$ wc -l readme 
8 readme
```
wc  -w readme  单词数
```
$ wc -w readme 
6 readme
```

wc -c readme 字节数
```
$ wc -c readme 
91 readme
```


统计单词数，但是不打印文件名
```
cat readme | wc -w 
```

统计当前目录下的文件数
```
ll | wc -l

# 总数为 6
$ ls -i  | wc -l
6

#　当前目录的文件数（6 个文件（夹））
$ ls -i 

39583745 idea                    40370661 navicat                         39452674 pycharm
39070625 ideaIU-2018.2.5.tar.gz  39071468 navicat121_mysql_en_x64.tar.gz  39452673 pycharm-professional-2018.2.4.tar.gz
```

统计当前目录下文件的个数（不包括目录）

```
$ ls -l | grep "^-" | wc -l

```
