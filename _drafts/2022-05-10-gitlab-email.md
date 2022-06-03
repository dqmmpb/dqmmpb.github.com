---
layout: post
title:  "Gitlab and Email config"
date:   2022-05-10 09:00:00 +0800
categories: jekyll update
---

# Gitlab搭建

## 准备工作

- `服务器` 腾讯云(CPU: 2核 内存: 4GB 系统盘: 80G SSD云硬盘 系统: CentOS 8)
- `域名` 域名(dqmmpb.com，公网IP: 81.69.240.118)，Gitlab域名(gitlab.dqmmpb.com)
- `Gitlab` Gitlab安装包(见官网)
- `邮箱` 腾讯企业邮箱(腾讯云上搜索即引导到企业邮箱注册页面，需要绑定微信)

```shell script
➜  ~ cat /etc/centos-release
CentOS Linux release 8.2.2004 (Core)
```

## 安装

### 安装依赖

```shell script
➜  ~ dnf install -y curl policycoreutils openssh-server openssh-clients
```
```shell script
➜  ~ yum install policycoreutils-python-utils
```
配置邮件服务
```shell script
➜  ~ yum install postfix
➜  ~ systemctl enable postfix
➜  ~ systemctl start postfix
```

### 安装Gitlab

官网下载CentOS8对应的Gitlab
[CentOS 8 对应的Gitlab地址](https://mirrors.tuna.tsinghua.edu.cn/gitlab-ce/yum/el8/?C=M&O=D)

```shell script
➜  ~ wget https://mirrors.tuna.tsinghua.edu.cn/gitlab-ce/yum/el8/gitlab-ce-14.10.2-ce.0.el8.x86_64.rpm
```
```shell script
➜  ~ rpm -i gitlab-ce-14.10.2-ce.0.el8.x86_64.rpm
```



### root密码

初装以后，密码放在一个临时文件`/etc/gitlab/initial_root_password`中：
```shell script
vim /etc/gitlab/initial_root_password
```
该文件将在首次执行reconfigure后24小时自动删除，使用这个密码登录后，需要尽快在web页面中进行密码修改。

也可使用如下方式修改root密码
```shell script

➜  ~ sudo gitlab-rails console
--------------------------------------------------------------------------------
 Ruby:         ruby 2.7.5p203 (2021-11-24 revision f69aeb8314) [x86_64-linux]
 GitLab:       14.10.2 (07d12f3fd11) FOSS
 GitLab Shell: 13.25.1
 PostgreSQL:   12.7
------------------------------------------------------------[ booted in 51.43s ]
Loading production environment (Rails 6.1.4.7)
irb(main):001:0> user = User.find_by_username 'root'
=> #<User id:1 @root>
irb(main):002:0> user.password = '你的密码'
=> "你的密码"
irb(main):003:0> user.password_confirmation = '你的密码'
=> "你的密码"
irb(main):004:0> user.save!
=> true
irb(main):005:0> exit

```


### 修改Gitlab的IP和端口

Gitlab自带nginx默认监听端口是80，不利于管理其他服务。因此考虑使用独立的nginx监听80端口，调整Gitlab自带nginx监听端口为9999，并将域名gitlab.dqmmpb.com转发给Gitlab自带的nginx

注：如无需独立nginx需求，则可不安装。通过修改Gitlab自带nginx的配置，也可实现类似需求。

#### 独立nginx配置

安装nginx
```shell script
➜  ~ yum install nginx
```
修改nginx配置，增加`gitlab-http.conf`，转发请求到`http://localhost:9999`
```shell script
➜  ~ vim /etc/nginx/conf.d/gitlab-http.conf
```
```text
server {
  listen *:80;

  server_name gitlab.dqmmpb.com;

  location / {
    proxy_cache off;
    proxy_pass  http://localhost:9999;
  }

}
```
重启nginx服务
```shell script
➜  ~ systemctl restart nginx
```

#### Gitlab配置

修改Gitlab配置文件
```shell script
➜  ~ vim /etc/gitlab/gitlab.rb
```
修改如下内容，并保存
```text
external_url 'http://gitlab.dqmmpb.com'
nginx['listen_port'] = 9999
```
重新配置，等待配置完成
```shell script
➜  ~ gitlab-ctl reconfigure
```
`gitlab-ctl reconfigure`会自动调整Gitlab自带的nginx、redis等配置。执行耗时可能较长，耐心等待即可。

ps: 腾讯云的防火墙允许端口9999，并使用`http://81.69.240.118:9999`访问测试，也可使用域名`http://gitlab.dqmmpb.com:9999`访问。
如果不想暴露9999端口，则可使用域名访问(域名开通后，可关闭9999端口，使用域名`http://gitlab.dqmmpb.com`访问)


### 配置邮箱（用于发送邮件）

修改Gitlab配置文件
```shell script
➜  ~ vim /etc/gitlab/gitlab.rb
```
修改如下内容，并保存（特别注意：smtp的address、port、domain、tls等参数的配置）
```text

gitlab_rails['smtp_enable'] = true
gitlab_rails['smtp_address'] = "smtp.exmail.qq.com"
gitlab_rails['smtp_port'] = 465
gitlab_rails['smtp_user_name'] = "service@dqmmpb.com"
gitlab_rails['smtp_password'] = "cZiGm7ivqiyq4euA"
gitlab_rails['smtp_domain'] = "exmail.qq.com"
gitlab_rails['smtp_authentication'] = "login"
gitlab_rails['smtp_enable_starttls_auto'] = true
gitlab_rails['smtp_tls'] = true

gitlab_rails['smtp_openssl_verify_mode'] = 'none'

gitlab_rails['gitlab_email_from'] = 'service@dqmmpb.com'

```
重新配置，等待配置完成
```shell script
➜  ~ gitlab-ctl reconfigure
```
发送邮件验证
```shell script
➜  ~ gitlab-rails console
```
在console中输入
```text
Notify.test_email('service@dqmmpb.com', '邮件标题', '邮件内容').deliver_now
```
使用exit命令退出console
```text
exit
```
> 执行效果如下
>
> ```shell script
> ➜  ~ gitlab-rails console
> --------------------------------------------------------------------------------
>  Ruby:         ruby 2.7.5p203 (2021-11-24 revision f69aeb8314) [x86_64-linux]
>  GitLab:       14.10.2 (07d12f3fd11) FOSS
>  GitLab Shell: 13.25.1
>  PostgreSQL:   12.7
> ------------------------------------------------------------[ booted in 25.39s ]
> Loading production environment (Rails 6.1.4.7)
> irb(main):001:0> Notify.test_email('service@dqmmpb.com', '邮件标题', '邮件内容').deliver_now
> Delivered mail 627a8d6614600_33ae45882325c@VM-16-16-centos.mail (2437.4ms)
> => #<Mail::Message:270520, Multipart: false, Headers: <Date: Wed, 11 May 2022 00:05:58 +0800>, <From: GitLab <service@dqmmpb.com>>, <Reply-To: GitLab <noreply@gitlab.dqmmpb.com>>, <To: service@dqmmpb.com>, <Message-ID: <627a8d6614600_33ae45882325c@VM-16-16-centos.mail>>, <Subject: 邮件标题>, <Mime-Version: 1.0>, <Content-Type: text/html; charset=UTF-8>, <Content-Transfer-Encoding: 7bit>, <Auto-Submitted: auto-generated>, <X-Auto-Response-Suppress: All>>
> irb(main):002:0> exit
> 
> ```

此时，在腾讯企业邮箱的`service@dqmmpb.com`收件箱中查看收到的邮件

### SSL配置

#### 修改gitlab配置

Gitlab配置SSL，由于使用独立的nginx转发，修改`/etc/gitlab/gitlab.rb`文件
```text
external_url 'https://gitlab.dqmmpb.com'
nginx['listen_port'] = 9443
# nginx['redirect_http_to_https'] = true
# nginx['redirect_http_to_https_port'] = 9999
```
调整external_url为https，同时修改nginx的监听端口为9443；

ps: 如果想保留原有`http://localhost:9999`转跳，则配置redirect_http_to_https为true，转跳端口redirect_http_to_https_port为9999，即对http的9999端口重定向到9443

#### 修改nginx配置

修改nginx配置，将`gitlab-http.conf`，
```shell script
➜  ~ vim /etc/nginx/conf.d/gitlab-http.conf
```
增加ssl配置，同时将转发`http://localhost:9999`改为`https://localhost:9443`
```text
server {
  listen *:80;

  server_name gitlab.dqmmpb.com www.gitlab.dqmmpb.com;

  rewrite ^(.*)$ https://$host$1; #将所有HTTP请求通过rewrite指令重定向到HTTPS。

}

server {
  listen *:443 ssl;

  server_name gitlab.dqmmpb.com www.gitlab.dqmmpb.com;

  ssl_certificate /etc/nginx/cert/7855935_gitlab.dqmmpb.com.pem;  #需要将cert-file-name.pem替换成已上传的证书文件的名称。
  ssl_certificate_key /etc/nginx//cert/7855935_gitlab.dqmmpb.com.key; #需要将cert-file-name.key替换成已上传的证书私钥文件的名称。
  ssl_session_timeout 5m;
  ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;
  #表示使用的加密套件的类型。
  ssl_protocols TLSv1.1 TLSv1.2 TLSv1.3; #表示使用的TLS协议的类型。
  ssl_prefer_server_ciphers on;


  location / {
    proxy_cache off;
    proxy_pass  https://localhost:9443;
  }

}

```

### 开启gitlab registry

Gitlab集成了`docker registry`的功能，可以用来作为一个docker镜像私有仓库使用

#### 配置

Gitlab默认不打开`container registry`的功能，需要修改配置打开，参考[官方配置](https://docs.gitlab.com/ee/administration/packages/container_registry.html#configure-container-registry-under-its-own-domain)。

修改配置`/etc/gitlab/gitlab.rb`文件，将`registry_external_url`的值修改为`https://registry.dqmmpb.com`
```text
registry_external_url 'https://registry.dqmmpb.com'
registry_nginx['listen_port'] = 5050
registry_nginx['ssl_certificate'] = "/etc/gitlab/ssl/registry.dqmmpb.com.pem"
registry_nginx['ssl_certificate_key'] = "/etc/gitlab/ssl/registry.dqmmpb.com.key"
```
registry_external_url 这个地址是docker命令进行pull或者push镜像的仓库地址。
* 注意，由于使用了独立nginx，采用的proxy_pass方式，如果不配置listen_port，则会使用443作为端口，会导致502错误

在nginx中新增配置文件`/etc/nginx/conf.d/gitlab-docker-registry.conf`，配置如下：
```text
server {
  listen *:80;

  server_name registry.dqmmpb.com www.registry.dqmmpb.com;

  rewrite ^(.*)$ https://$host$1; #将所有HTTP请求通过rewrite指令重定向到HTTPS。

}

server {
  listen *:443 ssl;

  server_name registry.dqmmpb.com www.registry.dqmmpb.com;

  ssl_certificate /etc/nginx/cert/7856350_registry.dqmmpb.com.pem;  #需要将cert-file-name.pem替换成已上传的>证书文件的名称。
  ssl_certificate_key /etc/nginx//cert/7856350_registry.dqmmpb.com.key; #需要将cert-file-name.key替换成已上>传的证书私钥文件的名称。
  ssl_session_timeout 5m;
  ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;
  #表示使用的加密套件的类型。
  ssl_protocols TLSv1.1 TLSv1.2 TLSv1.3; #表示使用的TLS协议的类型。
  ssl_prefer_server_ciphers on;


  location / {
    proxy_pass  https://localhost:5050;
  }

}
```
执行 `gitlab-ctl reconfigure`
```shell script
gitlab-ctl reconfigure
```
重启gitlab后，可以在gitlab面板中看到Container Registry菜单


#### 项目中测试

参考[helloword项目](https://gitlab.dqmmpb.com/study/test/end/helloworld/container_registry)

### 查看日志

```shell script
# 查看状态
sudo gitlab-ctl status
# 查看所有的logs; 按 Ctrl-C 退出
sudo gitlab-ctl tail
# 拉取/var/log/gitlab下子目录的日志
sudo gitlab-ctl tail gitlab-rails
# 拉取某个指定的日志文件
sudo gitlab-ctl tail nginx/gitlab_error.log

# Gitlab维护
gitlab-ctl status  # gitlab各组件服务状态
gitlab-ctl start/restart/stop [组件名]  # gitlab所有组件的统一控制（其中Unicorn组件重启完成前GitLab会报502）
gitlab-ctl tail [/var/log/gitlab下的某子目录]  # 实时查看日志

gitlab-ctl reconfigure  # 重载配置

# ContainerRegistry维护
gitlab-ctl registry-garbage-collect  # 垃圾回收，清理废弃layer（registry停机）

```

