---
title: removeif
date: 2019-06-08 16:16:36
tags: java
---


> Collection 类的 removeif 方法的学习。起因是项目中要实现删除集合中以 2 开头的元素。


### fori 循环

其中 i-- ,是重点，如果删除了一个元素，则list 的next 元素会填坑（删除的那个元素的坑），所以，如果不执行 i--,则会漏掉删除的元素的后面的那个元素。

```
public void removeByFori(List<String> list){
    for (int i = 0; i < list.size(); i++) {
        if (list.get(i).startsWith("1")){
            list.remove(i);
            i--;
        }
    }
    System.out.println(list);
}
```

或者反着来

```
public void removeByForiFlip(List<String> list) {
    for (int i = list.size() - 1; i > 0; i--) {
        if (list.get(i).startsWith("1")) {
            list.remove(i);
        }
    }
    System.out.println(list);
}
```

<!--more-->

### foreach


会报错。ConcurrentModificationException。

```
public void removeByForeach(List<String> list) {
    for (String s : list) {
        if (s.startsWith("1")) {
            list.remove(s);
        }
    }
    System.out.println(list);
}
```

![传送门：Fail Fast and Fail Safe Iterators in Java
](https://www.geeksforgeeks.org/fail-fast-fail-safe-iterators-java/)

### iterator

```
public void removeByIterator(ArrayList<String> list) {
    Iterator iterator = list.iterator();
    while (iterator.hasNext()){
        String s = (String) iterator.next();
        if (s.startsWith("1")) {
            iterator.remove();
        }
    }
    System.out.println(list);
}
```

使用迭代器删除

### removeIf

```
list.removeIf(p->p.startsWith("1"));
```

#### 源代码

Collections 集合的源代码


但是，ArrayList 有重写 removeIf 方法。大概原理还是 迭代器遍历。

``` 
default boolean removeIf(Predicate<? super E> filter) {
    Objects.requireNonNull(filter);
    boolean removed = false;
    final Iterator<E> each = iterator();
    while (each.hasNext()) {
        if (filter.test(each.next())) {
            each.remove();
            removed = true;
        }
    }
    return removed;
}
```

Objects.requireNonNull(filter);

```
 public static <T> T requireNonNull(T obj, String message) {
     if (obj == null)
         throw new NullPointerException(message);
     return obj;
 }
```

这用法很nice
