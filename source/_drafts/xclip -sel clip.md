---
title: xclip -sel clip
tags: linux
notebook: 
---



> xclip -sel clip 

起因是，配置 [github ssh](https://www.jianshu.com/p/4405d90dab9b)，发现有条命令 **$ xclip -sel clip < ~/.ssh/id_rsa.pub **，xcip 的使用。但是 使用  man xclip 之后没找到  -sel 的选项。也没仔细阅读全部手册。
后来在 stack overflow 上提了 [question](https://stackoverflow.com/questions/53735089/what-is-the-meaning-about-xclip-sel),有人回答
```
man xclip is your friend: https://linux.die.net/man/1/xclip
TL;DR it tells you which X selection to copy the text to
```
其实就是 man xclip  . 然后，又回过头来仔细阅读了 手册。 发现下面两段。

```
# sel 是 -selection 的缩写
 Options can be abbreviated as long as they remain unambiguous. For example, it is possible to use -d or -disp instead of -display. However, -v couldn't be used because it is ambiguous (it could be short for -verbose or -version), so it would be interpreted as a filename.
```

```
#clip  是 clipboard 的缩写 
Note that only the first character of the selection specified with the -selection option is important. This means that "p", "sec" and "clip" would have the same effect as using "primary", "secondary" or "clipboard" respectively.
```
还有就是 -selection 的解释。发现 X  selection 有三个类型。-selection 选项默认是到 primary ，加上 clip 是到剪贴板。

[Why do we have 3 types of X-selections in LINUX?](https://superuser.com/questions/200444/why-do-we-have-3-types-of-x-selections-in-linux
)

所以：

```
$ xclip -sel clip < ~/.ssh/id_rsa.pub 
```

意思就是：将id_ras.pub 文件的内容复制到剪贴板

### man xclip 

```shell
$ man xclip
```

参考：

[man xclip](https://linux.die.net/man/1/xclip)

[[Linux常用命令]xclip的使用](http://nekoqaq.lofter.com/post/1e7268fe_cb34817)

[Why do we have 3 types of X-selections in LINUX?](https://superuser.com/questions/200444/why-do-we-have-3-types-of-x-selections-in-linux
)
###  xclip 

```
xclip - command line interface to X selections (clipboard)
```

### 主要用法

> -selection
specify which X selection to use, options are "primary" to use XA_PRIMARY (default), "secondary" for XA_SECONDARY or "clipboard" for XA_CLIPBOARD

默认是复制到 XA_PRIMARY , 加上参数 clipboard 是 复制到 XA_CLIPBOARD。

-sel 是 -selection 的缩写

> Options can be abbreviated as long as they remain unambiguous. For example, it is possible to use -d or -disp instead of -display. However, -v couldn't be used because it is ambiguous (it could be short for -verbose or -version), so it would be interpreted as a filename.

clip  是 clipboard 的缩写

> Note that only the first character of the selection specified with the -selection option is important. This means that "p", "sec" and "clip" would have the same effect as using "primary", "secondary" or "clipboard" respectively.

```
#将readme 的文件内容复制到剪贴板
xclip -sel clip readme
```

```
# 文件内容复制到 XA_PRIMARY
xclip readme 
```


```
# XA_PRIMARY 里的内容输出
xclip -o 
```

```
# XA_CLIPBOARD （剪贴板）里的内容输出
xclip -o -sel clip 
```