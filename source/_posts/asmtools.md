---
title: asmtools
date: 2021-03-05 22:36:03
tags: [java, jvm]
---

[asmtools](https://wiki.openjdk.java.net/display/CodeTools/asmtools)

### How to build AsmTools

https://wiki.openjdk.java.net/display/CodeTools/How+to+build+AsmTools

#### 准备

- ant

```
[23:04] beer $ ant -version
Apache Ant(TM) version 1.10.9 compiled on September 27 2020
```

- java

```
[22:35] beer $ java -version
openjdk version "15.0.2" 2021-01-19
OpenJDK Runtime Environment (build 15.0.2+7-27)
OpenJDK 64-Bit Server VM (build 15.0.2+7-27, mixed mode, sharing)
```

#### build

```
hg clone http://hg.openjdk.java.net/code-tools/asmtools
cd asmtools/build
ant

# 有打印日志找到文件
[23:08] beer $ pwd
/Users/beer/code/asmtools-7.0-build/binaries/lib
```

<!--more-->

### 用法

一个例子（例子来源：懂的人都懂）

```
[08:22] beer $ cat Foo.java
public class Foo {
    public static void main(String[] args) {
        boolean flag = true;
        if (flag) System.out.println("Hello, Java!");
        if (flag == true) System.out.println("Hello, JVM!");
    }
}
```

- javac Foo.java

idea 查看
```java
//
// Source code recreated from a .class file by IntelliJ IDEA
// (powered by FernFlower decompiler)
//

public class Foo {
    public Foo() {
    }

    public static void main(String[] var0) {
        boolean var1 = true;
        if (var1) {
            System.out.println("Hello, Java!");
        }

        if (var1) {
            System.out.println("Hello, JVM!");
        }

    }
}
```

- jdis

```
[08:28] beer $ java -jar asmtools.jar jdis Foo.class 

super public class Foo
	version 59:0
{
  public Method "<init>":"()V"
	stack 1 locals 1
  {
		aload_0;
		invokespecial	Method java/lang/Object."<init>":"()V";
		return;
  }
  public static Method main:"([Ljava/lang/String;)V"
	stack 2 locals 2
  {
		iconst_1;
		istore_1;
		iload_1;
		ifeq	L14;
		getstatic	Field java/lang/System.out:"Ljava/io/PrintStream;";
		ldc	String "Hello, Java!";
		invokevirtual	Method java/io/PrintStream.println:"(Ljava/lang/String;)V";
	L14:	stack_frame_type append;
		locals_map int;
		iload_1;
		iconst_1;
		if_icmpne	L27;
		getstatic	Field java/lang/System.out:"Ljava/io/PrintStream;";
		ldc	String "Hello, JVM!";
		invokevirtual	Method java/io/PrintStream.println:"(Ljava/lang/String;)V";
	L27:	stack_frame_type same;
		return;
  }

} // end Class Foo
```

```
java -jar asmtools.jar jdis Foo.class  > Foo.jasm
```

- 修改 *boolean flag = true* => *boolean flag = 3*

```
iconst_1; => iconst_3;
```

- 生成 class

```
java -jar asmtools.jar jasm Foo.jasm
```

- 运行

```
[08:32] beer $ java Foo 
Hello, Java!
```

再看一眼 class 文件

```
//
// Source code recreated from a .class file by IntelliJ IDEA
// (powered by FernFlower decompiler)
//

public class Foo {
    public Foo() {
    }

    public static void main(String[] var0) {
        byte var1 = 3;
        if (var1 != 0) {
            System.out.println("Hello, Java!");
        }

        if (var1 == 1) {
            System.out.println("Hello, JVM!");
        }

    }
}
```

已经不是 int 类型了 int => byte






