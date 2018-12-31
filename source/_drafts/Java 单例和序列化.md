---
title: Java 单例和序列化
tags: Java
notebook: 
---


### 单例和序列化

https://www.hollischuang.com/archives/1144


序列化会破坏单例


```
/**
 * 双重检查
 */
public class Singleton5 implements Serializable {


    private static volatile Singleton5 singleton;


    private Singleton5() {

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


    private Object readResolve() {
        return singleton;
    }

}
```


```
/**
     * 测试序列化
     */
    public static void testSerializable() throws IOException, ClassNotFoundException {
        Singleton5 singleton5 = Singleton5.getInstance();

        ObjectOutputStream objectOutputStream = new ObjectOutputStream(new FileOutputStream("test"));
        objectOutputStream.writeObject(singleton5);

        ObjectInputStream objectInputStream = new ObjectInputStream(new FileInputStream("test"));
        Singleton5 newSingleton = (Singleton5) objectInputStream.readObject();

        Assert.assertEquals(singleton5, newSingleton);
    }
```

objectInputStream.readObject() => readObject0 =>  readOrdinaryObject => hasReadResolveMethod 


