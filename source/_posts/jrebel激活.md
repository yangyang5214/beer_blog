---
title: jrebel激活
date: 2019-07-10 21:52:40
tags: tools
---

> jrebel  激活 update 2019 。每次都去网上找激活码，找过来找过去就是那几个，有时候还失效。允悲 /苦瓜脸


参考：[JRebel 2018.1使用反代失败解决](http://blog.lanyus.com/archives/337.html) and  [撸了个反代工具, 可用于激活JRebel](http://blog.lanyus.com/archives/317.html) 。自己可以参考搭建一个！


友情提示：http://xxx.com 的网站别点。千万别点！！！


### 结果

1. 获取激活码

https://tools.hexianwei.com/#/tools/jrebel

直接在页面点击 获取激活码。

![获取激活码](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/20190710220238_7d8c780a08e88ccc97ff10c307f047ad.png)

2. 激活

![jrebel激活步骤](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/20190710220126_5e800a29cd9de3d0f6e000d1914b017a.png)

<!--more-->

### 步骤

参考上面的两篇博客。我们知道 url 的格式为： http://{url}/{uuid}

1. 配置反向代理

![反向代理设置](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/20190710215906_a6748b89eb58b67b1ebb8df5cde4ad31.png)


所以：我们配置 nginx 的反向代理。

```
server {
    listen 443 ssl;
    server_name jrebel.hexianwei.com;

    ssl on;
    ssl_certificate /etc/nginx/ssl/fullchain.cer;
    ssl_certificate_key /etc/nginx/ssl/hexianwei.key;
    ssl_session_timeout 5m;

        location / {
                proxy_pass http://idea.lanyus.com:80;
        }
}

server{
        listen 80;
        server_name jrebel.hexianwei.com;
         return 301 https://jrebel.hexianwei.com$request_uri;

}

```
2. 返回 url + uuid 的 url

```
public String getJrebelLicense() {
    String uuid = UUID.randomUUID().toString();
    return jrebelUrl + uuid;
}
```

3. 页面请求，展示
