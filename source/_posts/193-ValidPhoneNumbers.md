---
title: 193.ValidPhoneNumbers
date: 2019-03-24 21:32:05
tags: leetcode
---

> [193. Valid Phone Numbers
](https://leetcode.com/problems/valid-phone-numbers/)


### 题目
Given a text file file.txt that contains list of phone numbers (one per line), write a one liner bash script to print all valid phone numbers.

You may assume that a valid phone number must appear in one of the following two formats: (xxx) xxx-xxxx or xxx-xxx-xxxx. (x means a digit)

You may also assume each line in the text file must not contain leading or trailing white spaces.

#### Example:

Assume that file.txt has the following content:

```
987-123-4567
123 456 7890
(123) 456-7890
```
Your script should output the following valid phone numbers:
```
987-123-4567
(123) 456-7890
```
<!--more-->
### 解答

```
grep -P '^(\d{3}-|\(\d{3}\))\d{3}-\d{4}$' file.txt
```

(\d{3}-|\(\d{3}\))\d{3}-\d{4}

- (\d{3}-|\(\d{3}\))。| 表示或

-  d{3}: 匹配 [0-9] 三次
-  d{4}: 匹配 [0-9] 四次

