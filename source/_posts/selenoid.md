---
title: selenoid
date: 2021-03-04 20:08:43
tags: [python, tools，树莓派]
---

[官方 github](https://github.com/aerokube/selenoid)

> Selenoid is a powerful implementation of Selenium hub using Docker containers to launch browsers.

### install 

https://aerokube.com/selenoid/latest/

https://aerokube.com/selenoid-ui/latest/


<!--more-->

### docker-compose

[docker-compose.yaml](https://github.com/yangyang5214/docker-compose-pi/blob/main/selenoid/docker-compose.yaml)

[browsers.json](https://github.com/yangyang5214/docker-compose-pi/blob/main/selenoid/data/browsers.json)

[需要自己下载 browsers.json 定义的 image](https://github.com/aerokube/selenoid/issues/456)  (我也是搞了好久才发现。。。)
 
```
cat browsers.json | jq . 

{
  "chrome": {
    "default": "80.0",
    "versions": {
      "80.0": {
        "image": "selenoid/vnc:chrome_80.0",
        "port": "4444"
      }
    }
  },
  "firefox": {
    "default": "74.0",
    "versions": {
      "74.0": {
        "image": "selenoid/vnc:firefox_74.0",
        "port": "4444"
      }
    }
  }
}


docker pull selenoid/vnc:chrome_80.0
docker pull selenoid/vnc:firefox_74.0
```

```docker
cd selenoid
docker-compoes up
```

### 使用

http://localhost:4443/  docker-compose 配置的 selenoid-ui 的映射端口 

![](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/blog/20210305081542.png)

```python
# -*- coding: utf-8 -*-
from selenium import webdriver

capabilities = {
    "browserName": "chrome",
    "browserVersion": "80.0",
    "selenoid:options": {
        "enableVNC": True,
        "enableVideo": False
    }
}

driver = webdriver.Remote(
    command_executor="http://127.0.0.1:4444/wd/hub",
    desired_capabilities=capabilities)


driver.get('https:/www.hexianwei.com')

site_subtitle = driver.find_element_by_class_name("site-subtitle")
print site_subtitle.text

driver.close()
```

### 树莓派

参考：https://aerokube.com/selenoid/latest/#_using_selenoid_without_docker

目前，我也没集成好树莓派

- [build selenoid for arm](https://aerokube.com/selenoid/latest/#_contributing_development)

```go
cd selenoid
GOARCH=arm go build
```

- 绑定 chromium

browsers.json 
```json
{
  "chrome": {
    "default": "86.0",
    "versions": {
      "86.0": {
        "image": "/usr/bin/chromedriver"
      }
    }
  }
}
```
- 启动
```shell
cd selenoid
./selenoid -conf ./browsers.json -disable-docker
```

可惜一直绑定不了。。。

可以关注下。。。

[Raspberry Pi support ? #875](https://github.com/aerokube/selenoid/issues/875)




