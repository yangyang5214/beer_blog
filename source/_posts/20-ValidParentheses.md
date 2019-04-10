---
title: 20.ValidParentheses
date: 2019-04-10 23:39:32
tags: leetcode
---

> https://leetcode.com/problems/valid-parentheses/


### 题目

Given a string containing just the characters '(', ')', '{', '}', '[' and ']', determine if the input string is valid.

An input string is valid if:


Open brackets must be closed by the same type of brackets.
Open brackets must be closed in the correct order.
Note that an empty string is also considered valid.


<!--more-->

**Example 1:**

Input: "()"

Output: true

**Example 2:**

Input: "()[]{}"

Output: true

**Example 3:**


Input: "(]"

Output: false

**Example 4:**

Input: "([)]"

Output: false

**Example 5:**

Input: "{[]}"

Output: true


### 分析

注意 case 4 就好了。


{} and [] and () 都是成对出现的，且不允许有交叉。所以使用 栈 来实现。一开始，如果是左，则存储。碰到右 去栈顶找 左（pop）。


### 解答

```
public static boolean isValid(String s) {
        char[] chars = s.toCharArray();
        Stack stack = new Stack();
        for (char aChar : chars) {
            switch (aChar){
                case '{':
                case '(':
                case '[':
                    stack.push(aChar);
                    break;
                case ')':
                    if (stack.empty() || !stack.peek().equals('(')){
                        return false;
                    }else{
                        stack.pop();
                    }
                    break;
                case ']':
                    if (stack.empty() || !stack.peek().equals('[')){
                        return false;
                    }else{
                        stack.pop();
                    }
                    break;
                case '}':
                    if (stack.empty() || !stack.peek().equals('{')){
                        return false;
                    }else{
                        stack.pop();
                    }
                    break;
            }
        }
        return stack.isEmpty();
    }
```