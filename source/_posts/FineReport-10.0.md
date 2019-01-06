---
title: FineReport
tags: [FineReport,工具]
date:  2019-01-06 16:00:00
categories: 工具
---


> 最早接触 finereport 是在实习期间，[csdn](https://blog.csdn.net/qq_30009669/article/details/54710492#reply)，好久不用csdn，后面发现好多人在看，也有评论，最近有时间看了一下，FineReport的版本都到 10.0 了，而且去掉了 resources 文件夹。之前也的也不适用了，所以，研究了一下，记录一下。


> 如果有问题，直接联系我，希望来撩，如果能给我推荐工作更好，目标城市：上海，17毕业。


<!--more-->

### 之前的版本集成


> 这种方式适用当时的版本，2017.01.24 晚上写与上海一个不朝阳的小房间(实习期)。

[csdn FineReport 集成到web项目 ](https://blog.csdn.net/qq_30009669/article/details/54710492)



### 目前版本（10.0）

> 思路，不管是哪个版本的 finereport , 如果你仔细研究他的目录结构，其实和 tomcat 大同小异。

![](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/blog/20190106162908.png)

具体每个包代表什么含义 ，官网有解释

[WEB-INF下文件夹说明](http://help.finereport.com/doc-view-1912.html)

核心就是这三个

### 直接集成 tomcat 
![](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/blog/20190106163352.png)

直接将    finereport 下的文件夹全部拷贝过来（当然，如果自己能分得清的，比如：treasures、config 包不需要）。reportlets  embed  lib 这三个包肯定是必须的。

[lib 需要引入 tools.jar](http://help.finereport.com/doc-view-2475.html)


运行  tomcat , 访问方式： http://localhost:8080/finereport/decision/view/report?viewlet=city.cpt

city.cpt 是模板文件 (模板文件，可以见下面 github 链接)

效果图：

![](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/blog/20190106163845.png)

### 集成到项目中

> 如果你是要集成整个 决策系统的话，直接按照上面的操作，全部添加进去就好了（需要删什么东西，自己对照每个文件夹的含义斟酌）。


下面说一下，集成单个页面到系统中。下面是单个页面涉及到的东西。既然是单个页面，当然是最简化的东西，finereport自带的很多东西都忽略掉。

- reportlets 必须。放模板文件
- lib 必须,包依赖 （如果pom文件添加了，则非必须，后面讲到）
- embed 必须。自定义的数据连接（ps:自己试出来的）


#### embed 文件夹

之前版本的（不知道哪个版本，2017年），有个 resources 文件夹， 下有个 datasource.xml文件，显示的指定了要连接的数据库。现在 10.0 版本没这个文件了，看到 embed 文件夹下的 这几个文件，db.script and db.lck 有点可疑（其他三个文件显而易见，知道是什么东西）。我 直接删除 embed 文件夹，finereport 的数据集会消失，所以重新配置数据集，之后这几个文件，自动生成。而且 db.script 文件只有 2 kb,就是你配置的数据集。如果你闲麻烦，那就直接原生的全部copy进来，也不会报错，只是多加载了 finereport 默认的数据集。

![](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/blog/20190106195258.png)


#### springboot 集成

目录结构

![](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/blog/20190106200717.png)

- 新建 springboot 项目
- 增加 webapp 目录
- 排除自身 tomcat 依赖
- 增加 javax.servlet 模块


pom 文件

```
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
            <exclusions>
                <exclusion>
                    <groupId>org.springframework.boot</groupId>
                    <artifactId>spring-boot-starter-tomcat</artifactId>
                </exclusion>
            </exclusions>
        </dependency>

        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>3.1.0</version>
            <scope>provided</scope>
        </dependency>
```

**数据集**

一开始自定义数据集的时候没指定时区，报错了。加上时区

![](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/blog/20190106201638.png)


**效果图**

访问路径：http://localhost:8090/decision/view/report?viewlet=city.cpt （ps:访问路径也变化了，之前需要配置 servlet，现在什么也不需要，web.xml 文件都省了）

![](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/blog/20190106202046.png)

[gtihub finereport_10](https://github.com/yangyang5214/finereport/tree/master/finereport_10)


如果想直接运行我的模板，需要自己定义数据集（mysql 连接，替换 embed 文件家）. city 表在 finereport_10\src\main\resources\db 目录下。


### jar包  pom 文件引入

> maven 管理项目，所以没必要建立lib目录。将 fr 的包放在maven本地仓库即可，然后pom 文件引入

后续更新。。。。太晚了，撤了

