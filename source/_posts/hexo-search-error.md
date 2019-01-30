---
title: hexo_search_error
date: 2019-01-20 17:37:31
tags: hexo
---

> 发现 local_search 不能用了

<!--more-->
![](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/blog/search_error.png
)

本地的search文件是 search.xml .所以访问：http://localhost:4000/search.xml

![](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/blog/search_xml.png
)

hexo g 打包之后的文件夹是 public ,找到 search.xml 文件。定位到具体文章。没发现什么问题呀！阿西吧啦！

### windwos 下
最后在 windows 下找到了问题，看下面图：

![](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/blog/20190120180000.png
)


### Mac 下
传送门：https://www.v2ex.com/t/298727
传送门：https://wdd.js.org/vscode-control-characters-problem.html

![](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/blog/201901201822.png
)

正则匹配找到特殊字符。修改即可。

