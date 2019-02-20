---
title: java 类加载器
date: 2019-02-20 10:44:37
tags:
---


> [深入探讨 Java 类加载器](https://www.ibm.com/developerworks/cn/java/j-lo-classloader/index.html)

> JVM 将类加载到内存中，通过 classloader 的loadClass() 方法来加载  class .程序并不会一次性加载所有的class文件，而是根据需要，通过classLoader 来动态加载。

### 类加载器

![](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/blog/classloadClass.png
)

<!--more-->

- 引导类加载器
```
bootstrap class loader :加载 java 的核心库。
```
- 扩展类加载器
```
父类为：BootStrapClassLoader
extension class loader : 用来加载java的扩展库。java虚拟机的实现会提供一个扩展目录。该类加载器在此目录里面查找并加载java类
```
- 系统类加载器

system(App) class loader : 根据java 应用的类路径（classpath）来加载java类

```
父类为  ExtClassLoader
```
- 自定义加载器

```
父类为 AppClassLoader
```

ClassLoader loader = ClassLoaderService.class.getClassLoader();
System.out.println(loader);
System.out.println(loader.getParent());
System.out.println(loader.getParent().getParent());

# 输出结果
sun.misc.Launcher$AppClassLoader@18b4aac2
sun.misc.Launcher$ExtClassLoader@776ec8df
null
```

### 类加载器的代理模式

todo 

### 双亲委托

[加载类过程](https://img-blog.csdn.net/20161116230421561)
