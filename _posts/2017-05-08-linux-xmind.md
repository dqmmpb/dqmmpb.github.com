---
layout: post
title:  "Linux下XMind安装"
date:   2017-05-08 17:20:00 +0800
categories: jekyll update
---

## Linux下XMind安装

咕~~(╯﹏╰)b，2个月没有啥产出了，最近折腾的比较多的是[`vux框架`](https://vux.li)，顺带开始看起`java`代码。

好久没有写`java`了，再加上没有在`idea`中使用`maven`工程的经验，也是磕磕绊绊，各种查资料，总算跑起来了个`java`的程序。

这里先说说`vue`的开发，整体来看，效率还是蛮高的，最近遇到的最大的坑是`SPA`在`微信中`的`分享`、`支付`，`分享`由于[《这是一个大坑-微信！》](https://mp.weixin.qq.com/s/hAdtKl2i4ilyo9HxT1kXyw) 导致以前微信公众号开发时的代码无法分享了，也是醉了，各种排查原因，最终发现是这个坑……至少还算有个解决的方案。关于`支付`，就有点儿蛋疼了，明天继续搞。

服务器今天连不上，也没法更新`java`的后端代码，于是想到，`XMind`以前下载下来一直没有安装，`欲善其事，先利其器`，走起……

ps： 今天还有个不太好的消息，有个朋友玩儿期货，损失惨重，对他个人来说是个深刻的教训吧。虽然每个人都有赌徒的心里，但股票、基金、P2P什么的买买也就算了，期货，这东西还是别碰。千万！千万！千万别碰！！！当然，壕请无视这句。

### 准备工作

- 下载[XMind-Linux版](http://www.xmind.net/download/linux/) 的到的是`xmind-8-update1-linux.zip`包
- 安装`jdk8`

### 安装

解压`xmind-8-update1-linux.zip`

进入`xmind-8-update1-linux`目录

执行

```
➜  xmind-8-update1-linux sudo ./setup.sh 
[sudo] password for alphabeta: 
[setup] Installing dependencies....
Reading package lists... Done
Building dependency tree       
Reading state information... Done

sent 3,656,768 bytes  received 532 bytes  7,314,600.00 bytes/sec
total size is 3,654,132  speedup is 1.00
[setup] Done.
```

进入`xmind-8-update1-linux`目录

```
➜  xmind-8-update1-linux cd XMind_amd64 
➜  XMind_amd64 ls
configuration  p2  XMind  XMind.ini
➜  XMind_amd64 vim XMind.ini 
```

添加`jdk8`的vm路径
```
 14 @user.home/.xmind/secure_storage_linux
 15 -vm
 16 /usr/lib/jvm/jdk1.8.0_91/bin/java
 17 -vmargs
 18 -Dfile.encoding=UTF-8
```

启动`XMind`

```
➜  XMind_amd64 ./XMind 
```
