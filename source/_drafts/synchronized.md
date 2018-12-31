---
title: synchronized
tags: java
notebook: 
---



https://www.cnblogs.com/huansky/p/8869888.html

http://www.importnew.com/23511.html


> 普通同步方法：当前实例对象
> 静态同步方法：当前类的class对象。类锁，拦截所有线程，只有一个线程访问。
> 同步代码块：括号里的对象。如果是  this (表示当前实例对象)，也可以锁其他对象


### 同步方法和同步代码块


```
/**
 * Created by beer on 18-12-20.
 */
public class SynchronizeService {


    /**
     * 执行顺序：
     * <p>
     * 1、static synchronized method
     * 2、synchronized method
     * 5秒后
     * 3、synchronized code block
     * <p>
     * 同步方法和同步代码块用的是同一把锁
     *
     * @param args
     */
    public static void main(String[] args) {
        SynchronizeService synchronizeService = new SynchronizeService();
        new Thread(() -> {
            synchronizeService.test();
        }).start();

        new Thread(() -> {
            synchronizeService.test0();
        }).start();

        new Thread(() -> {
            synchronizeService.test1();
        }).start();

        try {
            Thread.sleep(100000000);
        } catch (
                InterruptedException e) {
            e.printStackTrace();
        }

    }


    /**
     * 同步方法(非静态同步方法)
     * <p>
     * 锁的对象：实例对象本身
     */
    public synchronized void test0() {
        System.out.println("synchronized method");
        try {
            Thread.sleep(5000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }


    /**
     * 同步方法（静态同步方法）
     * <p>
     * 锁的对象：类对象本身
     */
    public static synchronized void test() {
        System.out.println("static synchronized method");
        try {
            Thread.sleep(5000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }


    /**
     * 同步代码块
     */
    public void test1() {

        synchronized (this) {
            System.out.println("synchronized code block");
        }

        try {
            Thread.sleep(5000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```


**查看字节码**


同步方法：
```
public static synchronized void test();
    Code:
       0: getstatic     #15                 // Field java/lang/System.out:Ljava/io/PrintStream;
       3: ldc           #20                 // String static synchronized method
       5: invokevirtual #17                 // Method java/io/PrintStream.println:(Ljava/lang/String;)V
       8: ldc2_w        #18                 // long 5000l
      11: invokestatic  #12                 // Method java/lang/Thread.sleep:(J)V
      14: goto          22
      17: astore_0
      18: aload_0
      19: invokevirtual #14                 // Method java/lang/InterruptedException.printStackTrace:()V
      22: return
    Exception table:
       from    to  target type
           8    14    17   Class java/lang/InterruptedException
```


同步代码块

monitorenter
monitorexit
```
public void test1();
    Code:
       0: aload_0
       1: dup
       2: astore_1
       3: monitorenter
       4: getstatic     #15                 // Field java/lang/System.out:Ljava/io/PrintStream;
       7: ldc           #21                 // String synchronized code block
       9: invokevirtual #17                 // Method java/io/PrintStream.println:(Ljava/lang/String;)V
      12: aload_1
      13: monitorexit
      14: goto          22
      17: astore_2
      18: aload_1
      19: monitorexit
      20: aload_2
      21: athrow
      22: ldc2_w        #18                 // long 5000l
      25: invokestatic  #12                 // Method java/lang/Thread.sleep:(J)V
      28: goto          36
      31: astore_1
      32: aload_1
      33: invokevirtual #14                 // Method java/lang/InterruptedException.printStackTrace:()V
      36: return
    Exception table:
       from    to  target type
           4    14    17   any
          17    20    17   any
          22    28    31   Class java/lang/InterruptedException
}
```


