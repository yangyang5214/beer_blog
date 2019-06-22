---
title: 搭建blog
tags: tools
date: 2018-12-30 20:00:00
---

> beer 个人网站历程

1. 提交在github, 然后java解析网页。直接采用github 的 md 格式，前端采用vue 实现
2. 也是提交在github,  使用 vscode  enermokey 插件写，同步到印象笔记（ps:印象笔记办了会员）
3.  hexo 自己搭建 （ps:服务器，域名什么的，买了不用不白瞎了），印象笔记白瞎就白瞎了吧！

<!--more-->

### git and npm 
```
sudo apt install git 
sudo apt install npm
sudo apt install nginx 
```

### 初始化
```
npm install hexo-cli -g
hexo init beer
cd beer
npm install
```

### 目录结构
- _config.yml  
```
配置文件
```

- public 
```
hexo g  打包之后生成的静态文件
```

- node_modules
- package.json 
- scaffolds  
-  source  
```
_drafts草稿文件，_posts发布的文章
```
-  themes

我选的是 next 
```
模板

直接参考,写的很详细，全都有

https://github.com/theme-next/hexo-theme-next

http://theme-next.iissnan.com/getting-started.html
```

###  修改配置

直接修改 _config 文件
```
# 举例
title: beer
theme: next
```

图标文件的修改：

hexo next  使用的是： https://fontawesome.com/icons?from=io 的图标，所以找到名字，配置里面替换就好。

### nginx 配置

配置 server

/etc/nginx/sites-enabled/beer.conf

```
server{

	listen 80;
	server_name www.hexianwei.com;

	# hexo 打包之后的目录
	root /home/ubuntu/beer/public;
	
	index index.html;

	location / {
		index index.html;	
	}

}
```

启动 nginx , 访问  www.hexianwei.com
```
service nginx start
```

### 结束

每次代码提交到 github 之后，执行 shell 脚本 . 

- 登录服务器
- git pull
- hexo g 

本来想写个github 钩子的 ，算了吧 ，直接运行脚本还是挺方便的。


```
#!/bin/bash

ssh root@your.ip 'cd /home/ubuntu/beer;git pull;hexo g'
```
