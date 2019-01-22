---
title: java是纯面向对象的语言
tags: java
notebook: 
---


### java 是纯面向对象的语言吗？

https://www.geeksforgeeks.org/java-not-purely-object-oriented-language/

**面向对象语言特征**
```
1、封装
2、继承
3、多态
4、抽象
5、所有预定义的类型都是对象
6、所有用户定义的类型都是对象
7、在对象上执行的所有操作必须通过在对象上公开的方法
```

Java 满足 1 2 3 4 6

Java 不是纯 OO 的原因
- 原始数据类型（byte char short int long double float boolen）
- The static keyword. 
- wrapper class. 我们不调用对象的公开的方法（eg：算数运算符）
