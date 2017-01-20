---
layout: post
title:  "从今天开始..."
date:   2017-01-20 21:21:33 +0800
categories: jekyll update
---

## 这是一篇标准的心灵鸡汤

### 从"甦"这个字说起

相信有很多人不认识这个字，于是可以装下逼O(∩_∩)O哈哈~

![Su][su]

第一次看到这个字是路过隔壁公司门口，门上赫然写着"甦活"二字，装修以黑色昏暗系为主，文艺气息很浓，很像是新型餐饮模式的公司，也就先入为主的认为这应该是家以餐饮O2O为主题的创业公司。

### "甦活"="更生活"="更好的生活"

一次非常偶然的机会，得以去"甦活"中感受下，我也是在这个时候才知道"甦"字应该读"Su"。
这是一家很小的设计公司，boss是一名资深设计师，员工不到10人，公司承接各种设计业务，工业设计、室内设计（没错——公司的装修就是老板自己设计的）。

令我惊讶的是她们的产品：电烤箱 + 指纹锁。老板介绍说，电烤箱是她们自己设计的品牌[ziiiro][ziiiro]，并委托代工厂代工的，整体定位中高端市场。

我当时的最大疑问就是，淘宝上电烤箱种类已经那么多，很多烤箱品类都很便宜，这片红海市场她们有什么竞争力？

boss说，首先她们产品的定位是中高端市场，以白领为主，比如全职妈妈。这类人群首先不差钱，其次有时间，最重要的一点是非常在意自己的烘焙作品（包括色泽、口感、是否精致等）。
当然这类人群对电烤箱的外观也有很高的要求。而"ziiiro"正是基于这类人群设计的。外观很欧美化，黑色镜面反光，代工亲自监理，产品质量上的绝对保障。从前期的销量上看，市场还是很认可的

这里做个软广，附上[淘宝链接][ziiiro]，有需要朋友可以参考下。
![ziiiro-show][ziiiro-show]

而令我更为好奇的是，boss是个设计师，电烤箱和设计还是很紧密相关的，可指纹锁和设计感觉明明是八竿子打不着的嘛，怎么被boss也搞成了他们的产品呢，我反正是百思不得其解，如果有朋友能指点一二。

### "甦活"，一切从生活开始

当今的中国，物质生活可以说是非常丰富，各种需求层出不穷。开车的想开更好的车，会吃的想吃更美味的事物，"甦活"的定位就是让原有的生活更美好。可这和我这个IT民工又有什么关系呢？

深挖一下，产品、软件都是互通的，都是从需求出发，以满足需求为首要目标。需求-设计/研发-产品-反馈-新需求，这是一个产品从无到有到成熟的工程。
其实人生也是一样的，从早期的混沌目标开始，到某个版本的人生上线，再到对自我经历的反思，再到新的目标的制定，不停迭代。目标的明确程度、执行阶段的执行力、迭代周期的长短决定了你的人生应该是什么样的。
而这一切的根源，都在于初期的定位，包括产品定位、人生定位。定位决定了产品的后续走向。

我想，程序猿的人生应该不仅仅是代码的人生，而应该是程序猿自己的人生。以前更多的时候我沉浸在代码中，迷失了方向；而现在，重新找回方向，重新定位，对我来说还为时不晚。
昨天朋友圈有人转发了"任正非在2017年市场工作会上的讲话"，提到了温暖、幸福、快乐、平凡、奋斗，而这些都是英雄所应具备的。

到底是英雄还是狗熊，时间会证明一切的！


### 代码！还是代码！

最后，作为一个程序猿，怎么能没有代码呢，O(∩_∩)O哈哈~
最近回顾了下CSS知识点，感谢作者[doyoe][doyoe]整理的[CSS参考手册][css]

抽空看了下sprite sheet，决定在demo中使用下，本身项目使用了gulp编译
所以选择了[gulp-css-spriter][gulp-css-spriter]来合并spritersheet。

```git
$ npm install --save-dev gulp-css-spriter
```
参照[gulp-css-spriter][gulp-css-spriter]的[Usage][gulp-css-spriter-usage]来就是了。

