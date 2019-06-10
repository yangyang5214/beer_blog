---
title: optional
date: 2019-06-10 11:53:08
tags: java
---


> 参考：https://www.cnblogs.com/rjzheng/p/9163246.html

ps: 读完我也不知道怎么用，除非项目中全部用 optional 写，比如 mybatis 的返回值。有时候把逻辑搞复杂了。

### 源码

#### 构造函数

```
private Optional(T value) {
    this.value = Objects.requireNonNull(value);
}
```

#### of

```
public static <T> Optional<T> of(T value) {
    return new Optional<>(value);
}
```

#### ofNullable

如果为 null ,则 创建 empty 对象

```
public static <T> Optional<T> ofNullable(T value) {
    return value == null ? empty() : of(value);
}
```

#### empty

创建 empty 对象

```
public static<T> Optional<T> empty() {
    @SuppressWarnings("unchecked")
    Optional<T> t = (Optional<T>) EMPTY;
    return t;
}
```

<!--more-->
####  equals
```
@Override
public boolean equals(Object obj) {
    if (this == obj) {
        return true;
    }
    if (!(obj instanceof Optional)) {
        return false;
    }
    Optional<?> other = (Optional<?>) obj;
    return Objects.equals(value, other.value);
}
```

#### get

获取当前值

```
public T get() {
    if (value == null) {
        throw new NoSuchElementException("No value present");
    }
    return value;
```

#### isPresent
```
 public boolean isPresent() {
     return value != null;
 }
```

#### ifPresent
```
public void ifPresent(Consumer<? super T> consumer) {
    if (value != null)
        consumer.accept(value);
}
```

用法：
```
optional.ifPresent(p -> System.out.println(p));
```


#### filter

过滤，符合条件就返回，否则返回空对象

```
 public Optional<T> filter(Predicate<? super T> predicate) {
     Objects.requireNonNull(predicate);
     if (!isPresent())
         return this;
     else
         return predicate.test(value) ? this : empty();
 }
```

System.out.println(optional.filter(p->p.equals("111")))

#### orElse

如果是null , 则赋值

```
public T orElse(T other) {
    return value != null ? value : other;
}
```


