---
title: es-document-api
date: 2019-05-25 11:41:35
tags: elasticsearch
---

> [es documents api](https://www.elastic.co/guide/en/elasticsearch/reference/current/docs.html).对 es api 的学习。

### single documents api

#### Index 

推送一条数据（或者更新）

![](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/beer/blog/es_put_date.png
)

返回结果：
```
{
  "_index" : "beer",
  "_type" : "_doc",
  "_id" : "1000",
  "_version" : 1,
  "result" : "created",
  "_shards" : {
    "total" : 2,
    "successful" : 1,
    "failed" : 0
  },
  "_seq_no" : 0,
  "_primary_term" : 1
}
```

##### Automatic Index Creation  自动创建索引

如果索引不存在，则自动创建 index

##### Operation Type

如果指定了  on_tyoe=create,如果 id 存在，则 error

![](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/beer/blog/cosupload
)

等价于：

![](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/beer/blog/es_create.png
)

##### Automatic ID Generation  ID 自动生成

如果没指定ID ，则自动生成ID.注意是 POST 不是 PUT

![](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/beer/blog/es_automatic_id_generation.png
)

<!--more-->
#### Get

![](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/beer/blog/es_get_by_id.png
)

##### realtime

默认情况下，get API  是实时的。只要 id 存在。如果 一个文档 update,但是not yet refreshed, 会去 refresh.

##### source filtering

默认  _source 是显示的。可以设置 _source=false.

![](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/beer/blog/es_source_false.png
)

##### stored fields

https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-get.html

##### get _source directly

直接获取 _source 内容

![](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/beer/blog/es_get_source.png
)

#### Delete

直接根据 id 删除文档

```
DELETE /beer/_doc/100
```
#### Delete By Query 

根据条件删除

```
POST customer/_delete_by_query
{
  "query": {
    "match":{
      "name": "beer"
    }
  }
}
```
默认的删除会根据版本号来计算，如果文档在获取版本快照和删除之间发生了修改，则会出现版本冲突。删除失败。


设置参数：conflicts=proceed。可以忽略版本冲突，继续删除。（If you’d like to count version conflicts rather than cause them to abort. then set conflicts=proceed on the url or "conflicts": "proceed" in the request body.）

```
POST customer/_delete_by_query?conflicts=proceed
{
  "query": {
    "match":{
      "name": "beer"
    }
  }
}
```

By default _delete_by_query uses scroll batches of 1000. You can change the batch size with the scroll_size URL parameter:

滚动大小（scroll_size）

```
POST customer/_delete_by_query?scroll_size=5000
{
  "query": {
    "match":{
      "name": "beer"
    }
  }
}
```
#### Update 

先增加一条数据

```
PUT customer/_doc/1
{
    "counter" : 1,
    "tags" : ["red"]
}
```


#### update by script

通过脚本更新数字类型

```
POST customer/_update/1
{
  "script": {
    "source": "ctx._source.counter += params.count",
    "lang": "painless",
    "params": {
      "count": 4
    }
  }
}
```

注：painless 是 es 的一个脚本。 //todo

通过脚本更新集合类型

```
POST customer/_update/1
{
  "script": {
    "source": "ctx._source.tags.add(params.tags)",
    "lang": "painless",
    "params": {
      "tags": "blue"
    }
  }
}
```


当然也可以删除：

```
POST customer/_update/1
{
    "script" : {
        "source": "if (ctx._source.tags.contains(params.tag)) { ctx._source.tags.remove(ctx._source.tags.indexOf(params.tag)) }",
        "lang": "painless",
        "params" : {
            "tag" : "blue"
        }
    }
}
```

In addition to _source, the following variables are available through the ctx map: _index, _type, _id, _version, _routing, and _now (the current timestamp).


增加新字段
```
POST customer/_update/1
{
    "script" : "ctx._source.new_field = 'value_of_new_field'"
}
```

删除字段
```
POST customer/_update/1
{
    "script" : "ctx._source.remove('new_field')"
}
```

if else 语句
```
POST customer/_update/1
{
    "script" : {
        "source": "if (ctx._source.tags.contains(params.tag)) { ctx.op = 'delete' } else { ctx.op = 'none' }",
        "lang": "painless",
        "params" : {
            "tag" : "green"
        }
    }
}
```


##### update with a partial document 

部分更新

```
POST customer/_update/1
{
    "doc" : {
        "name" : "new_name"
    }
}
```

如果想全部更新，则使用 index api(PUT 操作).

##### upserts

如果文档不存在，则 upsert 的值将被插入(并不会再去执行脚本)。否则，执行脚本。

```
POST customer/_update/2
{
    "script" : {
        "source": "ctx._source.counter += params.count",
        "lang": "painless",
        "params" : {
            "count" : 4
        }
    },
    "upsert" : {
        "counter" : 1
    }
}
```
##### doc_as_upsert

Instead of sending a partial doc plus an upsert doc, setting doc_as_upsert to true will use the contents of doc as the upsert value:

有点绕。意思就是：doc_as_upsert = true , 表示 将 doc 的内容替换原来的，而不是追加。
```
POST customer/_update/1
{
    "doc" : {
        "name" : "new_name"
    },
    "doc_as_upsert" : true
}
```
#### update by query 

The simplest usage of _update_by_query just performs an update on every document in the index without changing the source. This is useful to pick up a new property or some other online mapping change. Here is the API:

最简单的用法是对文档进行更新，而不更新源。（可能存在多个版本，获取新属性）

```
POST customer/_update_by_query?conflicts=proceed
```


也可以在 _update_by_query 里面使用 query DSL.

You can also limit _update_by_query using the Query DSL. 

This will update all documents from the twitter index for the user kimchy:
```
POST customer/_update_by_query?conflicts=proceed
{
  "query": { 
    "term": {
      "user": "kimchy"
    }
  }
}
```
### mutil-document api

批量操作 api

```
GET /_mget
{
  "docs":[
    {
      "_index": "customer",
      "_id": "1"
    },
     {
      "_index": "customer",
      "_id": "2"
    }
  ]
}
```

![](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/beer/blog/es_multi_mget.png
)

也可以复用 index.

```
GET /customer/_mget
{
  "docs":[
    {
      "_id": "1"
    },
     {
      "_id": "2"
    }
  ]
}
```

变种：简化版
```
GET /customer/_mget
{
  "ids": [
    "1",
    "2"
  ]
}
```
#### source filtering


直接官网的例子：

```
GET /_mget
{
    "docs" : [
        {
            "_index" : "test",
            "_type" : "_doc",
            "_id" : "1",
            "_source" : false
        },
        {
            "_index" : "test",
            "_type" : "_doc",
            "_id" : "2",
            "_source" : ["field3", "field4"]
        },
        {
            "_index" : "test",
            "_type" : "_doc",
            "_id" : "3",
            "_source" : {
                "include": ["user"],
                "exclude": ["user.location"]
            }
        }
    ]
}
```

#### bulk api

可以一起发送不同类型的操作。感觉用不到。api 很简单

https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-bulk.html

#### reindex api


最简单是就是 copy 一份。customer => new_customer

```
POST _reindex
{
  "source": {
    "index": "customer"
  },
  "dest": {
    "index": "new_customer"
  }
}
```

#### term vectors

一些分词的信息

![](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/beer/blog/es_term_vectors.png
)

#### optimistic concurrency control


es 分布式的架构，通过 _version 来确保不会被旧的数据覆盖。

![](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/beer/blog/es_concuuency_control.png
)

_seq_no 和 _primary_term 字段分别保存了   操作的 seq_no 和 操作的主要术语。

```
{
  "_index" : "customer",
  "_type" : "_doc",
  "_id" : "2",
  "_version" : 2,
  "_seq_no" : 51,
  "_primary_term" : 2,
  "found" : true,
  "_source" : {
    "counter" : 1
  }
}
```