因为是回顾CSS知识点，就尝试使用animation来实现Loading的...效果，以下为less代码：
```less
.loading {
  p {
    color: red;
    &:after {
      content: '';
      color: red;
      animation: loading 1s linear infinite;
    }

    @keyframes loading {
      0% {
        content: '';
      }
      25% {
        content: '.';
      }
      50% {
        content: '..';
      }
      75% {
        content: '...';
      }
      100% {
        content: '...';
      }
    }
  }
}
```

于是捎带看了下大神张鑫旭[贝塞尔曲线][bezier]的知识点科普（ps：关于自己对贝塞尔曲线的学习，今后另开随笔吧）。
参考[移动web动画设计的一点心得——css3实现跑步][animation]，我也实践了CSS3的Animation。
这里出于对雪碧图的尝试，将原有的背景图拆成7张，使用[gulp-css-spriter][gulp-css-spriter]进行合并。
于是。。。 问题就来了，先看代码：
```less
#good-morning {

  @student-width: 180px;
  @student-height: 300px;
  .student {
    width: @student-width;
    height: @student-height;
  }

  .animate {
    animation-name: good-morning;
    animation-duration: 0.95s;
    animation-timing-function: step-start;
    animation-iteration-count: infinite;
  }

  .loop-good-morning(@n:7; @i:0) when (@i <= @n) {
    @pi: percentage(@i/@n);
    @pm: mod(@i, @n)+1;
    @{pi} {
      background-image: url("@{imgBaseUrl}/animation/a@{pm}.png");
    }
    .loop-good-morning(@n, (@i + 1));
  }

  .paused {
    animation-play-state: paused;
  }

  .running {
    animation-play-state: running;
  }


  @keyframes good-morning {
    .loop-good-morning();
  }
}
```
这里通过循环，将背景图片放到了`keyframes`里，于是。。。[gulp-css-spriter][gulp-css-spriter]居然没有将`keyframes`中的background-image合并过去，也没有替换其中的文件。
```less
@keyframes good-morning {
  0% {
    background-image: url("../../images/animation/a1.png");
  }
  14.28571429% {
    background-image: url("../../images/animation/a2.png");
  }
  28.57142857% {
    background-image: url("../../images/animation/a3.png");
  }
  42.85714286% {
    background-image: url("../../images/animation/a4.png");
  }
  57.14285714% {
    background-image: url("../../images/animation/a5.png");
  }
  71.42857143% {
    background-image: url("../../images/animation/a6.png");
  }
  85.71428571% {
    background-image: url("../../images/animation/a7.png");
  }
  100% {
    background-image: url("../../images/animation/a1.png");
  }
}
```
一脸懵逼，囧rz。。。

