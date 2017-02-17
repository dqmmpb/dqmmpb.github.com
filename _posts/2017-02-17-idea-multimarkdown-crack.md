---
layout: post
title:  "安装idea-multimarkdown插件并破解"
date:   2017-02-17 17:20:00 +0800
categories: jekyll update
---

## IntelliJ IDEA安装multimarkdown插件并破解

### 安装

先安装`idea-multimarkdown`插件
再在`$HOME/.IntelliJIdea2016.2/config/plugins/idea-multimarkdown/lib`目录下找到插件的`idea-multimarkdown.jar`文件

安装和破解过程参考[IntelliJ IDEA Multi-MarkDown插件安装破J全过程][idea-multimarkdown]

安装包可从从github项目[idea-multimarkdown][idea-multimarkdown-github]的`dist`下载。也可以通过插件的方式直接安装

### 破解

注意编译环境需要jdk8

使用`update-alternatives`配置多jdk环境，设置jdk8
```
sudo update-alternatives --install /usr/bin/java java /usr/lib/jvm/jdk1.8.0_91/bin/java 300
sudo update-alternatives --install /usr/bin/javac javac /usr/lib/jvm/jdk1.8.0_91/bin/javac 300
sudo update-alternatives --install /usr/bin/jar jar /usr/lib/jvm/jdk1.8.0_91/bin/jar 300
```

修改 com.vladsch.idea.multimarkdown.license.LicenseAgent.java 文件的内容如下：

1. getLicenseExpires() 整个方法体干掉不要了(**删除方法体**)，**只留返回值**改为 return "Never Expires";
2. getLicenseCode() **最后一行**返回值 return false 改为 return true，对你没有看错，只改最后一行代码;
3. isValidLicense() **删除方法体**，**只留返回值**，返回值改为 return true;
4. isValidActivation() **删除方法体**，**只留返回值**，返回值改为 return true;
5. getLicenseType() **删除方法体**，**只留返回值**，返回值改为 return "License" 或 return "license";
6. getLicenseExpiringIn() **删除方法体**，**只留返回值**，返回值改为 return 36000;(单位是天)
7. isActivationExpired() **删除方法体**，**只留返回值**，返回值改为 return false;
8. getLicenseFeatures **删除方法体**，**只留返回值**，返回值改为 return 1;(**开启高级功能，网上的破解方式[IntelliJ IDEA Multi-MarkDown插件安装破J全过程][idea-multimarkdown]并没有这步**)

修改完成后编译一下,如果报错了查看以上步骤是不是都操作正确。

编译时依赖的包需要
`idea-multimarkdown/lib`
`idea/lib`

以idea工程中配置为例，需要在`project structure`中配置`libraries`和`modules`

```
如 idea-multimarkdown/lib的路径为： /home/alphabeta/.IntelliJIdea2016.2/config/plugins/idea-multimarkdown/lib
如 idea/lib的路径为： /home/alphabeta/Software/idea-IU-162.1628.40/lib
```
![idea-multimarkdown-modules][idea-multimarkdown-modules]

![idea-multimarkdown-lib1][idea-multimarkdown-lib1]

![idea-multimarkdown-lib2][idea-multimarkdown-lib2]

使用jar命令将修改后的`LicenseAgent.java`和`LicenseAgent.class`打包到`idea-multimarkdown.jar`中

```
jar uvf idea-multimarkdown.jar com/vladsch/idea/multimarkdown/license/LicenseAgent.java
jar uvf idea-multimarkdown.jar com/vladsch/idea/multimarkdown/license/LicenseAgent.class
```

用破解后的`idea-multimarkdown.jar`文件替换原(`$HOME/.IntelliJIdea2016.2/config/plugins/idea-multimarkdown/lib`文件夹下的)`idea-multimarkdown.jar`

重启idea，大功告成

![idea-multimarkdown-screenshot][idea-multimarkdown-screenshot]


### Ubuntu下截屏工具Shutter安装
```
sudo add-apt-repository ppa:shutter/ppa
sudo apt-get update
sudo apt-get install shutter
```

[idea-multimarkdown]: http://www.jianshu.com/p/a0550f81cbd1
[idea-multimarkdown-github]: https://github.com/vsch/idea-multimarkdown
[idea-multimarkdown-screenshot]: http://ok2471oek.bkt.clouddn.com/dqmmpb/blog/2017-02-17/idea-multimarkdown.png
[idea-multimarkdown-modules]: http://ok2471oek.bkt.clouddn.com/dqmmpb/blog/2017-01-20/idea-multimarkdown-modules.png
[idea-multimarkdown-lib1]: http://ok2471oek.bkt.clouddn.com/dqmmpb/blog/2017-01-20/idea-multimarkdown-lib1.png
[idea-multimarkdown-lib2]: http://ok2471oek.bkt.clouddn.com/dqmmpb/blog/2017-01-20/idea-multimarkdown-lib2.png