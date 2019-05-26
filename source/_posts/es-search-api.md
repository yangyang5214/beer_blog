---
title: es-search-api
date: 2019-05-25 22:12:39
tags: elasticsearch
---

> [search apis](https://www.elastic.co/guide/en/elasticsearch/reference/7.0/search.html). 

## search apis

- routing

只是个人理解。不一定对。


推动一条数据，指定 routing .
```
POST /customer/_doc?routing=beer
{
    "user" : "beer",
    "postDate" : "2009-11-15T14:12:12",
    "message" : "trying out Elasticsearch"
}
```

如果不指定 routing  则 es 会根据hash算法将数据存储到各个节点上。如果现在有 10 数据，恰好都是  user = beer 的，但是 _id 是不一样的，所以可能存储在不同节点，不同分片上。但是如果指定了 routing = beer , 则 beer 的就存储在一块。查找的时候指定 routing 查询就会更快。


In such a case, if we want to search only on the tweets for a specific user, we can specify it as the routing, resulting in the search hitting only the relevant shard:

```
POST /customer/_search?routing=beer
{
    "query": {
        "bool" : {
            "must" : {
                "query_string" : {
                    "query" : "some query string here"
                }
            },
            "filter" : {
                "term" : { "user" : "beer" }
            }
        }
    }
}
```

- adaptive replica selection

自动选择副本分片



### search


muiti index 


可以多个 index 查询
```
GET /twitter/_search?q=user:kimchy

GET /kimchy,elasticsearch/_search?q=tag:wow
```

### URL search

在 url 后面拼接参数

```
GET customer/_search?q=user:beer
```

更多参数例子：
https://www.elastic.co/guide/en/elasticsearch/reference/7.1/search-uri-request.html 


from 例子
```
GET customer/_search?from=3
```
### request body search

#### doc value fields 

https://www.elastic.co/guide/en/elasticsearch/reference/7.1/search-request-docvalue-fields.html#search-request-docvalue-fields

todo

#### explain

看执行计划 。。。

```
GET customer/_search
{
  "explain": true, 
  "query": {
    "term": {
      "user": {
        "value": "beer"
      }
    }
  }
}
```
#### field collapsing

https://elasticsearch.cn/article/132 

完美 讲的很清楚了


基本语法：

```
GET /customer/_search
{
    "query": {
        "match": {
            "message": "elasticsearch"
        }
    },
    "collapse" : {
        "field" : "user" 
    },
    "sort": ["likes"]
    "from": 10 
}
```

相当于：
```
select * from customer group by user order by likes limit 10,-1
```


也可以指定 top hits of number

```
GET /customer/_search
{
    "query": {
        "match": {
            "message": "elasticsearch"
        }
    },
    "collapse" : {
        "field" : "user", 
        "inner_hits": {
            "name": "the_latest_data", 
            "size": 5, 
            "sort": [{ "date": "desc" }] 
        },
        "max_concurrent_group_searches": 4 
    },
    "sort": ["likes"]
```

每组检索的并发请求数量：max_concurrent_group_searches

#### from/size 

```
GET customer/_search
{
    "from" : 0, "size" : 10,
    "query" : {
        "term" : { "user" : "kimchy" }
    }
}
```

#### highlighting 

```
GET customer/_search
{
  "query": {
    "match": {
      "user": "beer"
    }
  }, 
  "highlight": {
    "fields": {
      "user":{}
    }
  }
}
```

![](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/beer/blog/es_highlight.png
)


#### inner hits

#### min_score

Exclude documents which have a _score less than the minimum specified in min_score:

```
GET customer/_search
{
  
  "query": {
    "match": {
      "user": "beer"
    }
  },
  "min_score" : 0.02
}
```
#### post filter

对聚合之后的结果进行处理。

比如： 有个业务请求是 ： 取符合条件的公司的最新的数据，并且值要在有个区间内。

逻辑结构就是：
```
{
    "query": {},
    "aggs": { "top_hits":},
    "post_filter": {}
}
```

#### script fields


https://www.elastic.co/guide/en/elasticsearch/reference/7.1/search-request-script-fields.html

script_fields 来进行一些计算

```
POST customer/_search
{
  "query": {
    "match": {
      "counter": 1
    }
  },
  "script_fields": {
    "counter": {
      "script" : {
                "lang": "painless",
                "source": "doc['counter'].value * 100 + 1"
            }
    }
  }
}
```

#### search after

在分页的情况下，使用  search_after 来将上一个分页的结果的分割点传递到吓一个。（个人理解）

```
GET twitter/_search
{
    "size": 10,
    "query": {
        "match" : {
            "title" : "elasticsearch"
        }
    },
    "search_after": [1463538857, "654323"],
    "sort": [
        {"date": "asc"},
        {"tie_breaker_id": "asc"}
    ]
}
```

#### sort


重点是： "mode": "avg"。可选值：min,max,sum,avg,median
```
POST /_search
{
  "query": {
    "term": {
      "product": "chocolate"
    }
  },
  "sort": [
    {
      "price": {
        "order": "asc",
        "mode": "avg"
      }
    }
  ]
}
```

#### source filtering

查询结果的显示结果的设置。类似在 url 后面配置

```
GET /_search
{
    "_source": false,
    "query" : {
        "term" : { "user" : "kimchy" }
    }
}
```

#### track total hits

返回总命中数

![](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/beer/blog/es_track_total_hits.png
)

```
POST customer/_search
{
  "track_total_hits": true,
  "query": {
    "match_all": {
     
    }
  }
}
```
#### version

Returns a version for each search hit.

```
GET /_search
{
    "version": true,
    "query" : {
        "term" : { "user" : "kimchy" }
    }
}

```

### search template

nice  太棒了，这功能。这样的话，代码就简洁多了（java 里面）。





