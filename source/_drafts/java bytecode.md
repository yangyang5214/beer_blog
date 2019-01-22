---
title: bytecode
tags: java
notebook: 
---

> [bytecode](https://www.ibm.com/developerworks/library/it-haggar_bytecode/index.html), [bytecode instruction listings](https://en.wikipedia.org/wiki/Java_bytecode_instruction_listings) 建议直接看原文

> Understanding bytecode makes you a better programmer.
### why understand bytecode

字节码是代码大小和执行速度的重要组成部分，考虑到你拥有的字节码越多，.class文件就越大，JIT或Hotspot运行时必须编译的代码就越多。

### generating bytecode

```
# 编译
javac Employee .java
# 反编译
javap -c Employee .class
```

```
     1	Compiled from Employee.java
     2	class Employee extends java.lang.Object {
     3	public Employee(java.lang.String,int);
     4	public java.lang.String employeeName();
     5	public int employeeNumber();
     6	}
     7	 
     8	Method Employee(java.lang.String,int)
     9	0 aload_0
    10	1 invokespecial #3 <Method java.lang.Object()>
    11	4 aload_0
    12	5 aload_1
    13	6 putfield #5 <Field java.lang.String name>
    14	9 aload_0
    15	10 iload_2
    16	11 putfield #4 <Field int idNumber>
    17	14 aload_0
    18	15 aload_1
    19	16 iload_2
    20	17 invokespecial #6 <Method void storeData(java.lang.String, int)>
    21	20 return
    22	 
    23	Method java.lang.String employeeName()
    24	0 aload_0
    25	1 getfield #5 <Field java.lang.String name>
    26	4 areturn
    27	 
    28	Method int employeeNumber()
    29	0 aload_0
    30	1 getfield #4 <Field int idNumber>
    31	4 ireturn
    32	 
    33	Method void storeData(java.lang.String, int)
    34	0 return
```

两个变量（String,int）, 一个构造方法 （public Employee(java.lang.String,int);），三个方法（storeData，employeeNumber，employeeName）。The first five lines （前5行），类的定义，构造器等。



aload_0，iload_2等。前缀（prefix）a 表示对象，i 表示 int . c => char , d => double 等。

###  the details 

To understand the details of the bytecode, we need to discuss how a Java Virtual Machine (JVM) works regarding the execution of the bytecode. A JVM is a stack-based machine. Each thread has a JVM stack which stores frames. A frame is created each time a method is invoked and consists of an operand stack, an array of local variables, and a reference to the runtime constant pool of the class of the current method. Conceptually, it might look like this:

- operand stack 
```
# LIFO 堆栈。大小在编译器确定。操作数栈也用于从方法接收返回值
The operand stack is a LIFO stack used to push and pop values. Its size is also determined at compile time. Certain opcode instructions push values onto the operand stack; others take operands from the stack, manipulate them, and push the result. The operand stack is also used to receive return values from methods.
```

- an array of local variables
also called the local variable  table (局部变量表).包含方法的参数 and 局部变量的值。

```
# 局部变量的大小在编译时期确定，并且取决于 局部变量 and 形式方法的参数 的数量和大小、
The size of the array of local variables is determined at compile time and is dependent on the number and size of local variables and formal method parameters. 
```
- constant pool

![](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/blog/jvm.jpg
)

```
     1	public String employeeName()
     2	{
     3	return name;
     4	}
     5	 
     6	Method java.lang.String employeeName()
     7	0 aload_0
     8	1 getfield  #5 <Field java.lang.String name>
     9	4 areturn
```

这个方法的字节码包含三个操作码。

- aload_0 

```
# 将值从局部变量表的索引0推送到操作数堆栈（引用）
pushes the value from index 0 of the local variable table onto the operand stack
```
- getfield 5 Field java.lang.String name

[getfield  **fetch field from object**](https://docs.oracle.com/javase/specs/jvms/se6/html/Instructions2.doc5.html)

```
# 从对象中获取字段。弹出堆栈的最高值。5 是 常量池的索引（name ），获取此引用时候，被push 到操作数堆栈。
The next opcode instruction, getfield, is used to fetch a field from an object. When this opcode is executed, the top value from the stack, this, is popped. Then the #5 is used to build an index into the runtime constant pool of the class where the reference to name is stored. When this reference is fetched, it is pushed onto the operand stack.
```
-  areturn

```
# 返回方法的引用。更具体地说，执行转换会导致操作数堆栈的最高值（对name的引用）被弹出并推送到调用方法的操作数堆栈上。
The last instruction, areturn, returns a reference from a method. More specifically, the execution of areturn causes the top value on the operand stack, the reference to name, to be popped and pushed onto the operand stack of the calling method.
```



The employeeName method is fairly simple. Before looking at a more complex example, we need to examine the values to the left of each opcode. In the employeeName method's bytecode, these values are 0, 1, and 4. Each method has a corresponding bytecode array. These values correspond to the index into the array where each opcode and its arguments are stored. You might wonder why the values are not sequential. Since bytecode got its name because each instruction occupies one byte, why are the indexes not 0, 1, and 2? The reason is some of the opcodes have parameters that take up space in the bytecode array. For example, the aload_0 instruction has no parameters and naturally occupies one byte in the bytecode array. Therefore, the next opcode, getfield, is in location 1. However, areturn is in location 4. This is because the getfield opcode and its parameters occupy location 1, 2, and 3. Location 1 is used for the getfield opcode, location 2 and 3 are used to hold its parameters. These parameters are used to construct an index into the runtime constant pool for the class to where the value is stored. The following diagram shows what the bytecode array looks like for the employeeName method:


为什么 aload => 0 , getfield => 1 ,why areturn => 4 ,而不是 2 ？

This is because the getfield opcode and its parameters occupy location 1, 2, and 3. Location 1 is used for the getfield opcode, location 2 and 3 are used to hold its parameters. These parameters are used to construct an index into the runtime constant pool for the class to where the value is stored.

因为： getfield 操作码位置是 1 ， 两个参数是 2 and 3 。参数的作用是：被用来构造运行时常量池存储的索引。

```
public Employee(String strName, int num){
     name = strName;
     idNumber = num;
     storeData(strName, num);
}
 
Method Employee(java.lang.String,int)
0 aload_0
1 invokespecial #3 <Method java.lang.Object()>
4 aload_0
5 aload_1
6 putfield #5 <Field java.lang.String name>
9 aload_0
10 iload_2
11 putfield #4 <Field int idNumber>
14 aload_0
15 aload_1
16 iload_2
17 invokespecial #6 <Method void storeData(java.lang.String, int)>
20 return
```

- location 0
aload_0 将此引用push 到 operand stack(此引用：应该指的是该方法). 实例方法和构造函数的局部变量表的第一个条目是该引用。


- location 1
位置1的下一个操作码指令invokespecial调用该类的超类的构造函数。 因为所有未显式扩展任何其他类的类都隐式地从java.lang.Object继承，所以编译器提供必要的字节码来调用此基类构造函数。 在此操作码期间，弹出操作数堆栈的最高值。


- location 2 3 

invokespecial 的两个参数

- location 4 5 

将前两个条目从局部变量表推送到操作数堆栈。 要推送的第一个值是此引用。 第二个值是构造函数strName的第一个形式参数。 推送这些值以准备位置6处的putfield操作码指令。

- location 6 

putfield 从堆栈中弹出两个顶部值，并将对strName的引用存储到由此引用的对象的实例数据名中。

- location 7 8 

putfield 的两个参数

- location 9 10 11 

9,10和11处的下三个操作码指令对构造函数num和实例变量idNumber执行与第二个形式参数相同的操作。


### Size and speed issues

对于桌面应用服务器以及嵌入式，大小和运行速率都是关键问题。下面两种实现同步的方式。

```
public synchronized int top1(){
  return intArr[0];
}

public int top2(){
 synchronized (this) {
  return intArr[0];
 }
}
```

明显   top 2 的 字节码 比 top1多 

```
Compiled from "TestSynchronized.java"
public class com.yangyang.leetcode.algarithm.TestSynchronized {
  public int[] intArr;

  public com.yangyang.leetcode.algarithm.TestSynchronized();
    Code:
       0: aload_0
       1: invokespecial #1                  // Method java/lang/Object."<init>":()V
       4: aload_0
       5: iconst_3
       6: newarray       int
       8: dup
       9: iconst_0
      10: iconst_1
      11: iastore
      12: dup
      13: iconst_1
      14: iconst_2
      15: iastore
      16: dup
      17: iconst_2
      18: iconst_3
      19: iastore
      20: putfield      #2                  // Field intArr:[I
      23: return

  public synchronized int top1();
    Code:
       0: aload_0
       1: getfield      #2                  // Field intArr:[I
       4: iconst_0
       5: iaload
       6: ireturn

  public int top2();
    Code:
       0: aload_0
       1: dup
       2: astore_1
       3: monitorenter
       4: aload_0
       5: getfield      #2                  // Field intArr:[I
       8: iconst_0
       9: iaload
      10: aload_1
      11: monitorexit
      12: ireturn
      13: astore_2
      14: aload_1
      15: monitorexit
      16: aload_2
      17: athrow
    Exception table:
       from    to  target type
           4    12    13   any
          13    16    13   any
}
```