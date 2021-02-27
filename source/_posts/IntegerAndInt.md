---
title: Integer And int
date: 2019-07-03 17:29:13
tags: 【java】
---

> An interesting bug for Integer with int.


### 重现
部分代码：

revenueList.remove(i);


```
for (Integer i = 0; i < revenueList.size(); i++) {
    JSONObject other = revenueList.get(i);
    if ("其他".equals(other.getString("name_cn"))) {
        revenueList.remove(i);
        //revenueList.remove(other);
        revenueList.add(other);
        break;
    }
}
```
本意是想移除第 i 个元素。但是，事实并没有。需要使用： revenueList.remove(other) 。

### 解决

其实，改这个 bug 的时候也是懵逼的 ，为什么 remove(i) 不行。


今天有空，回头看看。发现 fori 循环，用的是 Integer、Integer、Integer！！！amazing!!! 这就导致，remove 方法调用的是 remove(Object o); 而不是： remove(int index); 


最好的解决方法是   fori 循环 不要用 Integer ,不要用 Integer ，不要用 Integer ！！！
