---
title: integer
tags: java
notebook: 
---

### integer 的缓存范围

https://blog.csdn.net/BeauXie/article/details/53013946



```
    public static void main(String[] args) {
        Integer a = 127;
        Integer b = 127;

        //true
        System.out.println(a == b);


        Integer c = 128;
        Integer d = 128;


        //false
        System.out.println(c == d);


        Integer e = new Integer(2);
        Integer f = new Integer(2);


        //false
        System.out.println(e == f);


        Integer g = a;
        Integer h = new Integer(127);

        //false
        System.out.println(g == h);

        //true
        System.out.println(127 == h);

    }
```


### 自动装箱、自动拆箱
```
 public static void main(String[] args) {
        Integer dst = new Integer(65536);
        int src = 65536;

        //true
        System.out.println(src == dst);

        //true
        System.out.println(dst.equals(src));

        Integer a = 127;
        Integer b = 127;
        //true
        System.out.println(a == b);

        Integer c = 200;
        Integer d = 200;
        //false
        System.out.println(c == d);
    }
```


### byte 类型的取值范围为 -128-127

https://blog.csdn.net/qq_23418393/article/details/57421688

- 机器数
```
带符号的。正数为0，负数为1 。

+3 => 0000 00011
-3 => 1000 00011
```

- 真值
```
带符号位的机器数对应的数值称为机器的真值

0000 00011 => +3
1000 00011 => -3
``` 
- 原码 
```
符号位加上真值的绝对值

0000 00011 => +3
1000 00011 => -3
```
- 反码 
```
正数的反码是其本身
负数的反码是符号位不变，其余各位取反
```

- 补码
```
正数的补码是其本身
负数的补码是符号位不变，其余各位取反。最后+1（反码 +1 ）
```



> 正数的原码、反码、补码都相同

> 负数的反码是原码除符号位各位取反，补码是原码 +1 

|数值|原码|反码|补码|
| :---: | :---: | :---: | :---: |
| +1 |  0000 0001 | 0000 0001 | 0000 0001|
|  -1 |  1000 0001 | 1111 1110 | 1111 1111| 


#### 原码做运算

1-1  = 1 + （-1）= 0000 0001 + 0000 0001 = 0000 0010 = -2 （明显错误）
#### 反码做运算
1-1  = 1 + （-1）= 0000 0001 + 11111110 = 1111 1111(这里是反码) = 1000 0000(原码) = -0(真值)   1000 0000 和 0000 0000 是一样的 
 
#### 补码做运算
1-1 = 1 + （-1）= 0000 0001 + 1111 1111 = 0000 0000 (这里是补码) = 0 (真值)  这样 0  用 0000 0000 表示    1000 0000 表示 -128


eg: -1 - 127 = -1 + (-127) = 1111 1111 + 0111 1111 = 1 000 0000 (-128)  


机器使用补码做运算，所以  byte 8 位 =>  [-2 ^ 7, 2 ^ 7 -1 ] , 32位的int 型 [-2 ^ 31 - 2 ^ 31 -1]
