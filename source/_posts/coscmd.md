---
title: coscmd,cosupload
tags: tools
date: 2018-12-30 14:00:00
---

> [coscmd](https://cloud.tencent.com/document/product/436/10976#.E4.BD.BF.E7.94.A8.E7.8E.AF.E5.A2.83),腾讯cos 脚本操作


update: 没什么用, 用 [PicGo](https://github.com/Molunerfinn/PicGo) 很方便

### 下载安装


```python
pip3 install coscmd 

pip3 install coscmd -U
```

<!--more-->

### 配置

```
# 配置
coscmd config -a your_secret_id -s your_secret_key -b  your_bucket -r ap-shanghai	
```

上条命令执行完成之后，会在 当前用户根目录下建立  .cos.conf 的文件。 你也可以直接创建 .cos.conf 的文件。如下：

```
[common]
secret_id = your_secret_id
secret_key = your_secret_key
bucket = your_bucket
region = ap-shanghai
max_thread = 5
part_size = 1
schema = https
verify = md5
anonymous = False
```

### 命令 

coscmd的命令很多，我这里只需要上传文件的命令 。具体看官网。

```
# 上传
coscmd upload local_path cos_path
```

但是，上传之后没有返回url ,可以 coscmd info 查看。

```shell
# coscmd info cos_path
$ coscmd info /jiang/test.jpg
Info with : https://test-1111111.cos.ap-shanghai.myqcloud.com/jiang/test.jpg
+--------------------+---------------------------------------------------------------+
|   jiang/test.jpg   |                                                               |
+--------------------+---------------------------------------------------------------+
|   File size        |   78151                                                       |
|   Last mod         |   2018-12-13 15:19:37                                         |
|   ACL              |   qcs::cam::uin/100005231638:uin/100005231638: FULL_CONTROL   |
+--------------------+---------------------------------------------------------------+
```

可以看到 返回  一个url 

### 自定义 coscmd upload(cosupload)

使用coscmd upload 之后没有返回 url ，在这边我是想上传成功之后返回  url .


创建脚本文件 cosupload


```shell
#!/bin/bash

#本地文件路径
local_path=$1

#cos 文件路径
cos_path=$2

# prefix url
prefix_url=https://test-111111111.cos.ap-shanghai.myqcloud.com

 # 拼接最终的url
url=$prefix_url$cos_path

# coscmd upload 
coscmd upload $local_path $cos_path


# url 复制到剪贴板
echo $url | xclip -sel clip
```

然后将脚本  mv  到 /bin

```shell
# 添加执行权限
chmod +x cosupload
```

```shell
# 调用  
cosupload local_path cos_path
```
上传成功之后，命令在剪贴板，然后直接粘贴。

- ubuntu
```
# url 复制到剪贴板
echo $url | xclip -sel clip
```
- mac
```
# url 复制到剪贴板
echo $url | pbcopy
```