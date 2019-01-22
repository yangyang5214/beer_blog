---
title: mysql explain
tags: mysql
notebook: 
---

> 彻底搞清mysql索引，理论 + 实践，可直接看第三部分例子



### 1、数据准备

student 表，id ，name, sex, address , remark 四个字段

id 主键 

name + address 组合索引

remark 普通索引

sex 没有索引

```mysql
drop table if EXISTS student;
create table student(
id int auto_increment PRIMARY key,
name varchar(20),
sex varchar(2),
address VARCHAR(20),
remark VARCHAR(255)
);



# 创建一个存储过程，造数据
drop PROCEDURE if EXISTS INSERT_student;
create PROCEDURE INSERT_student()
BEGIN
DECLARE i INT DEFAULT 0;
WHILE i< 500000 DO
INSERT into student(name,address,sex,remark) 
SELECT CONCAT('name',FLOOR(1+ RAND()*1000)),CONCAT('address',FLOOR(1+ RAND()*1000)),1,CONCAT('remark',FLOOR(1+ RAND()*1000));
set i= i+1;
end WHILE;
END;


# 调用存储过程
CALL INSERT_student();


# remark 普通索引 （index 普通，unique 唯一，primary key 主键）
alter table student add index remark_index(remark);


# name and address 组合索引
alter table student add index name_address_index(name,address);
```

### 2、explain

[mysql 官网 Optimizing Queries with EXPLAIN](https://dev.mysql.com/doc/refman/8.0/en/execution-plan-information.html)

> 推荐直接看官网，下面只是我个人理解

```
# 例子
EXPLAIN SELECT * from student;
```

| colume | meaning |
| ------------- | ------------------------------------------------------------ |
| id | The [`SELECT`](https://dev.mysql.com/doc/refman/8.0/en/select.html) identifier |
| select_type | SIMPLE ,PRIMARY, UNION, DEPENDENT UNION，UNION RESULT，SUBQUERY,DEPENDENT SUBQUERY,DERIVED,MATERIALIZED,UNCACHEABLE SUBQUERY,UNCACHEABLE UNION |
| table | 查询的表 |
| partitions | |
| type | system const eq_ref ref fulltext ref_or_null index_merge unique_subquery index_subquery range index all |
| possible_keys | |
| key | |
| key_len | |
| ref | |
| rows | |
| filtered | |
| extra | |

#### select_type

| select_type value | meaning |
| -------------------- | ----------------------------------------- |
| SIMPLE | 简单查询，没有用到 union 和子查询 |
| PRIMARY | 最外层的select |
| UNION | 在select 之后使用 union |
| DEPENDENT UNION | 在select 之后使用 union，依赖于外部子查询 |
| UNION RESULT | union 的结果 |
| SUBQUERY | 第一个子查询 |
| DEPENDENT SUBQUERY | 第一个子查询，依赖于外部子查询 |
| DERIVED | |
| MATERIALIZED | |
| UNCACHEABLE SUBQUERY | |
| UNCACHEABLE UNION | |

- SIMPLE

```
explain select * from student 
```

![](https://yangyang-1256523277.cos.ap-shanghai.myqcloud.com/blog/20181230120447.png)

- PRIMARY, UNION , UNION RESULT

```
EXPLAIN
SELECT *
from (
SELECT *
from student 
where id in (1,2,3)
) s1
UNION
SELECT *
from student
where name in (
SELECT NAME
from student 
where name like '%100%'
)
```

![](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/blog/20181230122110.png)

#### table 

查询的表名

还包括： <union*M*,*N*> `<derived*N*> `<subquery*N*>`

#### partitions 

分区相关

#### type

- const

The table has at most one matching row, which is read at the start of the query. 
在开始查询之前就知道返回结果最多只有一行

```
EXPLAIN SELECT * from student where id= 1
```

- eq_ref 

除了system 和 const之外的最好的类型。

compared using the = operator. 

```
# 使用 eq_ref
EXPLAIN 
SELECT * 
from student s1,student s2
where s1.id = s2.id


# 使用all,因为 sex 上没有索引
EXPLAIN 
SELECT * 
from student s1,student s2
where s1.sex = s2.sex
```

- ref

最左前缀索引时候使用

```
# name and address 是联合索引，所以最左前缀索引的话 ，只有 name 会用到索引。
EXPLAIN
SELECT *
from student
where name = '1'

# 用到索引
EXPLAIN
select *
from student
where name = '1' and address = '2'

# 不会用到索引
EXPLAIN
SELECT *
from student
where address = '1'
```

- fulltext

全文索引

- ref or null

```
# 和ref 类似 但是 包含 null
EXPLAIN
SELECT *
from student
where name = '1' or name is null
```

- [`index_merge`](https://dev.mysql.com/doc/refman/8.0/en/explain-output.html#jointype_index_merge)

索引合并优化

- [`unique_subquery`](https://dev.mysql.com/doc/refman/8.0/en/explain-output.html#jointype_unique_subquery)

子查询用了 primary or unique 

- [`index_subquery`](https://dev.mysql.com/doc/refman/8.0/en/explain-output.html#jointype_index_subquery)

子查询用了普通索引

- range

```
EXPLAIN
SELECT *
from student
where id BETWEEN 0 and 1
```

- index

1、覆盖扫描（比all快，因为索引的大小通常小于表结构）

2、A full table scan is performed using reads from the index to look up data rows in index order. Uses index does not appear in the Extra column.

name and address 是联合索引，下面两个都会用到索引。

```
EXPLAIN
SELECT name
from student

EXPLAIN
SELECT address
from student
```

但是 在 where 条件下 ，只有 name 满足最左前缀原则，会用到索引，address 不会

```
# type 为 all
EXPLAIN
SELECT *
from student
where address = '11'


# type 为 index
EXPLAIN
SELECT *
from student
where name = '11'
```

- all

```
# 全表扫描，sex 上没有索引
EXPLAIN
SELECT *
from student
where sex = '11'
```

#### key 

```
实际使用的 index
```

#### rows

```
影响的行数。InnoDB 是估计值
```

#### extra

[extra 的额外信息](https://dev.mysql.com/doc/refman/8.0/en/explain-output.html#explain-extra-information)

### 3、case 

```
select *
from student
where name = '1' and sex = '1' and address = '2'
```

上述会用到索引 name_address_index 



```
EXPLAIN
select *
from student
where name = 1 and address = '2'
```

不会用到索引，因为 name 的类型不一致



```
EXPLAIN
select *
from student
where name = '1' and address = 1
```

用到索引（尽管类型不一致），因为 name 最左前缀原则，然后再查询 address = 1 的记录

