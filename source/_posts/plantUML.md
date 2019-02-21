---
title: plantUML
date: 2019-01-22 15:10:19
tags: [UML,设计模式]
---

> 泛化，实现，依赖，关联，组合，聚合

> 由弱到强：依赖 > 关联 > 聚合 > 组合

<!--more-->
### 依赖

局域变量、方法形参或者静态方法的调用。

比如开车，则在 Driver 类的 driver 方法中传入参数 car

![](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/blog/201901221509.png
)
```
@startuml

class Car{
    + move() : void
}
class Driver{
    + driver(Car car) : void
}

Car <... Driver
@enduml
```


### 关联

成员变量的关系

![](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/blog/assocation.png
)

```
@startuml

class People{
    - head : Head
}

class Head{

}

Head <-- People

@enduml
```

### 泛化

继承关系

![](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/blog/20190122-extend.png
)

```
@startuml

class People{
    + move(): void
    + say() : void
}

class Student{
    + study() : void
}

class Teacher{
    + teach() : void
}

People <|-- Student
People <|-- Teacher
@enduml
```

### 实现

接口实现的关系

![](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/blog/20190122-implent.png
)
```
@startuml

interface Car{
    + move() : void
}

class Benz{
    + move() : void
}

Car <|.. Benz
@enduml
```

### 组合

有生命周期的关系（组合模式）

![](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/blog/composite.png
)
```
@startuml

abstract Company{
    - name : String
    + add(Company company)
    + remove(Company company)
    + display()
}

Class ConcreteCompany{
    -  companyList : List<Company>
    + add(Company company)
    + remove(Company company)
    + display()
}

class Employee{
    - name : String
    + display()
}


Company <|-- Employee
Company <|-- ConcreteCompany

Company <--* ConcreteCompany
@enduml
```

### 聚合

一对多的关系（关联关系的一部分，非常强的关联关系）

![](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/blog/Aggregation.png
)

```
@startuml

class Route{
    - name : String
    - routeDetail: RouteDetail
}

class RouteDetail{

}

RouteDetail <--o Route

@enduml
```
