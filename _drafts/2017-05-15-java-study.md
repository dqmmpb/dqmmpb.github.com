---
layout: post
title:  "重温Java"
date:   2017-05-15 17:20:00 +0800
categories: jekyll update
---

## 重温Java

脱离了Java阵营很久了，这次有机会重新拾起，作为还停留在Eclipse时代的人，Idea的强大毋庸置疑，但Java开发工具的切换还是有很多不习惯的

### Java远程调试

- tomcat配置，`catalina.sh`，开启`9000`端口
- 
> ```
> -Djava.security.egd=file:/dev/./urandom
> CATALINA_OPTS="-server -Xdebug -Xnoagent -Djava.compiler=NONE -Xrunjdwp:transport=dt_socket,server=y,suspend=n,address=9000"
> JAVA_HOME=/opt/jdk1.8.0_121
> ```
