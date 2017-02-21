---
layout: post
title:  "Apache2.4-php7.1环境配置"
date:   2017-02-21 17:20:00 +0800
categories: jekyll update
---

## Apache2.4-php7.1环境配置

记得原先机器上有php5.6的开发环境的，但忘记怎么配了。刚好有个php的项目要做，于是重新开始搭环境。

搭环境的过程中遇到过各种问题，估计是和原先的apache有冲突，而且原先安装的nginx好像也有问题？于是先全部卸载

### 安装apache2.4

#### 卸载旧环境（反正是找到的都删掉了）

```
sudo apt-get --purge remove apache2
```

把所有apache2相关的全部删除调，包括`apache2*`等所有包，使用dpkg列出所有的，全部`sudo apt-get --purge remove`删除

```
dpkg --get-selections | grep apache2
```

同时把所有apache相关的配置删除

```
sudo find / -name "*apache*"
```

将上面列出的文件全部删除

```
sudo rm -rf
```

或直接执行

```
sudo find / -name "*apache*" | xargs sudo rm -rf
```

#### 重启后安装

重启后安装apache2

```
sudo apt-get install apache2
```

终于能够成功启动apache2了

### 安装php7.1

```
sudo apt-get install php7.1
```
这里可能需要添加ppa源

```
add-apt-repository ppa:ondrej/php-7.0
```

## 配置apache2和php7

### 配置apache2

ubuntu中apache2安装的目录可以使用`whereis apache2`查看

```
whereis apache2
cd /etc/apache2
```

修改配置文件

![apache2-php-idea-1][apache2-php-idea-1]

配置目录的简要说明

```
.
├── apache2.conf  // 主配置文件
├── conf-available  // 可用配置文件
├── conf-enabled  // 启用的配置文件
├── envvars  // 全局半两
├── magic  // MIME类型
├── mods-available  // 可用模块
├── mods-enabled  // 启用的模块
├── ports.conf  // 端口配置
├── sites-available  // 可用虚拟站点
└── sites-enabled  // 启用的虚拟站点
```

#### `apache2.conf`配置文件

`apache2.conf`配置文件是apache2的配置文件入口，文件中对apache2的配置有较为详细的描述，另外也可参考apache2官网的文档

```
 19 #   /etc/apache2/
 20 #   |-- apache2.conf
 21 #   |   `--  ports.conf
 22 #   |-- mods-enabled
 23 #   |   |-- *.load
 24 #   |   `-- *.conf
 25 #   |-- conf-enabled
 26 #   |   `-- *.conf
 27 #   `-- sites-enabled
 28 #       `-- *.conf
 
 
 139 # Include module configuration:
 140 IncludeOptional mods-enabled/*.load
 141 IncludeOptional mods-enabled/*.conf
 142 
 143 # Include list of ports to listen on
 144 Include ports.conf
 
 
 164 <Directory /var/www/>
 165     Options Indexes FollowSymLinks
 166     AllowOverride None
 167     Require all granted
 168 </Directory>
```

以上为基本结构，主要需要配置`端口`、`根路径`、`文件夹`等

#### `ports.conf`配置文件

`ports.conf`配置文件配置的主要是apache2监听的端口，可配置多个虚拟主站，通过配置不同的虚拟主站端口实现

```
  5 Listen 80
  6 NameVirtualHost *:8888
  7 Listen 8888
```
如上，配置了`8888`端口上的虚拟主站，而虚拟主站的相关配置文件在`sites-available`目录下配置

#### `sites-available`配置

`sites-available`目录下是虚拟站点的相关配置，默认是有个`000-default.conf`，复制一份`000-default.conf`开始配置自己的虚拟站点。

```
sudo cp 000-default.conf phptest.conf
```

将虚拟站点的端口改为`8888`，`DocumentRoot`的配置修改为`/var/www/phptest`（phptest为php项目目录，后面将结合IntelliJ Idea的php开发环境搭建来介绍phptest项目）
`ErrorLog`和`CustomLog`也修改为自己可用的

```
  1 <VirtualHost *:8888>
  2     # The ServerName directive sets the request scheme, hostname and port that
  3     # the server uses to identify itself. This is used when creating
  4     # redirection URLs. In the context of virtual hosts, the ServerName
  5     # specifies what hostname must appear in the request's Host: header to
  6     # match this virtual host. For the default virtual host (this file) this
  7     # value is not decisive as it is used as a last resort host regardless.
  8     # However, you must set it for any further virtual host explicitly.
  9     #ServerName www.example.com
 10 
 11     ServerAdmin webmaster@localhost
 12     DocumentRoot /var/www/phptest
 13 
 14     # Available loglevels: trace8, ..., trace1, debug, info, notice, warn,
 15     # error, crit, alert, emerg.
 16     # It is also possible to configure the loglevel for particular
 17     # modules, e.g.
 18     #LogLevel info ssl:warn
 19 
 20     ErrorLog ${APACHE_LOG_DIR}/error.phptest.log
 21     CustomLog ${APACHE_LOG_DIR}/access.phptest.log combined
 22 
 23     # For most configuration files from conf-available/, which are
 24     # enabled or disabled at a global level, it is possible to
 25     # include a line for only one particular virtual host. For example the
 26     # following line enables the CGI configuration for this host only
 27     # after it has been globally disabled with "a2disconf".
 28     #Include conf-available/serve-cgi-bin.conf
 29 </VirtualHost>
 30 
 31 # vim: syntax=apache ts=4 sw=4 sts=4 sr noet
```

#### `sites-enabled`配置

`sites-enabled`用来开启当前使用的虚拟站点，这里通过软链接的方式链接到`sites-available`目录下的配置

```
sudo ln -s ../sites-available/phptest.conf phptest.conf
```

配置好后通过`service`重启apache2服务

```
service apache2 restart
```

访问`http://localhost:80`查看apache2的默认页面

![apache2-php-idea-2][apache2-php-idea-2]

这样apache2+php7.1的环境基本就算ok了，感谢ubuntu的`apt-get`

### IntelliJ Idea配置php开发环境

#### PHP插件的安装

首先安装php插件，在`Settings`>`plugins`中查找`php`插件并安装

![apache2-php-idea-3][apache2-php-idea-3]

#### 配置插件

##### idea中php7设置

在`Settings`>`Languages & Frameworks`中找到`PHP`插件的配置项，配置如下

![apache2-php-idea-4][apache2-php-idea-4]

添加PHP7.1的开发黄精，并设置路径，这里在`Debugger`中最初是没有`xdebug`的，`xdebug`的安装参看[安装xdebug](#安装xdebug)。这里保存就好了


##### 新建phptest项目

新建php项目

- 第一步

![apache2-php-idea-7][apache2-php-idea-7]

- 第二步

![apache2-php-idea-8][apache2-php-idea-8]

- 第三步

![apache2-php-idea-9][apache2-php-idea-9]

新建index.php文件

``` php
<?php
/**
 * Created by IntelliJ IDEA.
 * User: alphabeta
 * Date: 17-2-20
 * Time: 下午7:18
 */