于是硬着头皮翻源码，[gulp-css-spriter][gulp-css-spriter]使用[css][css]插件来处理css文件
```javascript
var styles = css.parse(String(resultantFile.contents), {
    'silent': isSilent,
    'source': vinylFile.path
});
```
通过`css.parse`方法获取css中的所有Node，详细的css节点信息请参看[css][css]中的Common properties。
[gulp-css-spriter][gulp-css-spriter]根据解析的Node种的rules，在`map-over-styles-and-transform-background-image-declarations.js`中调用
```javascript
// Go over each background `url()` declarations
transformedStyles.stylesheet.rules.map(function(rule, ruleIndex) {
    if(rule.type === 'rule') {

        rule.declarations = transformMap(rule.declarations, function(declaration, declarationIndex, declarations) {
            // Clone the declartion to keep it immutable
            var transformedDeclaration = extend(true, {}, declaration);
            transformedDeclaration = attachInfoToDeclaration(declarations, declarationIndex);

            // background-image always has a url
            if(transformedDeclaration.property === 'background-image') {
                return cb(transformedDeclaration, declarationIndex, declarations);
            }
            // Background is a shorthand property so make sure `url()` is in there
            else if(transformedDeclaration.property === 'background') {
                var hasImageValue = spriterUtil.backgroundURLRegex.test(transformedDeclaration.value);

                if(hasImageValue) {
                    return cb(transformedDeclaration, declarationIndex, declarations);
                }
            }

            // Wrap in an object so that the declaration doesn't get interpreted
            return {
                'value': transformedDeclaration
            };
        });

    }

    return rule;
});
```
这里只是对rule.type为rule的节点进行了处理，并没有对rule.type为keyframes的节点进行处理，于是将改js修改如下：
```javascript
// Go over each background `url()` declarations
transformedStyles.stylesheet.rules.map(function(rule, ruleIndex) {
    if(rule.type === 'rule') {

        rule.declarations = transformMap(rule.declarations, function(declaration, declarationIndex, declarations) {
            // Clone the declartion to keep it immutable
            var transformedDeclaration = extend(true, {}, declaration);
            transformedDeclaration = attachInfoToDeclaration(declarations, declarationIndex);

            // background-image always has a url
            if(transformedDeclaration.property === 'background-image') {
                return cb(transformedDeclaration, declarationIndex, declarations);
            }
            // Background is a shorthand property so make sure `url()` is in there
            else if(transformedDeclaration.property === 'background') {
                var hasImageValue = spriterUtil.backgroundURLRegex.test(transformedDeclaration.value);

                if(hasImageValue) {
                    return cb(transformedDeclaration, declarationIndex, declarations);
                }
            }

            // Wrap in an object so that the declaration doesn't get interpreted
            return {
                'value': transformedDeclaration
            };
        });

    }

    if(rule.type === 'keyframes') {
        // Get keyframe from keyframes
        rule.keyframes = transformMap(rule.keyframes, function(keyframe, keyframeIndex, keyframes) {
            // Get declarations from keyframe
            keyframe.declarations = transformMap(keyframe.declarations, function(declaration, declarationIndex, declarations) {
                // Clone the declartion to keep it immutable
                var transformedDeclaration = extend(true, {}, declaration);
                transformedDeclaration = attachInfoToDeclaration(declarations, declarationIndex);

                // background-image always has a url
                if(transformedDeclaration.property === 'background-image') {
                    return cb(transformedDeclaration, declarationIndex, declarations);
                }
                // Background is a shorthand property so make sure `url()` is in there
                else if(transformedDeclaration.property === 'background') {
                    var hasImageValue = spriterUtil.backgroundURLRegex.test(transformedDeclaration.value);

                    if(hasImageValue) {
                        return cb(transformedDeclaration, declarationIndex, declarations);
                    }
                }

                // Wrap in an object so that the declaration doesn't get interpreted
                return {
                    'value': transformedDeclaration
                };
            });

        });
    }

    return rule;
});
```
添加对keyframes的解析，keyframes包含keyframe，keyframe包含declarations。
于是fork了源码，修改后，将该issue提交给插件作者[issue6][issue6]，问题解决。
这里漏了个test测试没有写，过几天补上，囧rz。。。


### 结束语

虽然以前也会或多或少提一些issue或改一些代码，但从没有主动贡献过，这是我第一次贡献代码，想想还是有些小激动的。

希望今后能再接再厉，在学习的同时，能够多一些分享，多一些交流！

从今天开始...


[su]: http://ok2471oek.bkt.clouddn.com/dqmmpb/blog/2017-01-20/su.png
[ziiiro]: https://shop290442649.taobao.com/?spm=a1z10.1-c.0.0.BLAlIj
[ziiiro-show]: http://ok2471oek.bkt.clouddn.com/dqmmpb/blog/2017-01-20/ziiiro.jpg
[doyoe]: http://blog.doyoe.com/
[css]: http://css.doyoe.com/
[gulp-css-spriter]: https://github.com/MadLittleMods/gulp-css-spriter
[gulp-css-spriter-usage]: https://github.com/MadLittleMods/gulp-css-spriter#usage
[bezier]: http://www.zhangxinxu.com/wordpress/2014/06/deep-understand-svg-path-bezier-curves-command
[animation]: http://www.cnblogs.com/PeunZhang/p/3685980.html
[issue6]: https://github.com/MadLittleMods/gulp-css-spriter/issues/6
[css]: https://github.com/reworkcss/css