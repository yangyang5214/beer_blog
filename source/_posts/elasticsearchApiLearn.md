---
title: Elasticsearch Api Learn
date: 2019-04-29 22:48:30
tags: elasticsearch
---

> 就是个人理解整理，建议直接看[官网APi](https://www.elastic.co/guide/en/elasticsearch/reference/current/index.html)。

## getting started

Elasticsearch is a highly scalable open-source full-text search and analytics engine. It allows you to store, search, and analyze big volumes of data quickly and in near real time. It is generally used as the underlying engine/technology that powers applications that have complex search features and requirements.

高度可扩展的开源全文搜索和分析引擎。


<!--more-->
### basic concepts

#### Near Realtime (NRT)

近实时搜索引擎

#### cluster
集群。一个或者多个节点服务器的集合。

#### node

A node is a single server that is part of your cluster, stores your data, and participates in the cluster’s indexing and search capabilities. 


及时集群的单个服务。

#### index

An index is a collection of documents that have somewhat similar characteristics. 

indexname 必须小写

#### type

6.0.0以后删除

一个 index 可以创建多个 type

#### document

A document is a basic unit of information that can be indexed. json 数据格式。

#### shards & replicas

### installation

启动

```
cd elasticsearch-7.0.0/bin
./elasticsearch
```

### exploring your cluster

#### cluster health

```
GET /_cat/health?v
```

```
epoch      timestamp cluster       status node.total node.data shards pri relo init unassign pending_tasks max_task_wait_time active_shards_percent
1556552434 15:40:34  elasticsearch yellow          1         1      7   7    0    0        5             0                  -             58.3%
```
cluster: elasticsearch
status: yellow

- green everything is good (cluster is fully functional)
- Yellow all data is available but some replicas are not yet allocated (cluster is fully functional) 
- red  some data is not available for whatever reason (cluster is partially functional)


状态是黄色的原因是：some replicas are not yet allocated。因为目前是单个节点，所以，副本未分配节点，如果有第二个节点连接进来，就是 green 了，

```
GET /_cat/nodes?v
```
```
ip        heap.percent ram.percent cpu load_1m load_5m load_15m node.role master name
127.0.0.1           44          73  16    1.41                  mdi       *      Y9IFxsQ\
```

#### list all index

```
GET /_cat/indices?v

```

```
health status index     uuid                   pri rep docs.count docs.deleted store.size pri.store.size
green  open   company   Yu8tCUCBRrSC5RGTVOCOAg   1   0          1            0      4.1kb          4.1kb
green  open   .kibana_1 bk7ZlGoNSPaz5niWUWnwqA   1   0          3            0     11.9kb         11.9kb
yellow open   beer      UWHB7v38S4mo_E-bqsGYjg   5   1          6            0     23.9kb         23.9kb
```
#### create an index


![](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/beer/blog/es_create_index.png)


todo  后续找下原因
```
#! Deprecation: the default number of shards will change from [5] to [1] in 7.0.0; if you wish to continue using the default of [5] shards, you must manage this on the create index request or with an index template
```
#### index and query a document

![](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/beer/blog/es_put_document.png
)

```
# 格式为 /index/_doc/id  pretty 忽略(将返回的信息，以可读的方式展示)
PUT /customer/_doc/1?pretty
{
  "name": "beer"
}
```

![](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/beer/blog/es_search_doc.png
)
#### delete an index  删除索引
```
delete /customer
```
### modifying your data 修改数据

#### indexing/replacing  documents 索引/替换


推动一条数据
```
PUT /customer/_doc/1
{
    "name": "beer"
}
```

再次执行，则是替换
```
PUT /customer/_doc/1
{
    "name": "beer"
}
```

不指定ID，则是自动生成。注意，使用的是POST
```
POST /customer/_doc
{
    "name": "beer"
}
```

![](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/beer/blog/es_auto_id.png
)


#### updating documents 更新

https://www.elastic.co/guide/en/elasticsearch/reference/current/getting-started-update-documents.html


Note though that Elasticsearch does not actually do in-place updates under the hood. Whenever we do an update, Elasticsearch deletes the old document and then indexes a new document with the update applied to it in one shot.

所以说：update 的操作还是：delete -> insert.也就是先删后增。可能是因为elasticsearch是 near real time

```
POST /customer/_update/1
{
    "doc": {
        "name": "beer test update"
    }
}
```

当然也可以增加字段（因为底层是先删后增）
```
POST /customer/_update/1
{
    "doc": {
        "name": "beer test update",
        "age": 20
    }
}
```

![](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/beer/blog/es_update_add_field.png
)
也可以使用脚本,使 age = age + 5 

```
POST /customer/_update/1
{
  "script" : "ctx._source.age += 5"
}
```
![](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/beer/blog/es_update_by_script.png
)

ctx._source refers to the current source document that is about to be updated.

**ctx._source** 指的是要更新的当前文档


#### delete documents 删除文档

```
DELETE /customer/_doc/1
```
#### batch processing 批次处理


一个简单的例子
```
POST /customer/_bulk
{"index":{"_id":"4"}}
{"name":"John Doe"}
{"index":{"_id":"5"}}
{"name":"beer"}
```

第一个是 updade，第二个是 delete
```
POST /customer/_bulk?pretty
{"update":{"_id":"1"}}
{"doc": { "name": "John Doe becomes Jane Doe" } }
{"delete":{"_id":"2"}}
```
delete 之后并没有 doc

![](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/beer/blog/es_bulk.png
)

The Bulk API does not fail due to failures in one of the actions. If a single action fails for whatever reason, it will continue to process the remainder of the actions after it. When the bulk API returns, it will provide a status for each action (in the same order it was sent in) so that you can check if a specific action failed or not.


批次计算的API，不会因为一个失败而全部失败，如果一个失败，则继续执行。最后会返回状态。


### exploring your data  探索数据

#### sample dataset 


[一个神奇的json网站](https://next.json-generator.com/E1c5bmfjL)

#### query language

- match_all

```
GET /customer/_search
{
  "query": {
    "match_all": {}
  }
}
```

- size 

```
GET /customer/_search
{
  "query": {
    "match_all": {}
  },
  "size": 1
}
```

- from to

```
GET /customer/_search
{
  "query": {
    "match_all": {}
  },
  "from": 2,
  "to": 4
}
```

- sort

```
GET /customer/_search
{
  "query": {
    "match_all": {}
  },
  "sort": [
    {
      "age": {
        "order": "desc"
      }
    }
  ]
}
```
#### executing searches

- _source

自定义返回字段

```
GET /customer/_search
{
  "query": {
    "match_all": {}
  },
  "_source": ["name"]
}
```

- match

匹配字段值(文本)。contains。注意是包含。而不是精确匹配。相当于 like(mysql).

```
GET /customer/_search
{
  "query": {
    "match": {
      "age": 25
    }
  }
}
```
如果是匹配 数字，则是精确匹配。

![](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/beer/blog/es_match_number.png
)

match 1010 的有数据，但是 match 10 的就没结果

![](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/beer/blog/es_match_number_1.png
)
- match_phrase

匹配短语

```
GET /customer/_search
{
  "query": {
    "match_phrase": {
      "name": "beer test"
    }
  }
}
```

- bool

```
GET /customer/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "match": {
            "name": "beer-1"
          }
        },
        {
          "match": {
            "age": 20
          }
        }
      ]
    }
  }
}
```

- must_not

```
GET /customer/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "match": {
            "name": "beer-1"
          }
        }
      ],
      "must_not": [
        {
         "match": {
           "age": 20
         }
        }
      ]
    }
  }
}
```

#### executing filters  过滤

```
GET /customer/_search
{
  "query": {
    "bool": {
      "must": [
        {"match_all": {}}
      ],
      "filter": {
        "range": {
          "age": {
            "gte": 10,
            "lte": 200
          }
        }
      }
    }
  }
}
```

#### executing aggregations   聚合

- group

```
GET /customer/_search
{
  "size": 0,
  "aggs": {
    "group_by_age": {
      "terms": {
        "field": "age",
        "size": 10
      }
    }
  }
}
```


```
select age,count(*)
from customer
group by age
order by count(*) desc
limit 10
```

![](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/beer/blog/es_group_by.png
)


Note that we set size=0 to not show search hits because we only want to see the aggregation results in the response.

size = 0 ,表示只需要 aggs 的结果，并不需要 hits 的结果。

- group avg

```
GET /customer/_search
{
  "size": 0,
  "aggs": {
    "group_by_age": {
      "terms": {
        "field": "age",
        "size": 10
      },
      "aggs": {
        "avg_age": {
          "avg": {
            "field": "age"
          }
        }
      }
    }
  }
}
```

```
select age,count(*),avg(age)
from customer
group by age
order by count(*) desc
limit 10
```

![](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/beer/blog/es_group_avg.png
)

#### conclusion 结束

elasticsearch is both a simple and complex product.


## set up elasticsearch

https://www.elastic.co/guide/en/elasticsearch/reference/current/setup.html

//todo

### install

传送门：https://www.elastic.co/guide/en/elasticsearch/reference/current/install-elasticsearch.html


包含各个操作系统，以及docker

### configuring

https://www.elastic.co/guide/en/elasticsearch/reference/current/settings.html


![](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/beer/blog/cosupload/es_config.png
)

...

## API conventions API 的一些约定

### Mutiple Indices

支持多个index

https://www.elastic.co/guide/en/elasticsearch/reference/current/multi-index.html 

//todo

- ignore_unavailable

- allow_no_indices

- expand_wildcards

### Date math support in index names 索引中的日期数字支持


索引中使用日期解析。

https://www.elastic.co/guide/en/elasticsearch/reference/current/date-math-index-names.html


### Common options 常见选项

#### Pretty Results

?pretty=true 格式化后的json返回。(use it for debugging only!). 仅仅用于 debug

#### Human readable output  人可读

Statistics are returned in a format suitable for humans (e.g. "exists_time": "1h" or "size": "1kb") and for computers (e.g. "exists_time_in_millis": 3600000 or "size_in_bytes": 1024). The human readable values can be turned off by adding ?human=false to the query string. This makes sense when the stats results are being consumed by a monitoring tool, rather than intended for human consumption. The default for the human flag is false.



#### Date Math  日记数学

对于日期格式，可以方便操作

- +1h  ：Add one hour
- -1d: Subtract one day
- /d: Round down to the nearest day 四舍五入一天

#### Response Filtering

对于返回结果的过滤（厉害了）

```
GET localhost:9200/_search?q=elasticsearch&filter_path=took,hits.hits._id,hits.hits._score"
```

返回
```
{
  "took" : 3,
  "hits" : {
    "hits" : [
      {
        "_id" : "0",
        "_score" : 1.6375021
      }
    ]
  }
}
```
#### Flat Settings  flat(平坦的)

结果以平坦的方式呈现（Flat）。

```
GET twitter/_settings?flat_settings=true

{
  "twitter" : {
    "settings": {
      "index.number_of_replicas": "1",
      "index.number_of_shards": "1",
      "index.creation_date": "1474389951325",
      "index.uuid": "n6gzFZTgS664GUfx0Xrpjw",
      "index.version.created": ...,
      "index.provided_name" : "twitter"
    }
  }
}
```
#### parameters

follow the convention of using underscore casing.

下划线的惯例

#### Boolean values

true and false. 其他值为 error

#### Number Values

可以使用数字类型的 string 
####  Time Units 时间单位

![](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/beer/blog/es_time_unit.png
)

### URL-based access control

代理访问？？？

https://www.elastic.co/guide/en/elasticsearch/reference/current/url-access-control.html


## Document APIs  api 文档

### single documents apis 单文档


#### Index Api 

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

#### Get Index

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

#### delete APi

```
DELETE /beer/_doc/100
```

### multi-documents apis  多文档
