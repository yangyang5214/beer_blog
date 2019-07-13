---
title: java中优雅使用es
date: 2019-07-13 19:49:31
tags: es
---

> 记录一下。java 中使用 es. 比较好的方式（自我认为）。

### 之前

之前使用es。是拼接的 DSL.


调用之前，格式化数据。

```
 public JSONArray callEsForSearch(JSONObject requestData, String index, String type) {
     Objects.requireNonNull(index, "es index null");
     Objects.requireNonNull(type, "es type null");
     String endPoint = "/" + index + "/" + type + "/_search?filter_path=hits.hits._source,hits.hits._score";
     JSONObject resultJosn = callEs(endPoint, requestData);
     JSONObject hitsObj = resultJosn.getJSONObject("hits");
     if (hitsObj == null) {
         return new JSONArray();
     }
     return hitsObj.getJSONArray("hits");
 }

```

<!--more-->

调用 es 核心方法

```
public JSONObject callEs(String endPoint, JSONObject requestData) {
    NStringEntity stringEntity = new NStringEntity(JSON.toJSONString(requestData), ContentType.APPLICATION_JSON);
    Response response;
    try {
        response = restClient.performRequest("GET", endPoint, Collections.emptyMap(), stringEntity);
    } catch (IOException e) {
        throw new ElasticsearchException(e);
    }
    HttpEntity entity = response.getEntity();
    JSONObject jsonObject;
    try {
        jsonObject = JSON.parseObject(EntityUtils.toString(entity));
    } catch (IOException e) {
        throw new RuntimeException(e.getMessage());
    }
    return jsonObject;
} 
```

但是：requestData 是代码拼接的，很恶心。。。节选一段，体会一下！！！

```
//part 1
String aggs = "{\"company_id\":{\"terms\":{\"field\":\"company_id\",\"size\":" + EsConstant.MAX_SIZE + "}," +
        "\"aggs\":{\"top\":{\"top_hits\":{\"sort\":[{\"file_date\":{\"order\":\"desc\"}}]," +
        "\"_source\":[\"" + metric + "\",\"company_id\"],\"size\":1}}}}}";

//part 2
 condition.append("{\"bool\":{\"filter\":{\"exists\":{\"field\":\"" + metric + "\"}},");
 condition.append("\"must\":[");
 if (!Constant.FH.equals(periodType) && !Constant.FQ.equals(periodType)) {
     condition.append("{\"match\":{\"period_type\":\"").append(periodType).append("\"}},");
 }
 if (companyId != null) {
     condition.append("{\"match\":{\"company_id\":\"")
             .append(companyId)
             .append("\"}},");
 }
 condition.append("{\"match\":{\"" + periodType + "_relatively" + "\":\"" + relativelyYear + "\"}}")
         .append("]}}");        
```

之前是祖传的代码，然后修改的。。。

有几个问题：

- 明明我都把 sql 写出来了，还要根据不同条件拆分，然后组合 sql
- 在代码拼接的过程中，各种 sql 格式错误

### 现在

https://www.elastic.co/guide/en/elasticsearch/reference/6.8/search-template.html

看到 es 有模板功能。

最简单的例子：

```
# 7.0 版本
GET _search/template
{
    "source" : {
      "query": { "match" : { "{{my_field}}" : "{{my_value}}" } },
      "size" : "{{my_size}}"
    },
    "params" : {
        "my_field" : "message",
        "my_value" : "some message",
        "my_size" : 5
    }
}
```

这样代码写起来就很干净了。

传入参数：template(source 内容) and params（params 内容）. 

```
 public JSONArray queryByTemplate(String index, String type, String template, JSONObject params) {
     String localEndPoint = MessageFormat.format(END_POINT, index, type);
     NStringEntity stringEntity = queryByTemplateCheck(index, type, template, params);
     try {
         Response response = restClient.performRequest("get", localEndPoint, Collections.emptyMap(), stringEntity);
         HttpEntity entity = response.getEntity();
         JSONObject jsonObject = JSON.parseObject(EntityUtils.toString(entity));
         JSONObject hitsObj = jsonObject.getJSONObject("hits");
         return hitsObj == null ? new JSONArray() : hitsObj.getJSONArray("hits");
     } catch (IOException e) {
         log.error(e.getMessage());
         throw new ElasticsearchException("调用 es 异常");
     }
 }
```

注意 es 版本问题：

![es 5.0版本 search-template语法](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/20190713200554_817acc456cb4996bcf612a223c69b457.png)


![es 7.0版本 search-template语法](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/20190713200653_a4236046ed787d63b3e2efcd2a30efb4.png)

更改 url 的版本号，即可找到和自己版本对应的语法。

springboot 添加配置文件（application-es.yml）。然后 include 进来。

例子：注意 单引号包起来。（好像是因为 &#123;&#123;&#125;&#125; 的问题）

```
es:
  screen:
    no_range_daily: '{"size": "{{size}}",
                      "_source": "{{field}}",
                      "query":{"bool":{
                                "filter":{"exists":{"field":"{{field}}"}},
                                "must":[{"term":{"trading_date":{"value":"{{trading_date}}"}}}]}},
                                "sort":[{"{{field}}":{"order":"{{order}}","unmapped_type":"double"}}]
                      }'
```
