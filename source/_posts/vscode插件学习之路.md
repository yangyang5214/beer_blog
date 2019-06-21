---
title: vscode插件学习之路
date: 2019-06-21 22:04:20
tags: [工具,vscode]
---


> 以 tencent cos vscode 插件为例子，记录一下，vscode 的插件开发过程。

### 安装

```
$ npm install -g yo generator-code
```

### 初始化

```
$ yo code
$ yo code
? ==========================================================================
We're constantly looking for ways to make yo better! 
May we anonymously report usage statistics to improve the tool over time? 
More info: https://github.com/yeoman/insight & http://yeoman.io
========================================================================== Yes

     _-----_     ╭──────────────────────────╮
    |       |    │   Welcome to the Visual  │
    |--(o)--|    │   Studio Code Extension  │
   `---------´   │        generator!        │
    ( _´U`_ )    ╰──────────────────────────╯
    /___A___\   /
     |  ~  |
   __'.___.'__
 ´   `  |° ´ Y `

? What type of extension do you want to create? New Extension (JavaScript)
? What's the name of your extension? vscode-tencent-cos
? What's the identifier of your extension? vscode-tencent-cos
? What's the description of your extension? tencent cos of vscode plugin
? Enable JavaScript type checking in 'jsconfig.json'? Yes
? Initialize a git repository? Yes
? Which package manager to use? npm
```

提示： vscode-tencent-cos 已存在

