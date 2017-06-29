---
title: webpack在项目里的应用（笔记）
date: 2017-06-28 11:52:56
tags: js webpack 笔记
---
![alt text](http://upload-images.jianshu.io/upload_images/3354589-aeaa8cbe30addeae.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

<!--more--><!--more-->

#####  前言

以前用webpack配着Vue全家桶做过单页应用。这次准备在多页应用里使用webpack打包程序。

##### 正文

可能是刚刚踩过坑就过来用了，能应用的知识点不是很多，凭着一腔热血就滚过来想在项目里试试。

昨天尝试着在项目里把首页给打包了，结果没想到异常的顺利。


打包过程是分着踩坑式打的，原来的主页文件大约加载了公共JS 1个，加密JS2个，还有一个jQuery。

jQuery 版本支持模块的导出，很方便弄，但是加密的2个JS连npm都不支持，别提支持模块导入导出了，怎么办？
一开始 ，我只把我写的JS 和公共类以及jQuery打包，加密的JS还是暴露在外面，试着运行一下，可以运行，但是还是暴露在外面，还用webpack 有什么用？
感觉不伦不类的。

接着，看了看2个加密的JS代码，折腾了半小时，把对应使用模块导出了，试着运行，可以运行。

今天呢？尝试着在打包一个页面，可是加载了更多的JS，还像昨天一样，我把能打包的先打包，不能的JS暴露在页面全局。但是令我奇怪的事情发生了，打包的文件里，用不了全局的JS插件。
但是在我昨天的印象里，暴露在全局的JS，打包后的JS应该是能访问里面的。接着，我把用到的外面的JS文件放到外面，能正常访问了。


##### 任务

今天有时间会尝试着把代码分割开来，理解问题出现的原因，寻找解决问题的方法，然后尝试着把全局的JS也打包进来。

昨天我看帖子有一句话，webpack更适合做单页页面应用，多页应用更适合用glup来做，是这样吗？

##### 最后放一张webpack的整体流程图
![alt text](https://img.alicdn.com/tps/TB1GVGFNXXXXXaTapXXXXXXXXXX-4436-4244.jpg)

#####  6.29
打包完文件是局部的，html代码在使用时 会报$ undefined

```
test.html
<script src="./dist/js/test.js"></script>
<script>
    $(function() {
        $("#fdas").text("22");
    })
</script>
```

test文件里import jQuery，在test.html 在使用jQuery时，就报错了。

######  6.29 下午

理解的有一些偏差，[手动滑稽]。
感觉webpack比自己想象的强大的多。

我模糊的以为引用第三方的JS 给需要export这种模块，其实并不需要，昨天提到需要引入2个JS插件，今天require()引入进来就万事大吉了。
这样好像就用不了代码分割的功能了，我其实一直把这个放在心头来着。其实想想像这种第三的库，又不会经常改动，何必要跟逻辑代码混在一起呢？

今天查了查资料，对这种常引用的第三方库，用CommonsChunkPlugin打包公共代码，打包的JS文件，然后在把这个静态资源做缓存 ，它有一个minChunks属性，我觉得很有趣，这个是判断公共代码的判断标准，某个js模块被多少个chunk加载了才算是公共代码。
