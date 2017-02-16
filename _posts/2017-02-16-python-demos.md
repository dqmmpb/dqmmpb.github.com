---
layout: post
title:  "这是一个蛋疼的问题，与Python的第一次接触"
date:   2017-02-16 15:19:00 +0800
categories: jekyll update
---

## 问题的提出

前两天，檀老板发微信过来，说想请教个问题，他现在在做的是**运维开发**，听起来很高大上的岗位，通俗一点儿就是开发运维所使用的一些系统。

问题的场景上是这样的，平台是用python开发，运维在日常会通过页面执行一些后台程序，后台程序的执行日志希望通过页面的方式实时呈现给使用者。

整体来说，应该很清晰的

操作流程

浏览器-》点击执行-》后台执行-》输出日志-》日志输出到浏览器


## 方案

浏览器B 服务端S 程序P 日志文件log

B调用S
S启动一个线程执行P，P在执行过程中会生成日志log

S启动另一个线程监听日志log，讲log的`tail -f`通过即时通讯的方式给浏览器B

即时通讯的Web端技术可参考[Web端即时通讯技术盘点：短轮询、Comet、Websocket、SSE][websocket-sse]

这几种即时通讯的技术中，只有websocket是比较熟悉的，因此决定结合以前Java时接触过的websocket，外加python来研究下这种场景

## 实施

python以前没有接触过，为了解决本问题，瞄了一眼[Python基础教程][python-tutorial]，说干就干，直接敲代码

python的开发环境可自行百度。在github上找了个python-websocket的demo直接研究[simple-websocket-server][simple-websocket-server]

python的语法比较好懂的。

### 场景对python的需求

- **文件操作** 需要读写日志文件
- **多线程** 这种场景和**生产者/消费者模式**很像，程序负责生成日志log，服务端负责读取日志，因此借用**生产者/消费者模式**先实现一个基于定时发收消息的程序

[项目源码simple-websocket-server][simple-websocket-server]中的文件说明

```
├── README.md
├── setup.py //安装文件
└── SimpleWebSocketServer
    ├── FileReader.py // 独立的读取文件内容的线程
    ├── FileWriter.py // 独立的生成文件内容的线程
    ├── __init__.py
    ├── MySimpleComman.py  // Console中使用生产者/消费者模式发送消息
    ├── MySimpleExampleServer.py // 结合websocket实现的生产者/消费者模式发送消息
    ├── MySimpleFileServerWithTail.py // 使用Tail插件实现场景需求
    ├── mywebsocket.html  // 本例子的网页
    ├── SimpleExampleServer.py  // simple原代码 无关
    ├── SimpleHTTPSServer.py  // simple原代码 无关
    ├── SimpleWebSocketServer.py // simple源代码 无关
    ├── thefile.txt // 数据文件
    └── websocket.html  // simple原页面  无关
```

> 基于**生产者/消费者模式**的Python例程，请参看[项目源码simple-websocket-server][simple-websocket-server]中的`MySimpleExampleServer.py`


Python程序的运行请参看[项目源码simple-websocket-server][simple-websocket-server]的[`README.md`][simple-websocket-server-readme]文件中的中文部分



[javascript-ruanyifeng]: http://javascript.ruanyifeng.com/
[websocket-sse]: https://www.oschina.net/question/737747_2188102
[python-tutorial]: http://www.runoob.com/python/python-tutorial.html
[simple-websocket-server]: https://github.com/dqmmpb/simple-websocket-server
[python-tail]: https://github.com/kasun/python-tail
[simple-websocket-server-readme]: https://github.com/dqmmpb/simple-websocket-server/blob/master/README.md