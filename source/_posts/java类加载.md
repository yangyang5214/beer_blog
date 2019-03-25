---
title: java类加载
date: 2019-02-20 09:47:44
tags: java
---

开发中的 ClassNotFoundExcetpion 。

### 类加载过程

- 加载
```
通过类的全名，来查找并加载类的二进制数据
```

[java类加载器](https://www.hexianwei.com/2019/02/20/java%E7%B1%BB%E5%8A%A0%E8%BD%BD%E5%99%A8/)

<!--more-->

- 链接
```
验证：确保加载类的正确性
准备：为类的静态变量分配内存，并将其初始化为默认值
解析：将类中的符号引用转为直接引用
```
- 初始化
```
这些情况下会被初始化
1、new
2、访问某个类的静态变量，或者对该静态变量赋值
3、调用类的静态方法
4、反射
5、初始化一个类的子类，会首先初始化这个类的父类
6、JVM启动时，标明启动类

```
- 使用
- 卸载

### java 类的加载顺序

（静态代码块，静态属性）> （属性，代码块） > 构造函数

静态代码块 和 静态属性 是按照写的顺序，顺序加载的，谁写在前面先加载谁。

如果有内部类:

内部类第一次被使用的时候加载。按照顺序进行加载。

```
public class Test {
    
    public static class Inner{
        
        public final static Test testInstance = new Test(3);
        
        static {
            System.out.println("TestInner Static!");
        }
    }
    
    public static Test getInstance(){
        return Inner.testInstance;
    }
    
    public Test(int i ) {
        System.out.println("Test " + i +" Construct! ");
    }
    
    public static Test testOut = new Test(1);

    static {
        System.out.println("Test Stataic");
    }
    
    public static void main(String args[]){
        Test t = new Test(2);
        Test.getInstance();
    }

}
```

```
执行顺序：
# 静态代码块
TestStatic Stataic
# 静态属性
TestStatic 1 Construct! 
# 实例方法
TestStatic 2 Construct!
# 内部类的静态属性
TestStatic 3 Construct! 
# 内部类的静态代码块
TestStaticInner Static!
```

