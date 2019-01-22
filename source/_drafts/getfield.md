---
title: getfield
tags: java,bytecode
notebook: 
---


> [getfield](https://docs.oracle.com/javase/specs/jvms/se6/html/Instructions2.doc5.html)



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

起因是看到操作码， 为什么 aload => 0 , getfield => 1 ,why areturn => 4 ,而不是 2 ？

```
# [出处]https://www.ibm.com/developerworks/library/it-haggar_bytecode/index.html
This is because the getfield opcode and its parameters occupy location 1, 2, and 3. Location 1 is used for the getfield opcode, location 2 and 3 are used to hold its parameters. These parameters are used to construct an index into the runtime constant pool for the class to where the value is stored.
```

### Operation


Fetch field from object

```
# https://en.wikipedia.org/wiki/Java_bytecode_instruction_listings
# 获取字段的值。这个字段在常量池中。
get a field value of an object objectref, where the field is identified by field reference in the constant pool index (indexbyte1 << 8 + indexbyte2)
```


### Operand Stack

objectref → value	

### Description

```
# 两个参数的作用是：被用来构造运行时常量池存储的索引。
The objectref, which must be of type reference, is popped from the operand stack. The unsigned indexbyte1 and indexbyte2 are used to construct an index into the runtime constant pool of the current class (§3.6), where the value of the index is (indexbyte1 << 8) | indexbyte2. The runtime constant pool item at that index must be a symbolic reference to a field (§5.1), which gives the name and descriptor of the field as well as a symbolic reference to the class in which the field is to be found. The referenced field is resolved (§5.4.3.2). The value of the referenced field in objectref is fetched and pushed onto the operand stack.
```

### Runtime Exception

```
# 如果 为null , 则抛出 空指针异常
Otherwise, if objectref is null, the getfield instruction throws a NullPointerException.
```

### Notes
```
# getfield 不能被用来访问数组，而是 arraylength 指令
The getfield instruction cannot be used to access the length field of an array. The arraylength instruction is used instead.
```
