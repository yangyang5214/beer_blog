---
title: es_status_red_to_green
tags:
  - elasticsearch
  - java
date: 2019-06-25 11:30:20
---



参考： http://www.jwsblog.com/archives/59.html


![_cluster/health](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/20190625113029_a62c7a25bc359c54b25b637ce2ac764c.png)



![_cluster/health?level=indices](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/20190625113110_eb2001da3d8166742b9202de866ed396.png)



UNASSIGNED

![_cat/shards](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/20190625113334_e186da5acb393877f2089c258b9cdf0f.png)

![Unknown AllocationCommand [allocate]](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/20190625122244_4c331dd0bf450da197035f18fabfdee6.png)

![allocate_empty_primary](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/20190625122343_337c233e119c7666f3647384c980f131.png)


https://www.elastic.co/guide/en/elasticsearch/reference/5.6/breaking_50_allocation.html

The reroute command allocate has been split into two distinct commands allocate_replica and allocate_empty_primary. This was done as we introduced a new allocate_stale_primary command. The new allocate_replica command corresponds to the old allocate command with allow_primary set to false. The new allocate_empty_primary command corresponds to the old allocate command with allow_primary set to true.


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
![success](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/20190625122614_536ca9a14d01928e055b22b33ba93005.png)


但是：

![节点磁盘使用率](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/20190625123728_40357013ac5b38d7e528256f0e1edc3a.png)



