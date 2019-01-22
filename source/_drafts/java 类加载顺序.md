---
title: java 类加载顺序
tags: java
notebook: 
---

### java 类的加载顺序

类内部静态块 > 类静态属性 > 类内部属性 > 类构造函数


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
    
    static {
        System.out.println("Test Stataic");
    }
    
    public static Test testOut = new Test(1);
    
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
# 实例方法
TestStatic 1 Construct! 
# 实例方法
TestStatic 2 Construct!
# 子类的 属性
TestStatic 3 Construct! 
# 子类的静态代块
TestStaticInner Static!
```

