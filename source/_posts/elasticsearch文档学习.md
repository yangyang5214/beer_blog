---
title: elasticsearch文档学习
date: 2019-04-22 22:23:29
tags: elasticsearch,java
---

> 公司之前使用的是 transclient 调用，结果总是丢数据。搞得头大。学习一下 [restClient官方文档](https://www.elastic.co/guide/en/elasticsearch/client/java-rest/current/java-rest-high-supported-apis.html)。


学习代码：https://github.com/yangyang5214/elasticsearch

基于版本 7.0。只是个人整理，建议直接看官网。我只是纯粹为了学英语，哈哈哈！！！

<!--more-->

## [RestClient](https://www.elastic.co/guide/en/elasticsearch/client/java-rest/current/java-rest-low.html)



### 依赖

```
<dependency>
    <groupId>org.elasticsearch.client</groupId>
    <artifactId>elasticsearch-rest-client</artifactId>
    <version>7.0.0</version>
</dependency>
```
### 初始化

设置 host port username password

```
@Bean
public RestClient getRestClient(){
    final CredentialsProvider credentialsProvider = new BasicCredentialsProvider();
    credentialsProvider.setCredentials(AuthScope.ANY,
            new UsernamePasswordCredentials(username, password));
    RestClient restClient = RestClient.builder(
            new HttpHost(host, port, "http"))
            .setHttpClientConfigCallback(httpClientBuilder -> httpClientBuilder.setDefaultCredentialsProvider(credentialsProvider))
            .build();
    return restClient;
}
```


### Performing requests

Once the RestClient has been created, requests can be sent by calling either performRequest or performRequestAsync. performRequest is synchronous and will block the calling thread and return the Response when the request is successful or throw an exception if it fails. performRequestAsync is asynchronous and accepts a ResponseListener argument that it calls with a Response when the request is successful or with an Exception if it4 fails.

RestClient，被创建之后，可以调用 performRequest（同步） 或者 performRequestAsync（异步）。

#### 创建 Request
```
Request request = new Request("get",/index/type/_search);
```

#### 添加参数

https://www.elastic.co/guide/en/beats/winlogbeat/7.x/configuration-output-codec.html

```
request.addParameter("pretty","true");
```

#### 设置 entity
```
JSONObject jsonObject = new JSONObject();
jsonObject.put("size","1");
jsonObject.put("_source", Arrays.asList("id,name"));
request.setEntity(new NStringEntity(JSON.toJSONString(jsonObject)));

//或者直接设置 json
request.setJsonEntity(JSON.toJSONString(jsonObject));
```

setJsonEntity 的源码，其实还是调用的 NStringEntity 

```
 public void setJsonEntity(String entity) {
     setEntity(entity == null ? null : new NStringEntity(entity, ContentType.APPLICATION_JSON));
 }
```

#### RequestOptions

我实际使用确实，没有设置过 RequestOptions，不过创建 singleton instance RequestOptions 确实不错。

```
private static final RequestOptions COMMON_OPTIONS;

static {
    RequestOptions.Builder builder = RequestOptions.DEFAULT.toBuilder();
    builder.addHeader("Authorization", "TOKEN");
    builder.setHttpAsyncResponseConsumerFactory(
            new HttpAsyncResponseConsumerFactory
                    .HeapBufferedResponseConsumerFactory(30 * 1024 * 1024 * 1024));
    COMMON_OPTIONS = builder.build();    
}

request.setOptions(COMMON_OPTIONS);
```

#### 并发执行

使用 CountDownLatch ，performRequestAsync 异步回调。执行一个请求有返回之后，调用 countDown，数量减 1，执行 await 方法等待子线程执行完毕。


当然，直接可以使用  bulk 的 API, 进行 批量操作。
```
public void multiple() throws InterruptedException {
    List<JSONObject> documents = new ArrayList<>();
    final CountDownLatch latch = new CountDownLatch(documents.size());
    for (int i = 0; i < documents.size(); i++) {
        Request request = new Request("PUT", "/posts/doc/" + i);
        request.setJsonEntity(JSON.toJSONString(documents.get(i)));
        restClient.performRequestAsync(
                request,
                new ResponseListener() {
                    @Override
                    public void onSuccess(Response response) {
                        latch.countDown();
                    }
                    @Override
                    public void onFailure(Exception exception) {
                        latch.countDown();
                    }
                }
        );
    }
    latch.await();
}
```


### Reading responses

重点的就是 下面两个吧，responseBody 转为 jsonObject 取数据

```
//返回状态
int statusCode = response.getStatusLine().getStatusCode();
//返回的结果集
String responseBody = EntityUtils.toString(response.getEntity());
```
