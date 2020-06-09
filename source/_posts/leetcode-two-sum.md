---
title: 两数之和
date: 2020-06-09 22:52:37
tags: [leetcode,hash]
---


https://leetcode-cn.com/problems/two-sum/


> 给定一个整数数组 nums 和一个目标值 target，请你在该数组中找出和为目标值的那 两个 整数，并返回他们的数组下标。

示例:

```json
给定 nums = [2, 7, 11, 15], target = 9

因为 nums[0] + nums[1] = 2 + 7 = 9
所以返回 [0, 1]
```

### 简单粗暴

两个 for 循环

```python
def twoSum(self, nums: List[int], target: int) -> List[int]:
    length = len(nums)
    for i in range(length):
        for j in range(length):
            if i != j:
                if (nums[i] + nums[j]) == target:
                    return [i, j]
```

显而易见，超时，而且时间复杂度高。

### hash

一次 for 循环搞定。

```python
def twoSum(self, nums: List[int], target: int) -> List[int]:
    length = len(nums)
    map = {}
    for i in range(length):
        if nums[i] in map:
            return [map.get(nums[i]), i]
        else:
            map[target - nums[i]] = i
```