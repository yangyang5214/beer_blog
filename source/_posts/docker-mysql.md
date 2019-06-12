---
title: docker-mysql
date: 2019-06-11 14:27:23
tags: [docker,mysql]
---

> docker 安装 mysql 简单步骤

### install docker
```
apt install docker-ce
```

### pull mysql
```
sudo docker pull mysql:5.6
```
查看镜像
```
docker images
```
### run mysql

```
docker run --name mysql -p 3306:3306 -v /mysql/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 -d mysql:5.6
```
### ps

查看启动情况

```
docker ps 
```

<!--more-->