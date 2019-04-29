---
title: elasticsearchApiLearn
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
#### delete an index 
```
delete /customer
```