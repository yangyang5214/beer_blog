---
title: timeout
date: 2019-04-27 20:01:23
tags: java
---


> 关于 setSocketTimeout setConnectTimeout 的意义


### 构造 url


注意：让主线程休眠一段时间。

```
@RestController
public class HttpController {


    @GetMapping("/timeout/test")
    public String sockerTimeout(){

        //休眠 5 秒，模拟 socket timeout
        try {
            Thread.sleep(5000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        return "hello beer!";
    }

}
```

<!--more-->

### 设置请求
```
@Test
public void httpTimeoutTest() throws IOException, URISyntaxException {
    final RequestConfig requestConfig =  RequestConfig.custom()
            .setConnectTimeout(50000)
            .setSocketTimeout(3000)
            .build();
    HttpClient httpClient = HttpClientBuilder.create()
            .setDefaultRequestConfig(requestConfig)
            .build();
    HttpGet httpGet = new HttpGet();
    httpGet.setURI(new URI("http://127.0.0.1:8080/timeout/test"));
    HttpResponse response = httpClient.execute(httpGet);
    String responseString = EntityUtils.toString(response.getEntity());
    System.out.println(responseString);
}
```

- 先设置 setSocketTimeout(3000)

模拟请求，报 SocketTimeoutException 异常

```
java.net.SocketTimeoutException: Read timed out

```

- 再设置 setSocketTimeout(10000)

返回正常结果

```

hello beer!

```


### 总结

- setConnectTimeout

请求的第一步，建立连接，如果连接超时，则 SocketTimeoutException。
```
 * Sets a specified timeout value, in milliseconds, to be used
 * when opening a communications link to the resource referenced
 * by this URLConnection.  If the timeout expires before the
 * connection can be established, a
 * java.net.SocketTimeoutException is raised. A timeout of zero is
 * interpreted as an infinite timeout.
```

- setReadTimeout

设置读数据的超时时间，如果超时，则 SocketTimeoutException

```
* Sets the read timeout to a specified timeout, in
* milliseconds. A non-zero value specifies the timeout when
* reading from Input stream when a connection is established to a
* resource. If the timeout expires before there is data available
* for read, a java.net.SocketTimeoutException is raised. A
* timeout of zero is interpreted as an infinite timeout.
```
- setSocketTimeout

查询资料无果，暂时理解和 setReadTimeout 一样。

