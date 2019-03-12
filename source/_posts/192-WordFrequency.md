---
title: 192.WordFrequency
date: 2019-03-12 20:00:00
tags: leetcode
---


> [192. Word Frequency](https://leetcode.com/problems/word-frequency/)


### 192. Word Frequency

Write a bash script to calculate the frequency of each word in a text file words.txt.

For simplicity sake, you may assume:

- words.txt contains only lowercase characters and space ' ' characters.
- Each word must consist of lowercase characters only.
- Words are separated by one or more whitespace characters

Example:

Assume that words.txt has the following content:

```
the day is sunny the the
the sunny is is
```
Your script should output the following, sorted by descending frequency:

```
the 4
is 3
sunny 2
day 1
```

<!--more-->

### 解题

>  哈哈，写了好久，用什么map存储之类的，但是最后没实现。直接参考的正确答案。确实好多命令没接触过。

```
cat words.txt | tr -s ' ' '\n' | sort | uniq -c | sort -r | awk '{ print $2, $1 }'
```

- cat words.txt
```
读文件
```

- tr -s ' ' '\n'
```
# 替换
tr 参数1 参数2 

-s  参数
针对 参数1 设置的，将参数 1 中连续的字符缩减为 1 个

eg: tr -s ' ' ' '

tr -s ' ' '\n' 的含义是：将连续的空格替换为 1 个，并且空格用 \n 替换
```
- sort | uniq -c | sort -r 
```
sort   排序
uniq -c   去重计数
sort -r    倒序排序
```
- awk '{ print $2, $1 }'
```
格式化打印， 字母和数字顺序颠倒
```





