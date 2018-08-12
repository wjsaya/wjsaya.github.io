---
title: '[运维]--ldconfig以及ld.so.conf遇到的问题及解决'
date: 2017-07-28
tags: ['运了个维','经验']
categories: [运维,linux,故障处理]
---

> 问题描述：公司做安全扫描，发现openssh漏洞太多，因此需要对openssh做一次升级，升级openssh需要先升级openssl，linuxPAM等等，在装好oenssh之后开始编译openssh，然后：error: Your OpenSSL headers do not match your library......
<!--more-->

----------
故障重现：
--
编译安装好openssl，然后执行下列语句更新库文件：
``` bash
echo "/usr/local/openssl/lib" >> /etc/ld.so.conf
ldconfig
```

更新完毕，然后安装openssh吧：
``` bash
service sshd stop
./configure --prefix=/usr --sysconfdir=/etc/ssh --with-ssl-engine --with-ssl-dir=/usr/local/openssl
```

咦？报错了？
头文件不匹配图
---

可以发现，此处报错是openssl的库文件和头文件不匹配，头文件没问题，版本就是我们想要的，可是库文件仍旧为老版本的。

那么，我们先看下当前系统有哪些库文件：
库文件图：
---
通过上图，我们可以得到两个信息：
1, 当前系统有三个openssl库。
2, 当前系统上有两个不同版本的openssl库。

三个库中，我们需要的其实是openssl1.02l，即100020cf这个库，但是系统首先识别到了lib64下的老版本库（1000010af），所以我们在安装openssh时就识别到了老版本的库，因此和我们的头文件匹配不上报错了。

我对库文件的更新操作就是更改了ld.so.conf文件并执行ldonfig，那么我再次执行下ldconfig，但是这次加上-v参数并grep一下ssl相关的内容来看看：
ldconfig -v截图：
---
从上图可以看出，系统识别的openssl版本，为1000010af的。。。


