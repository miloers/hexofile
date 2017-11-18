---
title: '再度进击的Mustache '
date: 2017-11-18 12:49:36
tags: js template 
---
数据驱动模型，你们的业务页面是怎么生成的呢？

![进击](http://upload-images.jianshu.io/upload_images/2414563-d8a1a2f8e1d26e06.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/700)
<!--more--><!--more-->




### Mustache初步的使用

关于[Mustache](https://github.com/janl/mustache.js) .
最早我是怎么使用的？出于什么目的使用的呢？

接口返回一个类似于这样的数据。（简书随便抽了一个。）
```
{"chapters":[{"title":"《假三国》目录","slug":"ddb4936a75d0","wordage":683},{"title":"《假三国》：霸气的开场，走心的套路","slug":"890d748caf26","wordage":1052},{"title":"《假三国》：谁识英雄是白身？","slug":"cc579990cbe2","wordage":1077},{"title":"《假三国》：普通人身居高位的悲剧","slug":"d4956542e231","wordage":1947},{"title":"《假三国》：留得生命在，万事皆可为","slug":"f28fc45f2737","wordage":1609},{"title":"《假三国》：资历尚浅，瞧不起你又能怎样？","slug":"545144530cd8","wordage":1393},{"title":"《假三国》：曹操又没死，真可惜","slug":"7ab36ad8419c","wordage":1445},{"title":"《假三国》：身外之物，怀璧之诛","slug":"a8dbc95f3289","wordage":1724},{"title":"《假三国》：我躺在别人床上爱你","slug":"1ee824581694","wordage":1745},{"title":"《假三国》：为了“英雄”这个称号，我再一次杀了我爹","slug":"38920c5fa608","wordage":1557},{"title":"《假三国》：试图交友反结怨","slug":"5a7f0ce7d786","wordage":1598},{"title":"《假三国》：同为道义，太史慈出力，刘备捡便宜","slug":"a67a83c396c4","wordage":1719},{"title":"《假三国》：刘备得徐州，仅仅是人品好？","slug":"2b5b168e18eb","wordage":1603},{"title":"《假三国》：三国中论吃苦，我只服汉献帝","slug":"eaa878009705","wordage":1799},{"title":"《假三国》：张飞喝酒误事，错失徐州","slug":"b52fe413af44","wordage":1561}],"total_count":32}
```
假定我们想循环对应的title和 slug
```
let str  = '';
for(let i;i<total_count;i++){
	 str+="标题："+chapters[i].title+"slug:"+chapters[i].slug;
} 
```
这个循坏出来是没有任何样式的，项目里还要把这些做样式啊。 在流程里又粘合了一堆这个样式，甚至还有一些attr属性什么的，想想就恶心。

就是在这个基础上用上了Mustache。
后台定义一个模板
```
{{#data}}
  <div class="title">
	  <span class="titleText">{{title}}<span>
 </div>
 <div class="slug">
	 <span class="slugText">{{slug}}<span>
 </div>
{{/data}}

```
只需要调用Mustache.to_html(数据，模板)就可以生成对应的HTML了。

诶，这个最起码不耦合在一起了，不显得黏黏糊糊了。


### 进步一点的Mustache

当然我们的数据不可能每次都是这种规律性质的。

假如返回类似于这种
```
[
  {
    mobile:"f23132131231"
  },
  {
   QQ:"123132131"
  },
  {
   wechat:"sfsdfas"
  }
]
```
这种数据怎么办?

这么写的【捂脸】
```
  <div class="mobile">
	  <span class="mobileText">{{mobile}}<span>
 </div>
 <div class="QQ">
	 <span class="QQText">{{QQ}}<span>
 </div>
 <div class="wechat">
	 <span class="wechatText">{{wechat}}<span>
 </div>
```
我们又做了一些看似重复的工作，但是他们返回的值的key是不同的啊。

试想一下，我们假如有一个初始的数据
```
const DATA =[{
	id:"wechat",
	name:"wechat",
	...
},{
	id:"QQ",
	name:"QQ",
	...
},
{
	id:"mobile",
	name:"mobile",
	...
}]

```
这里...可以是其他初始化的属性。
我们返回数据后，通过返回的key值，动态的往DATA这个初始的数据里插入对象的value属性。

这时我的模板就可以变成这样了

```
{{#data}}
  <div class="统一样式">
	<span class="text">{{name}}<span> 
    <span class="text">{{value}}<span>
 </div>
{{/data}}
```
这样又是批处理数据了。这样模板就可以复用了，不用像刚刚那样生成一堆处理数据的模板文件。

同理，当我们主动生成数据时，通过刚刚那个DATA生成就可以了。（被动生成，这里指接受数据后，根据数据生成Html文件）。

其实做到这里，我觉得关键的部分在于初始的DATA，然后根据DATA，来画出对应的可复用的模板。 
如果DATA，写不好的话，模板文件就会很多，而且重复的很多，很乱。
换句话说就是一将无能，累死三军。
典型的案例就是文图拉，意大利就是这种境况。造成的结果就是没进世界杯。


### 再进步一点的Mustache

做到这里，我觉得还不错，但是结合业务又发现一个问题。

这个问题，是由主动生成数据带来的。

我们业务里不可能都是 Input 一种文件吧，

举一个例子，我们注册时，

假如有昵称，性别，邮箱，是否有推荐人。
在这个信息里  Input 对应（ 昵称，邮箱）。Select 对应（性别） 。checkBox对应（是否有推荐人）。

你总不能摘着弄吧？批量自动生成数据顺序变成 昵称 邮箱 性别 是否有推荐人。 性别变到邮箱后面了。

你这么弄上去的话，信不信产品一口狗血吐你脸上。

那怎么办？

把模板单一化，让每一个元素分发到模板挨个生成？

说人话！！

还记得在上一小节最后，我自己总结，最重要的就是DATA这个驱动模板的数据了，类似于将的功能。

<strong>在初始化的DATA里，你可以设置一个对应的模板属性，当我们生成HTML时，数组的每一个元素挨个遍历，就去找这个模板，然后生成HTML。</strong>

就像这样：
```
const DATA =[{
	id:"wechat",
	name:"wechat",
	tpl:"commonInput"
	...
},{
	id:"sex",
	name:"sex",
	tpl:"commonSelect"
	...
},
{
	id:"mobile",
	name:"mobile",
	tpl:"commonInput"
	...
},
{
	id:"yes",
	name:"yes",
	tpl:"commonCheckBox"
	...
}]

```
这个是节选挨个生成模板的代码 ,通过reduce累加Html, 
两个参数分别是初始化的DATA和模板文件的key值。
```
  reduceHTML(array, attr) {
       let todo = new dynamicCreateData();
        let html = array.reduce(function (prev, cur) {
           todo.regString(cur[attr]);
            temp = Mustache.to_html(eval(cur[attr]), cur);
             return prev + temp;
        }, '')
        return html;
     }
```

### 总结

Mustache 还有很多其他的功能，这里只是简单把业务里用到的一个功能，纵向的说说，我想用Mustache做到这里，大部分需求应该都能满足了。