$foo = 10;
$bar = 20;
$compare = $foo <=> $bar;

echo $compare;

phpinfo();
```

##### idea配置`Deployment`

在`Tools`>`Deployment`>`Configuration`中设置apache的路径

![apache2-php-idea-5][apache2-php-idea-5]

添加apache项目的部署路径，首先在`/var/www`目录（apache2默认的根目录）下新建目录`phptest`，并讲`phptest`目录的权限设置为可写

```
sudo mkdir /var/www/phptest
sudo chmod a+w /var/www/phptest
```

配置`phptest`项目在apache下部署路径（从`phptest`项目映射到`/var/www/phptest`）

![apache2-php-idea-6][apache2-php-idea-6]

##### idea配置configuration

在`Run`>`Edit Configurations`中添加`PHP Web Application`

![apache2-php-idea-10][apache2-php-idea-10]

注意：`Use path mappings`中最好指定`index.php`的映射路径`/var/www/phptest/index.php`，否则在调试的时候回报路径不匹配的错误
**至于有没有更好的方式，比如直接配置路径，自动映射到文件夹，暂时还不知道**

##### idea启动xdebug调试

在`Run`>`Debug 'phptest'`启动调试，并设置断点，这时xdebug会启动默认浏览器访问`index.php`页面，同时idea进入调试环境

![apache2-php-idea-11][apache2-php-idea-11]

调试环境

![apache2-php-idea-12][apache2-php-idea-12]

### 后记

- 之所以出现各种问题，折腾了好几天，一方面是对php的开发环境搭建确实不熟悉，另一方面是，原先的apache环境忘记了，出问题后就使用`apache2.4.25`的源码搭建了下，但配置总是有问题，也是汗。最终还是采用了全部删净，包括配置文件什么的。使用ubuntu中的`apt-get`默认安装的方式，总算跑起来了。
- 配置好后，启动idea调试环境，不能进入断点，找了很长时间问题可能出在哪里，最终发现是xdebug的`remote_port`配置有问题，将`remote_port`配置成和`apache`服务器`8888`端口一样的`8888`了，`remote_prot`端口应该是xdebug和idea进行通讯的端口，因此不能和服务器`8888`端口相同，遂改成默认的`9000`端口，一切正常


### 安装xdebug

xdebug也没有去仔细了解，而是看了IntelliJ Idea中的配置中有一项，所以就安装了。

安装采用源码编译安装的方式（源码共github上下载），参考[xdebug安装][xdebug-install]

编译环境需要安装`php7.1-dev`，请预先安装

```
sudo apt-get install php7.1-dev
```

#### 安装和配置

步骤如下：

```
git clone git://github.com/xdebug/xdebug.git
cd xdebug
phpize
```

phpize命令或生成`configure`文件

```
./configure --enable-xdebug
make
sudo make install
```

配置PHP使用xdebug，找到php的配置文件

```
cd /etc/php/7.1
```

配置`xdebug.ini`文件

```
sudo vim /etc/php/7.1/mods-available/xdebug.ini
```
写入如下内容配置xdebug生效（**注意remote_port为xdebug的远程调试端口，默认9000，不能和服务器的端口8888相同，否则无法调试了**）

```
 zend_extension=xdebug.so
 ;xdebug.auto_enable=on
 ;xdebug.default_enable=on
 ;xdebug.auto_profile=on
 ;xdebug.collect_params=on
 ;xdebug.collect_return=on
 ;xdebug.profiler_enable=on
 ;xdebug.remote_host=localhost
 xdebug.remote_port=9000
 xdebug.remote_enable=on
 xdebug.remote_connect_back=on
 ;xdebug.trace_output_dir="/usr/share/php/xdebug/"
 ;xdebug.profiler_output_dir="/usr/share/php/xdebug/"
