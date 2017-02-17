---
layout: post
title:  "jekyll使用教程"
date:   2017-02-17 10:40:00 +0800
categories: jekyll update
---

## jekyll使用教程

换回ubuntu下，重新从github上拉代码后，jekyll需要重新安装

[jekyll中文网站][jekyll.com.cn]、[Rubgems][rubgems]

这里记录下相关问题

### Rails安装

```
方法一：使用apt-get安装
可以直接使用两个命令完成Ruby的安装。
# sudo apt-get update
# sudo apt-get install ruby
或者
# sudo apt-get install ruby2.0
方法二：使用brightbox ppa仓库安装
# sudo apt-get install Python-software-properties
# sudo apt-add-repository ppa:brightbox/ruby-ng
# sudo apt-get update
# sudo apt-get install ruby2.1 ruby2.1-dev
方法三：使用RVM安装
RVM是Ruby的版本管理器工具。
1、安装RVM
# sudo apt-get curl
# curl -L https://get.rvm.io | bash -s stable
# source ~/.rvm/scripts/rvm
2、安装RVM的环境依赖
# rvm requirements
3、安装Ruby
# rvm install ruby
如果想在Ubuntu上安装多个Ruby版本，那么可以使用下面的命令来指定使用rvm作为默认的Ruby版本管理。
# rvm use ruby --default
检查当前成功安装的Ruby版本
# ruby -v
安装gems
# gem list
# gem install [gem-name]
比如gem-name可以写sass
如果要从本地安装gems，命令如下：
# gem install --local [path of gem file]
可以使用命令更新已安装的gems，命令如下：
# gem update --system
或者
# gem update
```
rails安装遇到的问题参看[ubuntu-rails][ubuntu-rails]

### jekyll 安装

```
$ gem install jekyll
$ jekyll new my-awesome-site
$ cd my-awesome-site
$ jekyll serve
```

进入`my-awesome-site`目录中后，建议执行

> ```
> bundle install
> // 安装所有的插件
> ```
或者
> ```
> bundle update
> // 更新所有的插件
> ```

更新gem的插件，否则会遇到如下各种问题，我就是没有注意到这点，瞎搞搞，整出来如下问题，最终还是用一句`bundle update`命令更新了所有插件

### jekyll 使用

参看[基本用法][jekyll.com.cn.usage]

```
 jekyll serve --drafts
 // 使用草稿启动serve
 or
 jekyll build --drafts
 // 使用草稿进行build
```

### 遇到的问题

#### 以下问题应该是jekyll没有正确安装导致

正确的方式应该是**正确的方式是执行**
> ```
> bundle install
> // 安装所有的插件
> ```
或者
> ```
> bundle update
> // 更新所有的插件
> ```

1. gem install jekyll报错

```
    current directory: /var/lib/gems/2.3.0/gems/ffi-1.9.17/ext/ffi_c
 /usr/bin/ruby2.3 -r ./siteconf20170216-9295-7fgast.rb extconf.rb
 mkmf.rb can't find header files for ruby at /usr/lib/ruby/include/ruby.h
```

解决：
> 安装ruby2.3-dev
> ```
>  sudo apt-get install ruby2.3-dev
> ```

2. jekyll --help 报错

```
/usr/local/bin/jekyll: bad interpreter: /usr/bin/ruby2.0: no such file or directory
```

解决：
> 重装jekyll

这里重装使用 gem install jekyll后的jekyll版本为3.4.0， 但Gemfile文件中描述的版本为3.3.1，因此会引入错误4、5，因此建议限制性`bundle install`


3. jekyll serve

报错

```
/usr/lib/ruby/2.3.0/rubygems/core_ext/kernel_require.rb:55:in `require': cannot load such file -- bundler (LoadError)
        from /usr/lib/ruby/2.3.0/rubygems/core_ext/kernel_require.rb:55:in `require'
        from /var/lib/gems/2.3.0/gems/jekyll-3.4.0/lib/jekyll/plugin_manager.rb:34:in `require_from_bundler'
        from /var/lib/gems/2.3.0/gems/jekyll-3.4.0/exe/jekyll:9:in `<top (required)>'
        from /usr/local/bin/jekyll:23:in `load'
        from /usr/local/bin/jekyll:23:in `<main>'
```

解决
> 安装bundler
> ```
> sudo gem install bundler
> ```

