### 创建

```
use DATABASE_NAME
如果数据库存在则创建，否则切换到制定的数据库

db.createCollection(name,options)
name:集合名称
options:可选参数，制定有关内存大小及索引的选项
```


### 删除

```
db.dropDatabase()

删除当前数据库


db.collection.drop()
删除集合
```


### 插入

```
db.collection_name.insert(document)

插入文档
```

### 更新

```
db.collection.update(
	<query>,
	<update>,
	{
	upset:<boolean>,
	multiL<boolean>,
	writeConcern:<document>
	}

)


query:update 的查询条件
update:update的对象和一些更新的操作符，也可以理解为sql update 查询内set后面的
upset:如果不存在update的记录，是否插入objNew,默认为false,不插入
multi:默认为false,只更新找到的第一条记录，如果为true,则表示更新所有
writeConcern:抛出异常的级别


save()

替换
```

### 删除

```
db.collection.remove(
	<query>,
	<justOne>
)

query:删除文档的条件
justOne:如果为true,则只删除一个文档
```
### 查询

```
db.collection.find(query,projection)
query:可选，查询条件
projection:可选，使用投影操作符返回指定的键（默认，返回所有）

db.collection.find().pretty()
pretty() 方法以格式化的方式来显示文档
```
**where 语句**
http://www.runoob.com/mongodb/mongodb-query.html

**and 语句**

find()方法传入多个键（key），每个key以逗号隔开
```
db.col.find({key1:value1,key2:value2}).pretty()
```

**or 语句**

```
db.col.find(
   {
      $or: [
         {key1: value1}, {key2:value2}
      ]
   }
).pretty()
```
### 条件操作符

- > $gt
- < $lt
- >= $gte
- <= $lte
- != $ne
- = $eq

### $type 操作符

$type 操作符是基于BSON类型来检索集合中匹配的数据类型，并返回结果。

```
# 获取 col  集合中title 为 string 的数据
db.col.find({"title" : {$type : 'string'}})

```

### limit

```
db.collection.find().limit(NUMBER)

# skip 方法同样接受一个数字参数作为跳过的记录条数
>db.collection.find().limit(NUMBER).skip(NUMBER)

```

### sort

```
# 1 为升序，-1为降序
db.collection.find().sort({key:1})
```
### index

```
# options:1 为升序，-1 为降序
db.collection.createIndex(keys,options)
```

### 聚合

aggreagte 主要用于处理数据（统计平均值，求和等），并返回计算后的结果

```
db.mycol.aggregate([{$group : {_id : "$by_user", num_tutorial : {$sum : 1}}}])
{
   "result" : [
      {
         "_id" : "runoob.com",
         "num_tutorial" : 2
      },
      {
         "_id" : "Neo4j",
         "num_tutorial" : 1
      }
   ],
   "ok" : 1
}


类似sql的： select by_user, count(*) from mycol group by by_user
```




















