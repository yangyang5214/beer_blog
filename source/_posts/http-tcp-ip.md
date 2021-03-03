---
title: http_tcp_ip.m
date: 2020-03-02 21:04:26
tags: [java]
---

> 总结帖。TODO

### OSI 模型

[来自维基百科](https://zh.wikipedia.org/wiki/TCP/IP%E5%8D%8F%E8%AE%AE%E6%97%8F#%E5%9B%A0%E7%89%B9%E7%BD%91%E5%8D%8F%E8%AE%AE%E6%A0%88%E4%B8%AD%E7%9A%84%E5%B1%82) 

![来自维基百科](https://beef-1256523277.cos.ap-chengdu.myqcloud.com/bed/20210302211230.png)

#### 应用层

所有和应用程序协同工作，利用基础网络交换应用程序专用的数据的协议。

- http
- https
- FTP
- SMTP
- SSH
- SMB

#### 会话层

[socks](https://zh.wikipedia.org/wiki/SOCKS)

#### 传输层

解决诸如端到端可靠性（“数据是否已经到达目的地？”）和保证数据按照正确的顺序到达这样的问题

##### TCP

面向连接的、可靠的、基于字节流的传输层通信协议

*三次🤝*

*四次🙋*

#####  UDP

UDP只提供数据的不可靠传递，它一旦把应用程序发给网络层的数据发送出去，就不保留数据备份（所以UDP有时候也被认为是不可靠的数据报协议）

- 流媒体、在线游戏流量通常使用UDP传输。 实时视频流和音频流应用程序旨在处理偶尔丢失、错误的数据包，因此只会发生质量轻微下降，而避免了重传数据包带来的高延迟
- wireguard

#### 网络层

提供路由和寻址的功能

对网络层而言使用IP地址来唯一标识互联网上的设备，网络层依靠IP地址进行相互通信

#### 数据链路层

没明白

#### 物理层

光纤

### Reference

https://zh.wikipedia.org/wiki/TCP/IP%E5%8D%8F%E8%AE%AE%E6%97%8F

https://bingtaoli.github.io/2016/11/23/shadowsocks%E5%AE%9E%E7%8E%B0%E5%8E%9F%E7%90%86/

有一期讲的是网络传输的步骤：七层网络协议

https://www.youtube.com/playlist?list=PLqybz7NWybwUgR-S6m78tfd-lV4sBvGFG



