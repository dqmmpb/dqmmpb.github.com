---
layout: post
title:  "只能说自己太2了"
date:   2017-06-04 17:20:00 +0800
categories: jekyll update
---

## 只能说自己太2了

今天的这个问题必须要记下来，最近在使用[element][element]作为前端UI框架开发，前面一切都很顺利。  
今天真是哔了狗了，突然间`npm run dev`在`webpack`时卡主了，着实出乎意料，编译什么的都没有报错，就是`index.html`的`bundle`一直不过去，导致编译不成功，甚是诡异。  
然后就排查呗，比较了代码，感觉没有改错什么，各种折腾，起初怀疑是版本问题，`npm`重装，但问题还未解决。然后怀疑是天太热了，编译导致cpu占用过高，编译直接挂住了，然后重启，休息，问题依然存在。最后比较tag中的某次版本，感觉`package.json`和编译的`js`脚本也差别不大，就是死活过不去，但历史的tag是好的。  
想了想，逐个比较吧，最终终于发现了，在如下代码中发现了问题
``` html
<el-form-item>
  <el-button type="primary"><<返回</el-button>
</el-form-item>
```
`返回`前面的`<<`导致了`vue`在编译模板时出错，而`idea`上并没有明显的error提示（其实是有，只是我真的没有注意到……），这种弱智的错误导致了2个小时直接废掉了，=_=！  
特此记下，警示！！！

[element]: http://element.eleme.io/#/zh-CN