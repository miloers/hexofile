---
title: 学习Vuex写的一个小项目（我也不知道起个什么标题）
date: 2017-04-08 12:35:46
tags:  Vuex Js
---

![](http://upload-images.jianshu.io/upload_images/2071553-340fceadb00a383f.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
这篇文章就说说Vuex。
<!--more--><!--more-->

#### 前言

上次学习Vue,然后尝试在项目里封装了一个小项目模块，流程不复杂，感觉效果还不错。
这次写个demo主要为了学习一下Vuex,本来想在官方找随便找2个例子打打算了，但一开始看时，没怎么塌下心来，搁置了一段时间之后，这次找回来，写一个简单的小项目来拓展一下。

而且以后再拓展新东西，就直接放到这个项目里了。

这也是一个渐进式的学习demo。



#### 开始

一个知乎的热门新闻。
![](http://cgewx.img48.wal8.com/img48/565731_20170206100721/149162225727.png)
![](http://cgewx.img48.wal8.com/img48/565731_20170206100721/149162536412.png)
这是首页和详情页。
引入了mint-ui，目前只引入了header和轮播。
将来可能使用这个框架，这里简单使用一下。
正文内容使用flex进行布局。

首页的内容通过Vuex 来管理内容。
不过多的解释它是什么，我就说说我的看法。
它就是个内容管理器，分发数据，获得数据集。
为什么用它？
其实在项目里，你完全可以一口气写下来，一些轻量级别的项目完全不需要引入它。
我讲讲我对它的理解：
假如你是一个很小的餐厅老板，假设你每天干四件事，买菜，烹饪，刷碗，数钱。
人流量不是很多，ok，你自己可以完全胜任。虽说你可能很累。
但是，当你的餐馆做大了，你发现 你没这么多精力了。
你需要聘请员工来帮你，你找一个买菜的，找了一个做饭的，找了一个刷碗的，找了一个财务。
让更专业的人去做这些事。
这时，就把任务分发出去了。
来看看代码：
```
   export default {
        name: 'app',
        components: {
            Header,
            indexList,
            tag,
            qyfooter,        
        },
        created(){
            this.getList();
        },
        computed:{
            ...mapGetters(['DONE_HOT_NEWS'])
        },
        methods:{
            getList:function(){
                this.$store.dispatch('FETCH_HOT_NEWS')
            }
        }
    }
```
created钩子（实例已经创建完成之后被调用）里调用了getList这个方法，分发事件FETCH_HOT_NEWS.
<strong>这就相当于 天刚刚亮的时候，该去买菜了。</strong>

```
const actions = {
    [types.FETCH_HOT_NEWS]({ commit }) {
        axios.get(BIRD + 'http://news-at.zhihu.com/api/3/news/hot')
            .then(res => {
                console.log(res.data);
                commit(types.TOGGLE_HOT_NEWS, res.data.recent)
            })
            .catch(err => console.log(err));
    },
    [types.FETCH_NEWS_DETAIL]({ commit }, conurl) {
        axios.get(BIRD + conurl)
            .then(res => commit(types.TOGGLE_NEWS_DETAIL, res.data))
            .catch(err => console.log(err));
    }
}
```
网络请求使用了axios，BIRD是一个反向代理，从这获取数据。
<strong>你选好每天必要的菜品</strong>
```
const mutations = {
    [types.TOGGLE_HOT_NEWS](state, all) {
        state.HotNews = all;
    }
}
```
<strong>老板结账。这个过程是个异步的过程，但是如果都写在mutaions显得臃肿。</strong>

```
const getters = {
    [types.DONE_HOT_NEWS]: state => {
        return state.HotNews
    },
    [types.DONE_NEWS_DETAIL]: state => {
        return state.NewsDetail
    }
}
```
<strong>这时，你拿着菜回菜馆了。</strong>

获取数据
```
template	
   <div v-for=" item in DONE_HOT_NEWS">
        <indexList :src="item.thumbnail |changeBird" :title="item.title" :conurl="item.url"></indexList>   
    </div>

script 
 import {mapGetters} from 'vuex'
 
 computed:{
            ...mapGetters(['DONE_HOT_NEWS'])
        },
```

在vuex里引入mapGetters，获得DONE_HOT_NEWS 这个数据集，然后再template里引入循环。
在浏览器查看一下状态，当我们代码没有达到预期状态时，来看看Vuex的状态。从哪里挂掉了，有助于减少时间理清头绪。
![](http://cgewx.img48.wal8.com/img48/565731_20170206100721/149162577213.png)
![](http://cgewx.img48.wal8.com/img48/565731_20170206100721/149162577278.png)

来看看官方的一个图![](https://vuex.vuejs.org/zh-cn/images/vuex.png)

这就是我理解的一个Vuex的一个过程。

多余的代码就不贴了，这次就简单总结一下Vuex的学习笔记。

#### 最后
[代码地址](https://github.com/miloers/zhihu)在这，
如果你正好需要学习Vuex
如果你觉得其它的学习项目里琳琅满目的功能而导致你呼吸急促无心恋战，
我想它现在在适合你不过，不过如果你不是近期看的话，请注意提交记录。
最后如果能帮助到你，那最好不过了。
学习之中，难免有错，希望不吝赐教，共同进步。


项目里的所用的api来自[知乎日报api分析](https://github.com/izzyleung/ZhihuDailyPurify/wiki/%E7%9F%A5%E4%B9%8E%E6%97%A5%E6%8A%A5-API-%E5%88%86%E6%9E%90) Edit By [izzyleung](https://github.com/izzyleung)

使用vue-cli，vue,vue-router，axios全家桶，进行编码开发。