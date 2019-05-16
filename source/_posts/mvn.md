---
title: mvn
date: 2019-05-09 21:49:27
tags: java
---

> maven 记录。想写什么就写什么，一块一块慢慢补！！！


官网：http://maven.apache.org/


### 是什么

Apache Maven is a software project management and comprehension tool. Based on the concept of a project object model (POM), Maven can manage a project's build, reporting and documentation from a central piece of information。


软件项目管理工具。pom( project object model)

### download and install 

http://maven.apache.org/download.cgi

http://maven.apache.org/install.html

<!--more-->
### 命令

我主要参考的是这个网址：

http://maven.apache.org/guides/getting-started/index.html

#### mvn compile 

编译源代码，生成 target 文件夹

#### mvn test

编译单元测试并且执行

#### mvn test-compile

只编译单元测试，不执行

#### mvn package

打 jar 包

#### mvn install 

安装到本地仓库

#### mvn clean

清空之前的编译文件

This will remove the target directory with all the build data before starting so that it is fresh.

#### mvn site  

生成项目的站点文件

可以自己执行一下，第一次时间比较长

![](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/beer/blog/mvn_site.png
)

