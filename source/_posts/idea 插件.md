---
title: idea 插件
tags: 工具
date: 2018-12-30 20:00:00
---

###  GenerateSerialVersionUID

> 为实现序列化的类，添加 SerialVersionUID 字段. alt + insert .

Adds a new action 'SerialVersionUID' in the generate menu (alt + ins). The action adds an serialVersionUID field in the current class or updates it if it already exists, and assigns it the same value the standard 'serialver' JDK tool would return. The action is only visible when IDEA is not rebuilding its indexes, the class is serializable and either no serialVersionUID field exists or its value is different from the one the 'serialver' tool would return.

<!--more-->

### .ignore

> 生成各种ignore文件，一键创建git ignore文件的模板，免得自己去写

项目右键 ——>new  ——> .ignore file ——> 根据语言选择模板
### lombok

生成  get   set  equals  canEqual 等方法

### GsonFormat

json 转  实体类

alt + insert 

### findBugs-IDEA

代码右键  -> 选择 project 

### Maven Helper 

> 看maven 冲突

pom.xml 文件 下面 有 Dependency Analyzer 选项


### VisualVM Launcher

### CodeGlance

编辑器右侧的小地图