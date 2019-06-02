---
title: es-aggs-api
date: 2019-05-26 21:35:54
tags: elasticsearch
---


> es 聚合的api.

- 准备数据：准备城市表的数据

![](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/beer/blog/city.png
)

- 推送到 es.

![](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/beer/blog/es_city_search.png
)


- java 代码

简单的将 city 表 推送到 es

```
@Autowired
private CityMapper cityMapper;

@Autowired
private RestClient restClient;

public void pushCityToEs(){
    List<JSONObject> cityList = cityMapper.getAllCity();
    for (JSONObject jsonObject : cityList) {
        jsonObject.put("price", Math.random()*100);
        String id = jsonObject.getString("id");
        Request request = new Request("put","/city/_doc/" + id);
        request.setJsonEntity(jsonObject.toJSONString());
        try {
            restClient.performRequest(request);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

<!--more-->

### metrics aggregations  聚合指标


#### avg


"size": 0 表示 只要聚合的结果

对 price 求平均值。

script 里面表示 _value * correction 。

```
POST /city/_search
{
  "size": 0,
  "query": {
    "match_all": {}
  },
  "aggs": {
    "avg_price": {
      "avg": {
        "field": "price",
        "script": {
          "lang": "painless",
          "source": "_value * params.correction",
          "params": {
            "correction": 1.2
          }
        }
      }
    }
  }
}
```

The missing parameter defines how documents that are missing a value should be treated. By default they will be ignored but it is also possible to treat them as if they had a value.

es 官网的例子

```
POST /exams/_search?size=0
{
    "aggs" : {
        "grade_avg" : {
            "avg" : {
                "field" : "grade",
                "missing": 10 
            }
        }
    }
}
```

#### weighted avg

todo  

加权平均值，但是感觉不一样。后期更新。

#### cardinality 

基数统计。[但是不准确](https://www.elastic.co/guide/en/elasticsearch/reference/7.1/search-aggregations-metrics-cardinality-aggregation.html
)。

![](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/beer/blog/es_cardinality.png
)

```
SELECT parent_id
from city
GROUP BY parent_id
```

#### extend stats

A multi-value metrics aggregation  多值度量聚合

![](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/beer/blog/es_extend_status.png
)

#### geo bounds

https://www.elastic.co/guide/en/elasticsearch/reference/7.1/search-aggregations-metrics-geobounds-aggregation.html

