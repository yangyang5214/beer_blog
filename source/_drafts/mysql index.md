---
title: mysql index
tags: mysql
notebook: 
---



> [mysql 官网](https://dev.mysql.com/doc/refman/8.0/en/mysql-indexes.html) 

### 8.3.1 How MySQL Uses Indexes

Indexes are used to find rows with specific column values quickly. Without an index, MySQL must begin with the first row and then read through the entire table to find the relevant rows. The larger the table, the more this costs. If the table has an index for the columns in question, MySQL can quickly determine the position to seek to in the middle of the data file without having to look at all the data. This is much faster than reading every row sequentially.


使用索引可以避免全表扫描。

Most MySQL indexes (PRIMARY KEY, UNIQUE, INDEX, and FULLTEXT) are stored in B-trees. Exceptions: Indexes on spatial data types use R-trees; MEMORY tables also support hash indexes; InnoDB uses inverted lists for FULLTEXT indexes.

PRIMARY KEY , UNIQUE ,INDEX , FULLTEXT   => B-trees
spatial data type（空间数据类型） => R - trees
MEMORY table => hash index 
FULLTEXT => inverted lists (倒排索引)  参考：https://www.cnblogs.com/zlslch/p/6440114.html

MySQL uses indexes for these operations:

- To find the rows matching a WHERE clause quickly.

匹配where条件

- To eliminate rows from consideration. If there is a choice between multiple indexes, MySQL normally uses the index that finds the smallest number of rows (the most selective index).

索引有选择的话，选择最小行数索引

- If the table has a multiple-column index, any leftmost prefix of the index can be used by the optimizer to look up rows. For example, if you have a three-column index on (col1, col2, col3), you have indexed search capabilities on (col1), (col1, col2), and (col1, col2, col3). For more information, see Section 8.3.6, “Multiple-Column Indexes”.
最左前缀索引
例子：如果  (col1), (col1, col2) 上有索引
则： 
(col1), (col1, col2), (col1, col2, col3)
这三种情况会使用索引

- To retrieve rows from other tables when performing joins. MySQL can use indexes on columns more efficiently if they are declared as the same type and size. In this context, VARCHAR and CHAR are considered the same if they are declared as the same size. For example, VARCHAR(10) and CHAR(10) are the same size, but VARCHAR(10) and CHAR(15) are not.

在表连接的时候，join  on 条件的 类型和大小相同才会使用索引

For comparisons between nonbinary string columns, both columns should use the same character set. For example, comparing a utf8 column with a latin1 column precludes use of an index.

 nonbinary string columns，非二进制列，需要相同的字符集

Comparison of dissimilar columns (comparing a string column to a temporal or numeric column, for example) may prevent use of indexes if values cannot be compared directly without conversion. For a given value such as 1 in the numeric column, it might compare equal to any number of values in the string column such as '1', ' 1', '00001', or '01.e1'. This rules out use of any indexes for the string column.

不相似列不使用索引。数据类型要相同


- To find the MIN() or MAX() value for a specific indexed column key_col. This is optimized by a preprocessor that checks whether you are using WHERE key_part_N = constant on all key parts that occur before key_col in the index. In this case, MySQL does a single key lookup for each MIN() or MAX() expression and replaces it with a constant. If all expressions are replaced with constants, the query returns at once. For example:

```
SELECT MIN(key_part2),MAX(key_part2)
  FROM tbl_name WHERE key_part1=10;
```
没明白


- To sort or group a table if the sorting or grouping is done on a leftmost prefix of a usable index (for example, ORDER BY key_part1, key_part2). If all key parts are followed by DESC, the key is read in reverse order. (Or, if the index is a descending index, the key is read in forward order.) See Section 8.2.1.14, “ORDER BY Optimization”, Section 8.2.1.15, “GROUP BY Optimization”, and Section 8.3.13, “Descending Indexes”.

sort  group 使用索引

- In some cases, a query can be optimized to retrieve values without consulting the data rows. (An index that provides all the necessary results for a query is called a covering index.) If a query uses from a table only columns that are included in some index, the selected values can be retrieved from the index tree for greater speed:

```
SELECT key_part3 FROM tbl_name
  WHERE key_part1=1
```

是说，要查询的列只有   key_part3 ，但是 key_part3 有索引，所以先查询所有 key_part3 的 结果，然后，使用where 条件。索引优化？？？覆盖索引