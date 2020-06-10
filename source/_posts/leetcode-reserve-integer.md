---
title: 整数反转(TODO)
date: 2020-06-10 21:26:34
tags: [leetcode,math]
---

https://leetcode-cn.com/problems/reverse-integer/


> 给出一个 32 位的有符号整数，你需要将这个整数中每位上的数字进行反转。
  
示例 1:

```
输入: 123
输出: 321
```
 示例 2:
```python
输入: -123
输出: -321
```
示例 3:

```python
输入: 120
输出: 21
```
注意:

假设我们的环境只能存储得下 32 位的有符号整数，则其数值范围为 [−231,  231 − 1]。请根据这个假设，如果反转后整数溢出那么就返回 0。

### 简单粗暴

```python

import sys


class Solution:
    def reverse(self, x: int) -> int:
        flag = True
        if x < 0:
            flag = False

        res = '0'
        s = str(x)
        length = len(s)
        if flag:
            last = s[length - 1]
            if last != '0':
                res = last
            for i in reversed(range(length - 1)):
                res = res + s[i]
        else:
            for i in reversed(range(1, length)):
                res = res + s[i]
            res = '-' + res
        return int(res)


if __name__ == '__main__':
    s = Solution()
    print(sys.maxsize)
    print(s.reverse(234))
    print(s.reverse(-123))
    print(s.reverse(120))

    # TODO ❌ 输出（但是 python3）
    print(s.reverse(1534236469))
```

