---
title: spring事务管理
date: 2019-02-26 22:24:02
tags: [java,spring]
---

> spring 事务管理

### 事务

- 原子性
- 一致性
- 隔离性
- 持久性

### 事务的并发问题

#### 脏读

一个线程中的事务读取到了另外一个线程中未提交的事务（后期可能roolback）

事务A读取了事务B更新的数据，然后B回滚操作，那么A读取到的数据是脏数据

#### 不可重复读

指一个线程中的事务读取到了另外一个线程中提交的update的数据。(update)

事务 A 多次读取同一数据，事务 B 在事务A多次读取的过程中，对数据作了更新并提交，导致事务A多次读取同一数据时，结果 不一致。

#### 幻读

指一个线程中的事务读取到了另外一个线程中提交的insert的数据。（insert or delete）

事务A开始读取，事务B提交，事务A再读取，发现数据不一致

### Mysql 事务隔离级别

#### Read Uncommitted（读取未提交内容）

在该隔离级别，所有事务都可以看到其他未提交事务的执行结果 ===> 脏读，不可重复读，幻读


#### Read Committed（读取提交内容）


一个事务只能看见已经提交事务所做的改变。====> 不可重复读，幻读

#### Repeatable Read（可重读）

mysql 默认的事务隔离级别，它确保同一事务的多个实例在并发读取数据时，会看到同样的数据行。（重点是：同样的数据行）。所以还是可能幻读。
mysql GAP 锁解决了幻读。

#### Serializable（可串行化） 

最高隔离级别，强制事务排序。使之不可能相互冲突，从而解决幻读。

![](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/beer/blog/mysql_transaction.png
)


###  mysql  锁

#### 共享锁（读），排他锁（写）

共享锁：用于不更改或不更新数据的操作（只读操作。eg:select

排他锁：用于数据修改操作，例如 INSERT、UPDATE 或 DELETE。确保不会同时同一资源进行多重更新。

### spring 事务

```
public interface PlatformTransactionManager{


    //基于事务的传播性，返回一个已经存在的事务或者创建一个新的事务
	TransactionStatus getTransaction(@Nullable TransactionDefinition definition) throws TransactionException;


    //提交事务
	void commit(TransactionStatus status) throws TransactionException;

    //回滚事务
	void rollback(TransactionStatus status) throws TransactionException;
}
```

```
public interface TransactionDefinition{

    //如果有事务,那么加入事务,没有的话新建一个(不写的情况下)
	int PROPAGATION_REQUIRED = 0;

    //如果其他bean调用这个方法,在其他bean中声明事务,那就用事务.如果其他bean没有声明事务,那就不用事务.
	int PROPAGATION_SUPPORTS = 1;

    //必须在一个已有的事务中执行,否则抛出异常
	int PROPAGATION_MANDATORY = 2;

    //不管是否存在事务,都创建一个新的事务,原来的挂起,新的执行完毕,继续执行老的事务
	int PROPAGATION_REQUIRES_NEW = 3;

    //容器不为这个方法开启事务
    int PROPAGATION_NOT_SUPPORTED = 4;

    //必须在一个没有的事务中执行,否则抛出异常(与Propagation.MANDATORY相反)
	int PROPAGATION_NEVER = 5;

    //嵌套事务
	int PROPAGATION_NESTED = 6;
}
```

