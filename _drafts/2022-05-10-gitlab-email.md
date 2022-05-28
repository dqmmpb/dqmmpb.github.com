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
修改nginx配置，增加`gitlab-http.conf`，转发到9999端口
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

腾讯云的防火墙允许端口9999，并使用 `http://81.69.240.118:9999/` 访问(域名开通后，可关闭9999端口，使用域名 `http://gitlab.dqmmpb.com` 访问)


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
