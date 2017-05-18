---
title: 小试Mustache，封装自己的样式模板
date: 2017-05-18 17:42:07
tags: js
---


![](http://upload-images.jianshu.io/upload_images/626151-1dfbca9a11951b3f.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
(图片与本文无关)

<!--more--><!--more-->

#### 先啰嗦点别的
说实话，其实现在框架满天飞的时代，做这个感觉也没什么。
但是对我而言，从我自身的角度理解觉得这么做还是挺有必要的，现在前端发展的太快了。
前辈们都是一步一个脚印的探索。我打个比方：可能就像从1数到10，（1,2,3,4,5,6,7,8,9,10）。而我呢（1,5,10）。固然是数到10了，但中间总是差点什么。
就像一栋楼，而我造的就是一栋“危楼“。
也不想过多谈什么大道理，价值观不一样，想法也许就有歧义，我姥爷曾跟我说“这世上除了知识和身体之外都不是真正属于自己的。“我深以为然。 

#### 开始

为什么会有模板的存在，让公共样式有更强复用性，也让代码更加的可读。

这是个是个Header的模板，有三个变量，左边的图标，中间展示的名字，还有右边的图标，在Mustache里，模板接受对象后，通过双括号的方式来进行获取定义的值，
```

<div class="qyui-header jy_theme">
    <div class="qyui-header-left">
        <a class="icon {{left-icon}} f-white f30"></a>
    </div>
    <h1 class="qyui-header-title">{{headName}}</h1>
    <div class="qyui-header-right">
        <a class="icon {{right-icon}} f-white f30"></a>
    </div>
</div>

```
<b>header的数据</b> 传对应的参数给对应的模板,
```
 var headerData = {
       "headName": '我是标题',
       "left-icon": 'icon-29',
       "right-icon": 'icon-81'
   };
    var allTpl = new template();
   allTpl.loadHeader(headerData, "header_wrap");
```

异步调用模板文件，把数据加载进去
```
 loadHeader(userdefind, parent) {
    userdefind["left-icon"] ? '' : userdefind["left-icon"] = 'icon-29';
    userdefind["right-icon"] ? '' : userdefind["right-icon"] = 'icon-81';
    $.get("../template/header.tpl", (tpl) =>
       $("#" + parent).append(Mustache.render(tpl, userdefind))
        
```
做了个图标的默认样式，调用模板文件，在传入的父级ID下载入模板。

#### 看看循环

在Mustache里页面里加了data（data是你自定义的对象数据）,判断Mustache通过判断参数长度来进行渲染，当然循环里还可以在套一个循环，就像下面的other里的

```
 {{#data}}
  <div class="common_search_wrap jy_theme bs">
            <div class="rel wd80 mg_0_at">
                <div class="icon_content_str {{icon-color}}">{{name}}</div>
                <input type="text" class="qyui_input_common {{data-time}}" placeholder="{{placeholder}}" id="{{id}}">
            </div>
            {{#other}}
            <div class="rel wd80 mg_1_at">
                <div class="icon_content_str {{icon-color}}">{{name}}</div>
                <input type="text" class="qyui_input_common" placeholder="{{placeholder}}" id="{{id}}">
            </div>
            {{/other}}
        </div>

   
        <div class="carr_query_botton" id="{{query-id}}">
            <div class="  rel wd80 mg_0_at">
                <div class="qy_buttom {{query-bg}}">
                    {{query-name}}
                </div>
            </div>
        </div>
 {{/data}}
```

数据：循环里在套内部循环，这个外层只渲染了一次，内部由other进行循环。
```
 var searchData = {
                "name": 'D',
                "data-time": 'data-time',
                "placeholder": '查询日期',
                "id": 'begDate',
                "icon-color": 'icon_green',
                "other": [{
                    "name": 'T',
                    "placeholder": '查询站点',
                    "id": 'station',
                    "icon-color": 'icon_orange',
                },{
                    "name": 'F',
                    "placeholder": '其他',
                    "id": 'station22',
                    "icon-color": 'icon_pur',
				}],
                "query-id": "query",
                "query-name": "查询",
                "query-bg": 'icon_green'
            };
```
调用：
```
    var content = {
                "data": searchData
         }
    allTpl.loadSearch(content, 'search_wrap')
   方法：  
   loadSearch(userdefind, parent) {
        $.get("../template/commonSearch.tpl", (tpl) =>
            $("#" + parent).append(Mustache.render(tpl, userdefind))
        )
    }
```

#### 最后
主体思路还是挺简单的，通过Mustache 创建模板，在通过data传参，渲染模板加载到内容。

以前刚学Vue时，学习效率挺低的，有些东西都是死记硬背， 我觉得有时候不管学什么东西，一些原理思想还是要需要明白的，硬上的话程序和自己两败俱伤。

关于Mustache的用法可以去网上搜搜，现在前端工程大部分都用框架了吧，不知道用这种的还多不多。