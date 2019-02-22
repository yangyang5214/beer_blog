---
title: java面试题整理
date: 2018-08-08 00:00:00
tags:
---
### List 和 set 区别

list 
- ArrayList
- LinkedList
- Vector

ArrayList 线程不安全，Vector线程安全

```
{
        Vector vector = new Vector();
        List<Thread> threadList = new ArrayList<>();
        List<String> list = new ArrayList<>(100000);
        for (int i = 0; i < 100 ; i++) {
           Thread thread = new Thread(() -> {
               for (int j = 0; j < 10000 ; j++) {
                   list.add("");
                   vector.add("");
               }
           });
            thread.start();
            threadList.add(thread);
        }

        for (Thread thread : threadList) {
            thread.join();
        }

        System.out.println("arrayList size=" +list.size());
        System.out.println("vector size=" + vector.size());
    }
```

set
- HashSet

### HashSet如何保证不重复

底层是 hashMap , HashMap 的key 不能重复。

```
if (p.hash == hash && ((k = p.key) == key || (key != null && key.equals(k))))
```
###  HashMap 是线程安全的吗，为什么不是线程安全的（最好画图说明多线程环境下不安全）?

最简单的列子：HashMap 要扩容，可能会出现 outofIndex

### HashMap 的扩容过程

DEFAULT_LOAD_FACTOR = 0.75//装载因子

DEFAULT_INITIAL_CAPACITY = 16//初始化大小

MAXIMUM_CAPACITY = 1 << 30;//最大大小

TREEIFY_THRESHOLD = 8;//链表转为树的阈值

UNTREEIFY_THRESHOLD = 6; //resize 之后树的大小为6时，将树转为链表

扩容是二倍扩容

newCap = oldCap << 1

### Java反射 

在运行状态中，对于任意一个类，都能够知道这个类的所有属性和方法；对于任意一个对象，都能够调用它的任意一个方法和属性；这种动态获取的信息以及动态调用对象的方法的功能称为java语言的反射机制。

- CLass.forName
- getClass()
- .class

### 强引用 、软引用、 弱引用、 虚引用

- new , 垃圾回收期不会回收
- SoftReference 内存溢出之前进行回收
- 弱引用。第二次垃圾回收时候回收
- 虚引用。每次垃圾回收时候都会被回收

### synchronized 的实现原理以及锁优化？

synchronized  基于操作系统的 Lock 
### volatile 

读写的内存语义

### Java 的信号灯

todo

### synchronized 在静态方法和普通方法的区别？

当前类  和  当前实例对象

### 怎么实现所有线程在等待某个事件的发生才会去执行？


[CountDownLatch 和 join](https://blog.csdn.net/zhutulang/article/details/48504487
)

- CountDownLatch
设置初始值为1,所有线程调用 awit 方法等待。当事件发生后调用 countDown ，则所有线程得以继续执行

- BlockingQueue

所有队列从空的阻塞队列中获取值，当事件完成后，插入与阻塞线程相等的值，进入阻塞队列
- Semaphore

### CAS？CAS 有什么缺陷，如何解决？

- 开销大
- 不能保证代码块的原子性
- ABA问题

　　现有一个用单向链表实现的堆栈，栈顶为A，这时线程T1已经知道A.next为B，然后希望用CAS将栈顶替换为B：head.compareAndSet(A,B);在T1执行上面这条指令之前，线程T2介入，将A、B出栈，再pushD、C、A。而对象B此时处于游离状态：此时轮到线程T1执行CAS操作，检测发现栈顶仍为A，所以CAS成功，栈顶变为B，但实际上B.next为null，其中堆栈中只有B一个元素，C和D组成的链表不再存在于堆栈中，平白无故就把C、D丢掉了。

解决：对对象标记版本时间戳

### synchronized 和 lock 有什么区别

lock 可释放
### Hashtable 是怎么加锁的 ？

synchronized 

### HashMap 的并发问题？

rehash   resize 时候环形链表的出现

https://blog.csdn.net/bjwfm2011/article/details/81076736 


### ConcurrenHashMap 介绍？1.8 中为什么要用红黑树？
ConcurrenHashMap 加锁，在每个 Segment 上面加锁


resize ：Segment resize 
get：不加锁（volatile变量）
put: 加锁 

### AQS

### 如何检测死锁？怎么预防死锁？

- 互斥
- 请求和保持
- 不可剥夺  
- 环路等待


### Java 内存模型？

多线程之间的通信和同步
### 如何保证多线程下 i++ 结果正确？

- AtomicInteger(CAS + volatile)

- synchronized 

- lock

### 线程池的种类，区别和使用场景？

直接使用  ThreadPoolExecutor 创建。

### 分析线程池的实现原理和线程的调度过程？

- 线程复用
- 控制最大并发数
- 管理线程

### 线程池如何调优，最大数目如何确认？

### ThreadLocal  原理，用的时候需要注意什么？ 


ThreadLocalMap<ThreadLocal,Object>

### BeanFactory 和 FactoryBean？

- BeanFactory.是一个工厂类（接口）。负责生产和管理bean。 ApplicationnContext 
- FactoryBean 是一个bean, 不是一个简单的 bean, 而是一个能生产或者修饰对象生成的工厂 Bean, 它的实现与设计模式中的工厂模式和修饰器模式类似。在实际应用中若能灵活使用FactoryBean，则可以给应用程序增加很多的魔幻功能。

### Spring IOC 的理解，其初始化过程？

控制翻转。正常是 new ，然后获取。现在是 直接获取。初始化的控制权交给了 spring 容器。

初始化过程：BeanFactory 的实现，比如：ApplicationnContext 。创建了 IOC 容器。

### Spring Bean 的生命周期，如何被管理的？

单例
```
启动：容器启动的时候就初始化
使用：getBean ,返回的是 同一个bean
销毁：容器关闭销毁
```
多例
```
启动：getBean调用时候
使用：getBean ,返回的是新的bean
销毁：容器关闭销毁
```

### 如果要你实现Spring AOP，请问怎么实现？

代理模式

### Spring 是如何管理事务的，事务管理机制？

- 声明式事务
```
@Transaction
最小颗粒为方法。如果需要，则将需要时事务管理的代码块提取为方法
```
- 编程式事务
```
TransactionTemplate
```

#### 声明式事务 
- PlatformTransactionManager
```
getTransaction()//获取事务状态
commit()//提交事务
rollback()//回滚事务
```
- TransactionDefinition
```
事务定义信息
```
- TransactionStatus
```
事务状态
```
流程：
1. 配置事务管理器进行事务管理
2. 事务定义信息、事务管理方案。通过 TransactionManager 进行管理
3. 最事务运行过程中，每个时刻都获取 事务状态，来了解事务的运行状态。
### Spring 的不同事务传播行为有哪些，干什么用的？
todo
### Spring 中用到了那些设计模式？ 
- 工厂模式
- 模板模式
- 代理模式
- 单例模式
- 策略模式
- 原型模式
- 迭代器模式
## JVM

https://www.jianshu.com/p/c9ac99b87d56

### JVM 内存模型
- 堆内存
```
对象的实例
```
- 方法区
```
类信息，常量和静态变量。是各个线程共享的内存区域
```
- 栈内存
```
线程私有
```
### 内存溢出，内存泄漏

- 内存泄漏。当一个对象已经不需要再使用，本该被回收的时候，另外一个正在使用的对象持有它的引用从而导致它不能被回收
- 内存溢出。没有足够的内存。

### 说说Java线程栈

