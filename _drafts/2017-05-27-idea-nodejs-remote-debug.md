---
layout: post
title:  "Idea下NodeJS远程调试"
date:   2017-05-27 17:20:00 +0800
categories: jekyll update
---

## Idea下NodeJS远程调试

昨天在写Mock server的数据的时候，突然看下NodeJS该如何远程调试，但搜了搜，网上关于NodeJS远程调试的资料并不多，[《欲善其功，必先利其器--Nodejs调试技术总结》][nodejs-remote-debug]这篇文章的帮助很大，这里作者提供了3种调试方法，我只用到了第3种，基于node-inspector的调试，具体过程如下

### 修改node脚本，开启debug模式

在`package.json`中添加`--debug`选项，开启`debug`模式

```
    "mock": "node build/mock-server.js",
    "mock-debug": "node --debug build/mock-server.js",
```

运行
```
npm run mock-debug
```

### Idea中添加`Node.js Remote Debug`

在`Run/Debug Configurations`中添加`Node.js Remote Debug`，Host填写`本机IP`，Port填写`5858`，因为`node-inspector`的默认端口是`5858`，保存，运行，代码中设置断点，就可以了


[nodejs-remote-debug]: http://www.cnblogs.com/moonz-wu/archive/2012/01/15/2322120.html