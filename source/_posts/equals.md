---
title: equals
date: 2019-05-08 22:15:47
tags: java
---


> 优雅使用 equals 方法


### 问题

看下列代码，常量应该在前，变量在后，否则，会抛出空指针异常。

```
public static void main(String[] args) {
    String s = null;
    System.out.println("111".equals(s));
    System.out.println(s.equals("111"));
}
```

![](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/beer/blog/equals.jpg
)


<!--more-->

### 分析

因为所有类的父类都是 Object ,String 类重写了 equals 方法。所以，直接看 Object 的 equals方法。
```
/**
     * Indicates whether some other object is "equal to" this one.  
     * <p>
     * The {@code equals} method implements an equivalence relation
     * on non-null object references:
     * <ul>
     * <li>It is <i>reflexive</i>: for any non-null reference value
     *     {@code x}, {@code x.equals(x)} should return
     *     {@code true}.
     * <li>It is <i>symmetric</i>: for any non-null reference values
     *     {@code x} and {@code y}, {@code x.equals(y)}
     *     should return {@code true} if and only if
     *     {@code y.equals(x)} returns {@code true}.
     * <li>It is <i>transitive</i>: for any non-null reference values
     *     {@code x}, {@code y}, and {@code z}, if
     *     {@code x.equals(y)} returns {@code true} and
     *     {@code y.equals(z)} returns {@code true}, then
     *     {@code x.equals(z)} should return {@code true}.
     * <li>It is <i>consistent</i>: for any non-null reference values
     *     {@code x} and {@code y}, multiple invocations of
     *     {@code x.equals(y)} consistently return {@code true}
     *     or consistently return {@code false}, provided no
     *     information used in {@code equals} comparisons on the
     *     objects is modified.
     * <li>For any non-null reference value {@code x},
     *     {@code x.equals(null)} should return {@code false}.
     * </ul>
     * <p>
     * The {@code equals} method for class {@code Object} implements
     * the most discriminating possible equivalence relation on objects;
     * that is, for any non-null reference values {@code x} and
     * {@code y}, this method returns {@code true} if and only
     * if {@code x} and {@code y} refer to the same object
     * ({@code x == y} has the value {@code true}).
     * <p>
     * Note that it is generally necessary to override the {@code hashCode}
     * method whenever this method is overridden, so as to maintain the
     * general contract for the {@code hashCode} method, which states
     * that equal objects must have equal hash codes.
     *
     * @param   obj   the reference object with which to compare.
     * @return  {@code true} if this object is the same as the obj
     *          argument; {@code false} otherwise.
     * @see     #hashCode()
     * @see     java.util.HashMap
     */
    public boolean equals(Object obj) {
        return (this == obj);
    }
```


看 ul 标签内的内容，五点

- 自反性。x.equals(x) 必须返回 true
- 对称性。x.equals(y) 与 y.equals(x) 结果一致
- 传递性。x.equals(y),y.equals(z),x.equals(z) 结果一致
- 一致性。只要没修改，多次调用的结果一致。
- x.equals(null),必须返回false.

equals 方法是最具辨别力的等价关系的判断，如果此方法返回true,则他们的值相等。

重写 hashcode 方法时候，需要重写 equals 方法。因为要保证相等的对象，必须具有相同的哈希吗。

### 解决

- 常量在前，变量在后
```
System.out.println("111".equals(s));
```

- 使用 Objects.equals(Object a, Object b)
```
System.out.println(Objects.equals(s,"111"));
```

底层实现：

```
public static boolean equals(Object a, Object b) {
    return (a == b) || (a != null && a.equals(b));
}
```