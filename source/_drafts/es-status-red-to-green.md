---
title: es_status_red_to_green
date: 2019-06-25 11:30:20
tags: [elasticsearch,java]
---


参考： http://www.jwsblog.com/archives/59.html

> 记一次 es 状态 由 red 变为 green. 出现的原因是：代码出错了。然后推送了大量的数据到es,导致节点 red. 搜索很多解决方法，网上的 99% 都是 删除  index . 呵呵！！！删除了怎么用，本来是数据不全，一下子变成了数据没有。。。况且，重新推送需要很久（小公司，资源有限，每次推送都要等待 es 消化。。。）。主要参考了上面的那篇文章。


red 的原因：不是所有的主分片都可用。通常时由于某个索引的住分片为分片unassigned，只要找出这个索引的分片，手工分配即可。（reroute）

### 查看状态

#### 查看集群的情况

```
GET _cluster/health 
```

![_cluster/health](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/20190625113029_a62c7a25bc359c54b25b637ce2ac764c.png)

UNASSIGNED_SHARDS: 4

#### 查看索引级别的

```
GET _cluster/health?level=indices
```
![_cluster/health?level=indices](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/20190625113110_eb2001da3d8166742b9202de866ed396.png)

可以看到：dailyinfo_hub  index 的 UNASSIGNED_SHARDS: 2（另外两个是 ： financialinfo_hub ）

#### 查看分片状态

```
GET _cat/shards
```

![_cat/shards](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/20190625113334_e186da5acb393877f2089c258b9cdf0f.png)

可以看到： dailyhub_info 的两个节点的状态为：UNASSIGNED (找出两个分片的 id)

<!--more-->

### reroute

官方文档： 

https://www.elastic.co/guide/en/elasticsearch/reference/5.0/cluster-reroute.html

可能，我处理的也不一定对，具体可以参考官方文档每个操作的使用场景，对号入座使用。

![Unknown AllocationCommand [allocate]](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/20190625122244_4c331dd0bf450da197035f18fabfdee6.png)

![allocate_empty_primary](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/20190625122343_337c233e119c7666f3647384c980f131.png)


上面是另个错误的 sql. 一定去找对应版本的 sql.

5.6版本的es: https://www.elastic.co/guide/en/elasticsearch/reference/5.6/breaking_50_allocation.html

```
The reroute command allocate has been split into two distinct commands allocate_replica and allocate_empty_primary. This was done as we introduced a new allocate_stale_primary command. The new allocate_replica command corresponds to the old allocate command with allow_primary set to false. The new allocate_empty_primary command corresponds to the old allocate command with allow_primary set to true.
```

```
POST /_cluster/reroute
{
  "commands": [
    {
      "allocate_empty_primary": {
        "index": "financialinfo_hub",
        "shard": 1,
        "node": "82kRgwG",
        "accept_data_loss": true
      }
    }
  ]
}
```
### success

![success](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/20190625122614_536ca9a14d01928e055b22b33ba93005.png)

### 重推

但是：

![节点磁盘使用率](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/20190625123728_40357013ac5b38d7e528256f0e1edc3a.png)

突然下降好多，然后，再看一下历史的磁盘使用数据。明显是丢失了一部分数据，但是，es 可用。应急的办法是推送近五年的数据(再往前使用频率不多)。近五年的数据推送完成之后。然后再一次性推送剩下的数据（update）.





