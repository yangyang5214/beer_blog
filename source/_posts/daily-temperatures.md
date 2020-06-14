---
title: daily_temperatures
date: 2020-06-14 08:56:54
tags: stack
---

https://leetcode-cn.com/problems/daily-temperatures/comments/

> 请根据每日 气温 列表，重新生成一个列表。对应位置的输出为：要想观测到更高的气温，至少需要等待的天数。如果气温在这之后都不会升高，请在该位置用 0 来代替。

>例如，给定一个列表 temperatures = [73, 74, 75, 71, 69, 72, 76, 73]，你的输出应该是 [1, 1, 4, 2, 1, 1, 0, 0]。

>提示：气温 列表长度的范围是 [1, 30000]。每个气温的值的均为华氏度，都是在 [30, 100] 范围内的整数。

### 粗暴解决


```python
def dailyTemperatures(self, T: List[int]) -> List[int]:
    index = 0
    length = len(T)
    res = [0] * length
    for current in T:
        for j in range(index + 1, length):
            if T[j] > current:
                res[index] = j - index
                break
        index = index + 1
    return res
```

<!--more-->

时间复杂度是 O(n^2)

### 单调栈

```
    /**
     * 维护一个单调递减栈
     * <p>
     * 当前元素和栈顶元素比较：
     * <p>
     * 1. 如果 current < top：则 push
     * 2. 如果 current > top：则 pop。并且 result[pop_index] = current_index - pop_index
     *
     * @param T
     * @return
     */
    public int[] dailyTemperatures(int[] T) {
        Stack<Integer> s = new Stack<>();
        int[] result = new int[T.length];
        for (int i = 0; i < T.length; i++) {
            while (!s.isEmpty() && T[s.peek()] < T[i]) {
                result[s.peek()] = i - s.peek();
                s.pop();
            }
            s.push(i);
        }
        return result;
    }
```

粗暴解决的方式是每次遍历的时候往后找最大的，会出现重复查找。

单调栈是找到最大的，往前回溯。避免重复






