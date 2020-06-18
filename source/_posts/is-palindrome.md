---
title: is_palindrome
date: 2020-06-19 07:21:59
tags: math
---

https://leetcode-cn.com/problems/palindrome-number/comments/

> 判断回文数


```
判断一个整数是否是回文数。回文数是指正序（从左向右）和倒序（从右向左）读都是一样的整数。

示例 1:

输入: 121
输出: true
示例 2:

输入: -121
输出: false
解释: 从左向右读, 为 -121 。 从右向左读, 为 121- 。因此它不是一个回文数。
示例 3:

输入: 10
输出: false
解释: 从右向左读, 为 01 。因此它不是一个回文数。
进阶:

你能不将整数转为字符串来解决这个问题吗？
```


### 解决

- 负数不是 
- length ==1 是
- 一次遍历

```
class Solution:
    def isPalindrome(self, x: int) -> bool:
        if x < 0:
            return False
        s = str(x)
        length = len(s)
        if length == 1:
            return True
        for i in range(length):
            if s[i:i + 1] != s[length - i - 1:length - i]:
                return False
        return True
```

### 简单的

评论区看到一个流氓做法

```python
str(x)[::1] === str(x)
```
