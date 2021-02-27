---
title: java中的锁
date: 2019-01-20 21:14:16
tags: [java]
---

### lock接口

> 相对于 synchronized 的好处是：synchronized 不可中断。Lock 可以 等待一段时间或者能够相应中断；synchronized 多个同时读操作不能同时执行，Lock 可以通过 ReentrantReadWriteLock；synchronized 不能知道线程有没有成功获得锁，Lock 可以。

用来获取锁。如果锁已被其他线程获取，则进行等待。

> lock 是个接口


```
public interface Lock {
    void ();
    void lockInterruptibly() throws InterruptedException;
    boolean tryLock();
    boolean tryLock(long time, TimeUnit unit) throws InterruptedException;
    void unlock();
    Condition newCondition();
}
```

#### tryLock
它表示用来尝试获取锁，如果获取成功，则返回true，如果获取失败（即锁已被其他线程获取），则返回false。会立即返回。

#### tryLock(long time, TimeUnit unit)

类似 tryLock ，区别有个等待时间。

#### lockInterruptibly


通过 lockInterruptibly 获取锁，是可以被中断的（等待中的线程）。当两个线程同时通过lock.lockInterruptibly()想获取某个锁时，假若此时线程A获取到了锁，而线程B只有在等待，那么对线程B调用threadB.interrupt()方法能够中断线程B的等待过程。

#### unlock

释放锁

### ReentrantLock

可重入锁。可重入锁是指同一个线程可以多次获取同一把锁。ReentrantLock和synchronized都是可重入锁。

可中断锁。可中断锁是指线程尝试获取锁的过程中，是否可以响应中断。synchronized是不可中断锁，而ReentrantLock则提供了中断功能。

公平锁与非公平锁。公平锁是指多个线程同时尝试获取同一把锁时，获取锁的顺序按照线程达到的顺序，而非公平锁则允许线程“插队”。synchronized是非公平锁，而ReentrantLock的默认实现是非公平锁，但是也可以设置为公平锁。

CAS操作(CompareAndSwap)。CAS操作简单的说就是比较并交换。CAS 操作包含三个操作数 —— 内存位置（V）、预期原值（A）和新值(B)。

AQS => AbstractQueuedSynchronizer（抽象队列同步器）



### ReentrantReadWriteLock

[](https://www.cnblogs.com/zaizhoumo/p/7782941.html)

　　ReentrantReadWriteLock是Lock的另一种实现方式，我们已经知道了ReentrantLock是一个排他锁，同一时间只允许一个线程访问，而ReentrantReadWriteLock允许多个读线程同时访问，但不允许写线程和读线程、写线程和写线程同时访问。相对于排他锁，提高了并发性。在实际应用中，大部分情况下对共享数据（如缓存）的访问都是读操作远多于写操作，这时ReentrantReadWriteLock能够提供比排他锁更好的并发性和吞吐量。



### 队列同步器 AbstractQueuedSynchronizer

是用来构建锁或者其他同步组件的基础框架，它使用了一个 int 成员变量表示同步状态，通过内置的 FIFO 队列来完成资源获取线程的排队工作。


#### 接口和示例

- getState():获取当前同步状态
- setState(int newState)：设置当前同步状态
- compareAndSetState(int expect,int update):使用CAS获取当前状态

### 重入锁
todo
### 读写锁
todo
### Condition接口
todo