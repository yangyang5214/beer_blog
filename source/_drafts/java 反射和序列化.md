---
title: java 单例和反射
tags: java
notebook: 
---

#### java 单例和反射

反射会破坏序列化


```
 /**
     * 测试反射
     */
    public static void testReflection() throws ClassNotFoundException, IllegalAccessException, InstantiationException, NoSuchMethodException, InvocationTargetException {
        Singleton5 singleton5 = Singleton5.getInstance();

        Constructor constructor = Singleton5.class.getDeclaredConstructor();
        //为 true, 禁止java语言进行访问检查 
        constructor.setAccessible(true);
        Singleton5 newsingleton = (Singleton5) constructor.newInstance();
        Assert.assertEquals(singleton5, newsingleton);
    }
```

```
/**
 * 双重检查
 */
public class Singleton5 implements Serializable {


    private static volatile Singleton5 singleton;


    //避免反射破坏单例
    private Singleton5() {
        if (singleton != null){
            throw new RuntimeException("此类已经被创建过！");
        }
    }


    public static Singleton5 getInstance() {
        if (singleton == null) {
            synchronized (Singleton5.class) {
                if (singleton == null) {
                    singleton = new Singleton5();
                }
            }
        }
        return singleton;
    }


    //避免序列化破坏单例
    private Object readResolve() {
        return singleton;
    }

}
```



###  setAccessible(true)

setAccessible(true) 破坏了java语言的封装？不安全？


```
public void setAccessible(boolean flag) throws SecurityException {
        SecurityManager sm = System.getSecurityManager();
        if (sm != null) sm.checkPermission(ACCESS_PERMISSION);
        setAccessible0(this, flag);
}
```
 

 