```

配置`php7.1-fpm`、`cli`、`apache2`中`xdebug`生效（使用软链接）

```
sudo ln -snf /etc/php/7.1/mods-available/xdebug.ini /etc/php/7.1/fpm/conf.d/20-xdebug.ini
sudo ln -snf /etc/php/7.1/mods-available/xdebug.ini /etc/php/7.1/cli/conf.d/20-xdebug.ini
sudo ln -snf /etc/php/7.1/mods-available/xdebug.ini /etc/php/7.1/apache2/conf.d/20-xdebug.ini
```

重启服务

```
service php7.1-fpm restart
service apache2 restart
```

#### 检测xdebug是否生效

我们有很多方式来确认Xdebug已经正常工作了：

 * 在Terminal执行php -m，在输出结果最后的[Zend Modules]部分，可以看到有Xdebug；
 * 执行php -i | grep xdebug，在输出的结果中，可以看到有xdebug support => enabled；
 * 访问我们之前的`http://localhost:8888/index.php`，可以找到Xdebug的配置

![apache2-php-idea-13][apache2-php-idea-13]


### 参考

- [IntelliJ Idea官方php环境配置][intellij-idea-php]
- [xdebug官网安装文档][xdebug-install]
- [PHP7来了，教你如何配置Xdebug和Homestead环境][thx-php7]

[apache2-php-idea-1]: http://ok2471oek.bkt.clouddn.com/dqmmpb/blog/2017-02-21/apache2-php7-idea-1.png
[apache2-php-idea-2]: http://ok2471oek.bkt.clouddn.com/dqmmpb/blog/2017-02-21/apache2-php7-idea-2.png
[apache2-php-idea-3]: http://ok2471oek.bkt.clouddn.com/dqmmpb/blog/2017-02-21/apache2-php7-idea-3.png
[apache2-php-idea-4]: http://ok2471oek.bkt.clouddn.com/dqmmpb/blog/2017-02-21/apache2-php7-idea-4.png
[apache2-php-idea-5]: http://ok2471oek.bkt.clouddn.com/dqmmpb/blog/2017-02-21/apache2-php7-idea-5.png
[apache2-php-idea-6]: http://ok2471oek.bkt.clouddn.com/dqmmpb/blog/2017-02-21/apache2-php7-idea-6.png
[apache2-php-idea-7]: http://ok2471oek.bkt.clouddn.com/dqmmpb/blog/2017-02-21/apache2-php7-idea-7.png
[apache2-php-idea-8]: http://ok2471oek.bkt.clouddn.com/dqmmpb/blog/2017-02-21/apache2-php7-idea-8.png
[apache2-php-idea-9]: http://ok2471oek.bkt.clouddn.com/dqmmpb/blog/2017-02-21/apache2-php7-idea-9.png
[apache2-php-idea-10]: http://ok2471oek.bkt.clouddn.com/dqmmpb/blog/2017-02-21/apache2-php7-idea-10.png
[apache2-php-idea-11]: http://ok2471oek.bkt.clouddn.com/dqmmpb/blog/2017-02-21/apache2-php7-idea-11.png
[apache2-php-idea-12]: http://ok2471oek.bkt.clouddn.com/dqmmpb/blog/2017-02-21/apache2-php7-idea-12.png
[apache2-php-idea-13]: http://ok2471oek.bkt.clouddn.com/dqmmpb/blog/2017-02-21/apache2-php7-idea-13.png
[intellij-idea-php]: https://www.jetbrains.com/help/idea/2016.3/php-specific-guidelines.html
[xdebug-install]: https://xdebug.org/docs/install
[thx-php7]: https://gold.xitu.io/entry/5668198060b25b0400e838cd