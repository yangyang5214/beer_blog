---
title: scrapy
date: 2020-11-07 18:34:35
tags: [python,scrapy]
---

> 简单做个总结吧


### 介绍

一款爬虫利器

### 组成

很优秀的把爬虫的几个部分按照模块化拆分。

- 请求
- 下载
- 存储

图片来自官网

![](https://beef-1256523277.cos.ap-chengdu.myqcloud.com/bed/20201107184902.png)

涉及到的名词

![](https://beef-1256523277.cos.ap-chengdu.myqcloud.com/bed/20201107185046.png)


### spider

给定 url, 一系列的逻辑方法，最后输出 items

- input 

给定  start_urls，然后开始 请求

或者自己重写 start_requests 方法, 定义好 callback ,然后就可以 在 parse 方法取到 response

```
def start_requests(self):
    url = ''
     yield self.scrapy_request(url=url, callback=self.parse)
```

- process

在 self.scrapy_request 执行后的 callback 函数,获取到 response 之后，写一些逻辑代码。

可以 无限套娃，继续 yield scrapy_request

- output

直到 不继续 yield , 或者方法 返回 item, 结束

有的爬虫不需要返回 item, 纯粹是为了 调 api.

### middleware

中间件。

可以定义很多的 middleware。例如： 加代理。源码中会按照权重执行 middleware，直到返回 response.

 









