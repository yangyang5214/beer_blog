---
title: java 序列化
tags: java
notebook: 
---


> [Java 序列化的高级认识](https://www.ibm.com/developerworks/cn/java/j-lo-serial/index.html)，[深入分析Java的序列化与反序列化](https://www.hollischuang.com/archives/1140)


### 序列化

将对象的状态信息转换为可以存储或传输的形式的过程


被序列化的类需要实现 serializable 接口，使用 ObjectInputStream and ObjectOutputStream 方法进行对象的读写。

### 序列化ID问题

序列化ID不一致，导致反序列化失败。

运行时判断类的 serialVersionUID 来验证版本一致性的

idea 通过插件直接生成


### 静态变量问题

序列化的是对象的状态。静态变量属于类状态。因此，序列化，并不保存静态变量

### 父类序列化和 transient 关键字

父类如果没有实现 serializable 接口，则不会被序列化。反序列时候，会默认调用父类的无参构造函数作为默认的对象。


transient 控制变量的序列化。可以阻止该变量被序列化到文件中，在被反序列化后，transient 变量的值会被设置为默认值。int 为0 ， 对象型是 null.

### 敏感字段加密

对象类的 writeObject and readObject . 用户自定义序列化和反序列化。

或者也可以对 transient 关键字的赋默认值

```
 private void writeObject(ObjectOutputStream oos) {
        ObjectOutputStream.PutField putField = null;
        try {
            putField = oos.putFields();

            //模拟加密，后面随便加几位
            pwd = pwd + "0000";

            System.out.println("加密后的字符串" + pwd);
            putField.put("pwd", this.pwd);

            oos.writeFields();

        } catch (IOException e) {
            e.printStackTrace();
        }

    }

    private void readObject(ObjectInputStream ois) {
        try {
            ObjectInputStream.GetField getField = ois.readFields();
            Object object = getField.get("pwd", "");

            //模拟解密,取前十位
            pwd = object.toString().substring(0, 10);
            System.out.println("解密后的字符串" + pwd);
        } catch (IOException e) {
            e.printStackTrace();
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        }

    }
```
### ArrayList 的序列化

ArrayList 底层是一个数组。

由  elementData 数组存储

实现了   Serializable 接口，可以被序列化。

但是 elementData 被关键字  transient 修饰  ,不会被序列化。

所有查看  readObject and  writeObject 方法。
```
public class ArrayList<E> extends AbstractList<E>
        implements List<E>, RandomAccess, Cloneable, java.io.Serializable{

    private static final long serialVersionUID = 8683452581122892189L;

    transient Object[] elementData; // non-private to simplify nested class access
```


writeObject 方法

将大小和容量写入   ObjectOutputStream 
```
 /**
     * Save the state of the <tt>ArrayList</tt> instance to a stream (that
     * is, serialize it).
     *
     * @serialData The length of the array backing the <tt>ArrayList</tt>
     *             instance is emitted (int), followed by all of its elements
     *             (each an <tt>Object</tt>) in the proper order.
     */
    private void writeObject(java.io.ObjectOutputStream s)
        throws java.io.IOException{
        // Write out element count, and any hidden stuff
        int expectedModCount = modCount;
        s.defaultWriteObject();

        // Write out size as capacity for behavioural compatibility with clone()
        s.writeInt(size);

        // Write out all elements in the proper order.
        for (int i=0; i<size; i++) {
            s.writeObject(elementData[i]);
        }

        if (modCount != expectedModCount) {
            throw new ConcurrentModificationException();
        }
    }
```


readObject 方法

从 ObjectInputStream  读取元素

```
/**
     * Reconstitute the <tt>ArrayList</tt> instance from a stream (that is,
     * deserialize it).
     */
    private void readObject(java.io.ObjectInputStream s)
        throws java.io.IOException, ClassNotFoundException {
        elementData = EMPTY_ELEMENTDATA;

        // Read in size, and any hidden stuff
        s.defaultReadObject();

        // Read in capacity
        s.readInt(); // ignored

        if (size > 0) {
            // be like clone(), allocate array based upon size not capacity
            int capacity = calculateCapacity(elementData, size);
            SharedSecrets.getJavaOISAccess().checkArray(s, Object[].class, capacity);
            ensureCapacityInternal(size);

            Object[] a = elementData;
            // Read in all elements in the proper order.
            for (int i=0; i<size; i++) {
                a[i] = s.readObject();
            }
        }
    }
```
ArrayList 是动态数组，先初始化容量，然后存放元素。可能有的位置的元素是null, 所以这样做避免浪费。