4. jekyll serve

仍然报错

```
/var/lib/gems/2.3.0/gems/bundler-1.14.4/lib/bundler/spec_set.rb:87:in `block in materialize': Could not find ffi-1.9.14 in any of the sources (Bundler::GemNotFound)
        from /var/lib/gems/2.3.0/gems/bundler-1.14.4/lib/bundler/spec_set.rb:80:in `map!'
        from /var/lib/gems/2.3.0/gems/bundler-1.14.4/lib/bundler/spec_set.rb:80:in `materialize'
        from /var/lib/gems/2.3.0/gems/bundler-1.14.4/lib/bundler/definition.rb:176:in `specs'
        from /var/lib/gems/2.3.0/gems/bundler-1.14.4/lib/bundler/definition.rb:235:in `specs_for'
        from /var/lib/gems/2.3.0/gems/bundler-1.14.4/lib/bundler/definition.rb:224:in `requested_specs'
        from /var/lib/gems/2.3.0/gems/bundler-1.14.4/lib/bundler/runtime.rb:118:in `block in definition_method'
        from /var/lib/gems/2.3.0/gems/bundler-1.14.4/lib/bundler/runtime.rb:19:in `setup'
        from /var/lib/gems/2.3.0/gems/bundler-1.14.4/lib/bundler.rb:100:in `setup'
        from /var/lib/gems/2.3.0/gems/jekyll-3.4.0/lib/jekyll/plugin_manager.rb:36:in `require_from_bundler'
        from /var/lib/gems/2.3.0/gems/jekyll-3.4.0/exe/jekyll:9:in `<top (required)>'
        from /usr/local/bin/jekyll:23:in `load'
        from /usr/local/bin/jekyll:23:in `<main>'
```

解决
> 安装ffi
> ```
> sudo gem install bundler
> ```

但上述方法（包括3,4）会导致版本问题，**正确的方式是执行**
> ```
> bundle update
> // 安装所有的插件
> ```

5. jekll serve

报jekyll版本问题

```
/var/lib/gems/2.3.0/gems/bundler-1.14.4/lib/bundler/runtime.rb:40:in `block in setup': You have already activated jekyll 3.4.0, but your Gemfile requires jekyll 3.3.1. Prepending `bundle exec` to your command may solve this. (Gem::LoadError)
        from /var/lib/gems/2.3.0/gems/bundler-1.14.4/lib/bundler/runtime.rb:25:in `map'
        from /var/lib/gems/2.3.0/gems/bundler-1.14.4/lib/bundler/runtime.rb:25:in `setup'
        from /var/lib/gems/2.3.0/gems/bundler-1.14.4/lib/bundler.rb:100:in `setup'
        from /var/lib/gems/2.3.0/gems/jekyll-3.4.0/lib/jekyll/plugin_manager.rb:36:in `require_from_bundler'
        from /var/lib/gems/2.3.0/gems/jekyll-3.4.0/exe/jekyll:9:in `<top (required)>'
        from /usr/local/bin/jekyll:23:in `load'
        from /usr/local/bin/jekyll:23:in `<main>'
```

原因在`Gemfile`中已经描述的比较清楚：
```
source "https://rubygems.org"
ruby RUBY_VERSION

# Hello! This is where you manage which Jekyll version is used to run.
# When you want to use a different version, change it below, save the
# file and run `bundle install`. Run Jekyll with `bundle exec`, like so:
#
#     bundle exec jekyll serve
#
# This will help ensure the proper Jekyll version is running.
# Happy Jekylling!
gem "jekyll", "3.3.1"

# This is the default theme for new Jekyll sites. You may change this to anything you like.
gem "minima", "~> 2.0"

# If you want to use GitHub Pages, remove the "gem "jekyll"" above and
# uncomment the line below. To upgrade, run `bundle update github-pages`.
# gem "github-pages", group: :jekyll_plugins

# If you have any plugins, put them here!
group :jekyll_plugins do
   gem "jekyll-feed", "~> 0.6"
end
```

可以先执行`bundle install`再执行`bundle exec jekyll serve`



[ubuntu-rails]: http://www.linuxdiyf.com/linux/14518.html
[rubgems]: https://rubygems.org/
[jekyll.com.cn]: http://jekyll.com.cn/
[jekyll.com.cn.usage]: http://jekyll.com.cn/docs/usage/