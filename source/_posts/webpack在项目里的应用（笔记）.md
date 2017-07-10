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


##### 6.30  
六月的最后一天，刚刚好也是星期五。

以前用vue 脚手架，写过一个应用查询的小程序，着实体验了一把单页应用的丝滑柔顺[笑哭]。 记得在页面都是动态引入CSS资源来着，不过那个是单页应用，整体页面引入一个，生成webpack 把JS CSS 一些文件混在一起。

在多页面应用,我尝试把第三方的css文件，JS库和业务逻辑代码分开打包。

common.js // 三方 这些不动的

[name].js //每个页面固定的入口文件

还有一些单个页面定制的css文件，现在想的是把这个也混放在业务逻辑代码包里。

不知道这样合不合理？还有把这个打包除了对缓存，以及减少http请求有优势以外还有其他的优势吗？

##### 使用extract-text-webpack-plugin

目前的状态是，html代码里 有一些文件，并不是配合webpack动态载入的模板文件。打包后的CSS样式就这样加载到了页面的头部，但是当用户启动页面时，已经加载html文件了，也就是说，第一眼看到的html文件没有样式的。

使用extract-text-webpack-plugin插件可以独立打包css文件

安装
npm install extract-text-webpack-plugin --save-dev 

配置
```
  rules: [{
            test: /\.css$/,
            use: ExtractTextPlugin.extract({
                fallback: "style-loader",
                use: "css-loader"
            })
    }]
 plugins: [
        new ExtractTextPlugin({ filename: 'css/[name].css', allChunks: true })
    ]
```
allChunks 的作用是可以把加载的多个CSS文件合并为一个css文件。

##### 阶段想法
就目前而言，感觉就像是把法拉利的材料拼接成了一个金杯。
从页面访问角度来讲，初见成效，代码维护也相对更好维护。
但是里面使用的plugins，loaders，并没有做到深层次的挖掘，这是下一阶段需要做的学习内容。

#####  7.4  url-loader

使用了url-loader，把对应小的图片转为base64的形式。
```
import img from '../../img/icon_to.png';

对应的Loader
{
        test: /\.(png|jpg|gif)$/,
         use: [{
             loader: 'url-loader',
             options: {
                 limit: 8192
             }
         }]
 }
```

这里有一篇单图，雪碧图，base64图片的对比分析。大致看了一下，不明觉厉，准备详细看看[前端图片引入方式神演算.
](http://www.jianshu.com/p/486fa240a3a7)

相比传统页面，我测试了目前的页面状态，使用webpack动态引入图片，相比载入的时候更耗时间。

老实说，看的越多，越感觉一团乱遭，刚刚尝试使用了require.ensure()拆分代码，按需去加载模块，流程明白为什么去这么做，但是这个demo效果没有到达期望的效果，页面载入时间比以前更长了。

##### 压缩JS CSS代码

用ExtractTextPlugin把所有的文件打包为一个CSS文件，然后压缩.但是每次压缩后页面有些样式就挂了[滑稽].目前还没找到原因。
```
 //压缩css配置
 {
     test: /\.css$/,
     use: ExtractTextPlugin.extract({
         fallback: "style-loader",
         use: [{
             loader: 'css-loader',
             options: {
                 minimize: true }
             }
         }]
     })
 }
```

压缩JS代码，webpack 已经内嵌了uglifyJS无需额外引用插件，但是好像对es6代码压缩不了。
```
new webpack.optimize.UglifyJsPlugin({ 
		compress: {
		 warnings: false
		},
		except: ['$', 'jQuery', 'require', 'exports', 'import']
})
```
except :排除关键字
