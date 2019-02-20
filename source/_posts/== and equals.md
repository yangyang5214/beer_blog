---
title: == and equals
tags: [java]
date: 2019-02-19 09:47:44
---

### 基本数据类型

使用 == 比较

### 类类型

#### object 

object 是所有类的基类

默认是比较的内存地址，和  ==  一样

···
public class Object{

    public boolean equals(Object obj) {
        return (this == obj);
    }
}
···


<!--more-->
#### String

String 类重写了Object类的 equals 方法，先判断内存地址是否相同。然后将字符串转化为字符数组然后依次比较每个字符是否相等。

```
public boolean equals(Object anObject) {
        if (this == anObject) {
            return true;
        }
        if (anObject instanceof String) {
            String anotherString = (String)anObject;
            int n = value.length;
            if (n == anotherString.value.length) {
                char v1[] = value;
                char v2[] = anotherString.value;
                int i = 0;
                while (n-- != 0) {
                    if (v1[i] != v2[i])
                        return false;
                    i++;
                }
                return true;
            }
        }
        return false;
    }
```

#### Integer 
```
public boolean equals(Object obj) {
        if (obj instanceof Integer) {
            return value == ((Integer)obj).intValue();
        }
        return false;
}
```

String，Integer 等类重写了 Object 的 equals 方法。先比较类型相同，然后比较值。