---
title: mysql copy table 
date: 2020-05-06 21:52:28
tags: mysql
---

> mysql 如何快速的备份一个表。

原文：https://tableplus.com/blog/2018/11/how-to-duplicate-a-table-in-mysql.html

下面是搬运的：

In this post, we are going to show you how to copy a table in MySQL.

First, this query will copy the data and structure, but the indexes are not included:
```
CREATE TABLE new_table SELECT * FROM old_table;
```
Second, this query will copy the table structure and indexes, but not data:
```
CREATE TABLE new_table LIKE old_table;
```
So, to copy everything, including database objects such as indexes, primary key constraint, foreign key constraints, triggers, etc., run these queries:

```
CREATE TABLE new_table LIKE old_table; 
INSERT new_table SELECT * FROM old_table;
```

If you want to copy a table from one database to another database:

```
CREATE TABLE destination_db.new_table LIKE source_db.old_table;

INSERT destination_db.new_table
SELECT
    *
FROM
    source_db.old_table;
```

### 例子
```
CREATE TABLE keyword_copy LIKE keyword; 
INSERT keyword_copy SELECT * FROM keyword;


CREATE TABLE keyword_class_copy LIKE keyword_class; 
INSERT keyword_class_copy SELECT * FROM keyword_class;

CREATE TABLE keyword_class_rel_copy LIKE keyword_class_rel; 
INSERT keyword_class_rel_copy SELECT * FROM keyword_class_rel;
```


