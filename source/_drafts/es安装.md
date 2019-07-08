---
title: es安装
tags: es
---

### 安装

https://www.elastic.co/cn/downloads/

### 启动

```
bin/elasticsearch
```

### 插件
#### 查看插件

```
bin/elasticsearch-plugin list
```

```
localhost:9200/_cat/plugins
```

#### 安装插件

```
bin/elasticsearch-plugin install analysis-icu
```

```
192:elasticsearch-7.0.0 beer$ bin/elasticsearch-plugin list
analysis-icu
```
### 单机启动集群

```
bin/elasticsearch -E node.name=node1 -E cluster.name=beer -E path.data=node1_data -d
bin/elasticsearch -E node.name=node2 -E cluster.name=beer -E path.data=node2_data -d
bin/elasticsearch -E node.name=node3 -E cluster.name=beer -E path.data=node3_data -d
```

```
localhost:9200/_cat/nodes
```
