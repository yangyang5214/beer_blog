---
title: clone
tags: java
notebook: 
---



### 浅拷贝
```
默认是浅拷贝的。
实现  cloneable 方法

重写 clone() 方法

只复制引用，改变引用

可以自己定义一个类

比如  people 类  引用 user 类


public static void main(String[] args) {
        UserClone user = new UserClone();
        user.setId(1);
        user.setName("tom");
        user.setIdCard(new Integer(999));


        People people = new People();
        people.setName("www");
        user.setPeople(people);

        UserClone user2 = null;
        try {
            user2 = (UserClone) user.clone();
        } catch (CloneNotSupportedException e) {
            e.printStackTrace();
        }

        user2.setName("Tom");
        System.out.println(user2 == user);
        System.out.println(user2.getId() == user.getId());
        System.out.println(user2.getName() == user.getName());

        System.out.println("user2 name: " + user2.getName());
        System.out.println("user name: " + user.getName());

        user2.setId(111);
        System.out.println("user2 id: " + user2.getId());
        System.out.println("user id: " + user.getId());

        user2.setIdCard(new Integer(1000));
        System.out.println("user2 idCard: " + user2.getIdCard());
        System.out.println("user idCard: " + user.getIdCard());


        // 浅拷贝
        People people1 = new People();
        people1.setName("qqq");
        user2.setPeople(people);
        System.out.println("user2 people: " + user2.getPeople().getName());
        System.out.println("user people: " + user.getPeople().getName());
    }
```
### 深拷贝

在拷贝对象时候，同时会对引用指向的对象进行拷贝。



### 区别

是否对对象中的引用变量所指向的对象进行拷贝


