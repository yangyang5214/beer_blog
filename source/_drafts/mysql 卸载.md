---
title: mysql  卸载
tags: mysql
notebook: 
---

- service mysql status
```
beer@beer-computer:~/mysql$ service mysql status
● mysql.service - MySQL Community Server
   Loaded: loaded (/lib/systemd/system/mysql.service; enabled; vendor preset: enabled)
   Active: active (running) since Thu 2018-12-27 09:55:52 CST; 5h 12min ago
  Process: 1745 ExecStart=/usr/sbin/mysqld --daemonize --pid-file=/run/mysqld/mysqld.pid (code=exited, status=0/SUCCESS)
  Process: 1453 ExecStartPre=/usr/share/mysql/mysql-systemd-start pre (code=exited, status=0/SUCCESS)
 Main PID: 1752 (mysqld)
    Tasks: 27 (limit: 4915)
   Memory: 208.6M
   CGroup: /system.slice/mysql.service
           └─1752 /usr/sbin/mysqld --daemonize --pid-file=/run/mysqld/mysqld.pid

12月 27 09:55:18 beer-computer systemd[1]: Starting MySQL Community Server...
12月 27 09:55:52 beer-computer systemd[1]: Started MySQL Community Server.

```
- service mysql stop
```
停止之后，然后在执行   service mysql status 查看状态
```

- whereis mysql 
```
删除 mysql 文件夹
``` 
