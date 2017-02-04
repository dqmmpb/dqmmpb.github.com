---
layout: post
title: "测试框架Mocha实例"
date:  2017-01-21 14:06:00 +0800
categories: jekyll update
---

## ATT 如题

### 续上篇

昨天把[gulp-css-spriter][gulp-css-spriter]的代码重新看了下，既然发现缺少测试，于是今天补上。
测试框架使用了[Mocha][Mocha]，所以今天顺带花时间研究一下，另外还有一个博文，推荐了一些前端的工具[五个值得尝试的前端开发工具][5-great-front-end-dev-tools]。
当然首先参考的当然是[阮一峰][ruanyifeng]的一篇博客[测试框架Mocha实例教程][ruanyifeng-a-mocha-tutorial-of-examples]。

按照实例教程中的一步步来就可以了。

> ps: 先放个无关的，刚刚去楼上打酱油，看到有同事在使用[google-guava][google-guava]类库写Java，这里先备注下，以防忘记，有时间也去调研下


言归正传，为了练习，还是自己敲一下代码比较好。

### Mocha测试框架

#### 安装

参看["mocha-demos"][mocha-demos]中的README文件

Mocha测试框架没有自己的断言库，因此断言使用[chai.js][chai]断言库实现，这些都能够在上述demos中找到

#### 使用

参看["mocha-demos"][mocha-demos]中的相关demo


### 其他测试框架

> [测试框架Mocha实例教程][ruanyifeng-a-mocha-tutorial-of-examples]中提到了[karma][karma]等其他测试框架。
>
> 这里有2篇文章[karam测试框架的前世今生][karma-introduction]、[Karma和Jasmine自动化单元测试][nodejs-karma-jasmine]

暂时先调研到这里，以后有时间研究研究。



[gulp-css-spriter]: https://github.com/MadLittleMods/gulp-css-spriter
[Mocha]: http://mochajs.org
[chai]: http://chaijs.com/
[chai-api-cn]: http://www.jianshu.com/p/f200a75a15d2
[ruanyifeng]: http://www.ruanyifeng.com/home.html
[ruanyifeng-a-mocha-tutorial-of-examples]: http://www.ruanyifeng.com/blog/2015/12/a-mocha-tutorial-of-examples.html
[5-great-front-end-dev-tools]: http://www.leiphone.com/news/201406/5-great-front-end-dev-tools.html
[google-guava]: http://ifeve.com/google-guava
[karma]: http://karma-runner.github.io/1.0/index.html
[nodejs-karma-jasmine]: http://blog.fens.me/nodejs-karma-jasmine
[karma-introduction]: https://yq.aliyun.com/articles/4194
[mocha-demos]: https://github.com/dqmmpb/mocha-demos
