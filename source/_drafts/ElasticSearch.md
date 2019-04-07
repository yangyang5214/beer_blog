---
title: ElasticSearch
date: 2019-03-13 22:08:19
tags: java
---


### 功能

- 分布式的搜索引擎和数据分析引擎
- 全文检索，结构化检索，数据分析
- 对海量数据进行近实时的处理

### 核心概念

- Near Realtime (NRT):近实时。
```
1. 从写入到读取妙级∂
2. 基于es搜索和分析可以达到妙级
```

- Cluster 集群
```
包含多个节点
```
- Node 节点
```
```
- Document 文档
```
es中最小的数据单元,一个document里面有多个 field , 每个 field 就是一个数据字段
```
- index 索引
```
一个 index 可以放多个 type , 每个 type 又是 document 
```

- shard
```
节点
```

- replica
```
备份节点
```

**对比**
| ElasticSearch| Mysql |
| :--: | :--:|
|  document    |   行 |
|  type    |    表 |
|   index   |   数据库  |


### 安装(mac)

[download](https://www.elastic.co/cn/products/)

#### elasticsearch

- 启动

./bin/elasticsearch


- 访问： http://localhost:9200/

```
{
    "name": "Y9IFxsQ",
    "cluster_name": "elasticsearch",
    "cluster_uuid": "Nvo4QqtYQ1KI49W4Udnw_A",
    "version": {
        "number": "6.6.2",
        "build_flavor": "default",
        "build_type": "tar",
        "build_hash": "3bd3e59",
        "build_date": "2019-03-06T15:16:26.864148Z",
        "build_snapshot": false,
        "lucene_version": "7.6.0",
        "minimum_wire_compatibility_version": "5.6.0",
        "minimum_index_compatibility_version": "5.0.0"
    },
    "tagline": "You Know, for Search"
}
```
#### kibana

- 启动
```
./bin/kibana
```
- 访问
```
http://localhost:5601/
```

![](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/beer/blog/kibana_dev_tool.png
)
```
GET _cluster/health

{
  "cluster_name" : "elasticsearch",
  "status" : "green",
  "timed_out" : false,
  "number_of_nodes" : 1,
  "number_of_data_nodes" : 1,
  "active_primary_shards" : 1,
  "active_shards" : 1,
  "relocating_shards" : 0,
  "initializing_shards" : 0,
  "unassigned_shards" : 0,
  "delayed_unassigned_shards" : 0,
  "number_of_pending_tasks" : 0,
  "number_of_in_flight_fetch" : 0,
  "task_max_waiting_in_queue_millis" : 0,
  "active_shards_percent_as_number" : 100.0
}

```
### 简单的集群管理

- 快速检查集群的健康状态


GET _cat/health

三种状态：

- green : 每个索引的 primary shard and replica shard 都是 active 状态的
- yellow : 每个索引的 primary shard  active 状态，但是部分 replica shard 不是  active 状态，处于不可用状态
- red : 不是所有的 primary shard 都是 active 状态，部分索引丢失

- 快速查看集群中有哪些索引

```
# 查看索引
GET _cat/indices?v


health status index     uuid                   pri rep docs.count docs.deleted store.size pri.store.size
green  open   .kibana_1 bk7ZlGoNSPaz5niWUWnwqA   1   0          3            0     11.9kb         11.9kb


# 创建索引
PUT /test_index/

health status index      uuid                   pri rep docs.count docs.deleted store.size pri.store.size
green  open   .kibana_1  bk7ZlGoNSPaz5niWUWnwqA   1   0          3            0     11.9kb         11.9kb
yellow open   test_index 6D1twlQ8RWWPMmFMe_QEEg   5   1          0            0      1.1kb          1.1kb


# 删除索引
delete /test_index/
```

### 电商项目商品管理案例


#### CRUB

- 新增商品，新增文档，建立索引

格式：PUT /index/type/1
```
# 插入几条数据

PUT /beer/travel/1
{
    "name": "shanghai",
    "date": "2019"
}

PUT /beer/travel/2
{
    "name": "daqing",
    "date": "2013"
}

PUT /beer/travel/3
{
    "name": "nanjing",
    "date": "2017"
}
```

- 查询商品
```
get /beer/travel/1
```
- 替换
```
相当于执行一遍 put
```
- 修改
POST /index/type/id{ "doc":{}}

```
POST /beer/travel/1/_update
{
      "doc": { "date": "2019-1"}
}
```
-  删除

```
delete /index/type/id
```
#### 搜索方式

- query string search
```
# 查询所有
get /beer/travel/_search

# 查询 name=shanghai
get /beer/travel/_search?q=name:shanghai
```
- query Domain Specified Language (DSL)
```
# 查询所有数据
get /beer/travel/_search
{
  "query":{
    "match_all": {}
  }
}

# 分页
get /beer/travel/_search
{
  "query":{
    "match_all": {}
  },
  "from": 0,
  "size": 2
}

# 查询特定字段(只查询 name )
get /beer/travel/_search
{
  "query":{
    "match_all": {}
  },
  "_source": ["name"]
}
```
- query filter
```
get /beer/travel/_search
{
  "query":{
    "bool": {
      "must": [
        {
          "match": {
            "name": "shanghai"
          }
        }
      ],
      "filter": {
        "range": {
          "date": {
            "gte": 2018,
            "lte": 2010
          }
        }
      }
    }
  }
}
```
- full-text search (全文检索)
```
get /beer/travel/_search
{
  "query": {
    "match": {
      "name": "shanghai"
    }
  }
}
```

- phrase search (短语搜索)

完全匹配
```
get /beer/travel/_search
{
  "query": {
    "match_phrase": {
      "name": "shanghai"
    }
  }
}
```

- highlight search(高亮搜索结果)

```
get /beer/travel/_search
{
  "query": {
    "match_phrase": {
      "name": "shanghai"
    }
  },
  "highlight":{
    "fields": {
      "date": {}
    }
  }
}
```
#### 聚合


Fielddata is disabled on text fields by default. Set fielddata=true on [name] in order to load fielddata in memory by uninverting the inverted index. Note that this can however use significant memory

理解：默认 text 上是
```
get /beer/travel/_search
{
  "aggs": {
    "group_by_name": {
      "terms": {
        "field": "name",
        "size": 10
      }
    }
  }
}
```

设置：fielddata: true

```
PUT beer/_mapping/travel/
{
  "properties": {
    "name": { 
      "type":     "text",
      "fielddata": true
    }
  }
}
```
### 基础分布式架构

- ElasticSearch 是一套分布式系统，

- 垂直扩容和水平扩容

- 增减或减少节点时的数据 rebalance

- master 节点
```
创建或删除索引
增加或删除节点
```
- 节点平等的分布式架构
```
1、节点对等，每个节点都能接受所有的请求
2、自动请求路由
3、相应收集
```

### shard and replica 机制

```
1、一个index包含一个或者多个 shard . 
2、每个 shard 都是一个最小工作单元，承载部分数据，lucene 实例，完整的建立索引和处理请求的能力
3、增减节点时，shard 会自动 node 负载均衡
```
### 元数据

都直接类比  mysql 理解吧

- _index
- _type
- _id

### document_id

#### 手动指定 document_id 

一般来说，是从其他系统导入进来的时候，进行设置。

#### 自动生成 document_id 

```
POST /index/id

自动生成 document_id（20位） GUID(全局唯一标示)
```

### 并发冲突的问题

#### 悲观锁

#### 乐观锁

基于 _version 元数据，实现乐观锁

分布式的 shard => replica ,去多线程更新时，如果  version 大的先到的，先修改，后到的 version 小的直接舍弃。


### partial update 
```
POST /index/type/id?_update
{
  "doc":{
      //需要 update 的字段
  }
}
```
1、所有的查询、修改 and 写回操作，都发生在es的一个 shard 内部，避免了网路传输的开销
2、减少了查询和修改的时间间隔，有效减少并发冲突的情况

### 批量操作

#### _mget
```
POST /_mget
{
  "docs":{
     "_index": index,
     "_type": type,
     "_id": 1
  },
  "docs":{
  "_index": index,
     "_type": type,
     "_id": 2
  },
}
```

```
# 同一个index
POST /index/_mget
{
  "docs":{
     "_type": type,
     "_id": 1
  },
  "docs":{
     "_type": type,
     "_id": 2
  },
}
```

#### _bulk