```
Compiled from "SynchronizeService.java"
public class com.beer.interview.basic.SynchronizeService {
  public com.beer.interview.basic.SynchronizeService();
    Code:
       0: aload_0
       1: invokespecial #1                  // Method java/lang/Object."<init>":()V
       4: return

  public static void main(java.lang.String[]);
    Code:
       0: new           #2                  // class com/beer/interview/basic/SynchronizeService
       3: dup
       4: invokespecial #3                  // Method "<init>":()V
       7: astore_1
       8: new           #4                  // class java/lang/Thread
      11: dup
      12: aload_1
      13: invokedynamic #5,  0              // InvokeDynamic #0:run:(Lcom/beer/interview/basic/SynchronizeService;)Ljava/lang/Runnable;
      18: invokespecial #6                  // Method java/lang/Thread."<init>":(Ljava/lang/Runnable;)V
      21: invokevirtual #7                  // Method java/lang/Thread.start:()V
      24: new           #4                  // class java/lang/Thread
      27: dup
      28: aload_1
      29: invokedynamic #8,  0              // InvokeDynamic #1:run:(Lcom/beer/interview/basic/SynchronizeService;)Ljava/lang/Runnable;
      34: invokespecial #6                  // Method java/lang/Thread."<init>":(Ljava/lang/Runnable;)V
      37: invokevirtual #7                  // Method java/lang/Thread.start:()V
      40: new           #4                  // class java/lang/Thread
      43: dup
      44: aload_1
      45: invokedynamic #9,  0              // InvokeDynamic #2:run:(Lcom/beer/interview/basic/SynchronizeService;)Ljava/lang/Runnable;
      50: invokespecial #6                  // Method java/lang/Thread."<init>":(Ljava/lang/Runnable;)V
      53: invokevirtual #7                  // Method java/lang/Thread.start:()V
      56: ldc2_w        #10                 // long 100000000l
      59: invokestatic  #12                 // Method java/lang/Thread.sleep:(J)V
      62: goto          70
      65: astore_2
      66: aload_2
      67: invokevirtual #14                 // Method java/lang/InterruptedException.printStackTrace:()V
      70: return
    Exception table:
       from    to  target type
          56    62    65   Class java/lang/InterruptedException

  public synchronized void test0();
    Code:
       0: getstatic     #15                 // Field java/lang/System.out:Ljava/io/PrintStream;
       3: ldc           #16                 // String synchronized method
       5: invokevirtual #17                 // Method java/io/PrintStream.println:(Ljava/lang/String;)V
       8: ldc2_w        #18                 // long 5000l
      11: invokestatic  #12                 // Method java/lang/Thread.sleep:(J)V
      14: goto          22
      17: astore_1
      18: aload_1
      19: invokevirtual #14                 // Method java/lang/InterruptedException.printStackTrace:()V
      22: return
    Exception table:
       from    to  target type
           8    14    17   Class java/lang/InterruptedException

  public static synchronized void test();
    Code:
       0: getstatic     #15                 // Field java/lang/System.out:Ljava/io/PrintStream;
       3: ldc           #20                 // String static synchronized method
       5: invokevirtual #17                 // Method java/io/PrintStream.println:(Ljava/lang/String;)V
       8: ldc2_w        #18                 // long 5000l
      11: invokestatic  #12                 // Method java/lang/Thread.sleep:(J)V
      14: goto          22
      17: astore_0
      18: aload_0
      19: invokevirtual #14                 // Method java/lang/InterruptedException.printStackTrace:()V
      22: return
    Exception table:
       from    to  target type
           8    14    17   Class java/lang/InterruptedException

  public void test1();
    Code:
       0: aload_0
       1: dup
       2: astore_1
       3: monitorenter
       4: getstatic     #15                 // Field java/lang/System.out:Ljava/io/PrintStream;
       7: ldc           #21                 // String synchronized code block
       9: invokevirtual #17                 // Method java/io/PrintStream.println:(Ljava/lang/String;)V
      12: aload_1
      13: monitorexit
      14: goto          22
      17: astore_2
      18: aload_1
      19: monitorexit
      20: aload_2
      21: athrow
      22: ldc2_w        #18                 // long 5000l
      25: invokestatic  #12                 // Method java/lang/Thread.sleep:(J)V
      28: goto          36
      31: astore_1
      32: aload_1
      33: invokevirtual #14                 // Method java/lang/InterruptedException.printStackTrace:()V
      36: return
    Exception table:
       from    to  target type
           4    14    17   any
          17    20    17   any
          22    28    31   Class java/lang/InterruptedException
}
```