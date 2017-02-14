---
title: vue-router2.0基础
date: 2017-01-22 15:23:51
tags: vue vue-router
---

最近在踩vue的坑,这篇是关于vue-router基础的~~
不过说实在的,也是再重复造轮子,vue-router官方的手册已经很全面了.
<!--more--><!--more-->

####  前言
用Vue+vue-router 很方便的就能构建单页应用，依靠vue-router把各个组件联系在一起。

####  最基础的vue-router用法
 
```
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <script src="https://unpkg.com/vue/dist/vue.js"></script>
    <script src="https://unpkg.com/vue-router/dist/vue-router.js"></script>
</head>

<body>
    <div id="app">
        <h1>hello world!</h1>
        <div>
            <!--它默认会被渲染成一个带有链接的a标签，通过to属性指定链接地址-->
            <router-link to="/foo">Go to foo</router-link>
            <router-link to="/bar">Go to bar</router-link>
        </div>
        <div style="color: darkcyan;">
            <!-- 用于渲染匹配的组件。-->
            <router-view></router-view>
        </div>
    </div>

    <script>
        /*定义组件*/
        const Foo = {
            template: '<div>foo template!</div>'
        }
        const Bar = {
                template: '<div>Bar template!</div>'
            }
            //绑定组件到路由，设置路径
        const routes = [{
                path: '/foo',
                component: Foo
            }, {
                path: '/bar',
                component: Bar
            }]
            //创建路由实例
        const router = new VueRouter({
                routes: routes
            })
            //挂载根实例
        const app = new Vue({
            router
        }).$mount('#app')
    </script>


</body>

</html>
```

`<router-link>` 是一个组件，它默认会被渲染成一个带有链接的`a`标签，通过to属性指定链接地址。当点击对应路由的链接时，为该链接自动添加`router-link-active`样式
来看看它的属性值，
##### to
表示目标路由的链接。这是一个必须的属性值，当被点击后，跳转到对应的目标对象。

##### replace
布尔类型，默认值为false.设置replace，点击后，导航不会留下history记录。从路径上相当于无痕浏览吧，在浏览器内点击返回时，没有返回目标。

```
 <router-link to="/foo" replace></router-link>
```
##### append
布尔类型，默认值为false。路径叠加。从路径a跳转到路径b，如果配置了append，路径变成 /a/b, 相反，路径为/b。
```
 <router-link to="/foo" append></router-link>
```

##### tag
字符串类型，看名字就知道是渲染的标签嘛，如果不配置tag属性，则默认渲染为`a`标签，
否则
```
 <router-link to="/foo"  tag="li"></router-link>
```
将渲染为
```
<li></li>
```

#### 动态路由匹配
使用动态路径参数来映射不同的参数。我理解就是不同的人持着自己的身份ID证件访问同一个组件，来获得属于自己的身份ID。

```
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <title>动态匹配路由</title>
    <script src="https://unpkg.com/vue/dist/vue.js"></script>
    <script src="https://unpkg.com/vue-router/dist/vue-router.js"></script>
</head>

<body>
    <div id="app">
        <h1>hello world!</h1>
        <div>
            <router-link to="/user/foo/post/111">Go to foo</router-link>
            <router-link to="/user/bar/post/222">Go to bar</router-link>
        </div>
        <div style="color: darkcyan;">
            <router-view></router-view>
        </div>
    </div>

    <script>
        /*定义组件*/
        const User = {
                template: '<div>User{{$route.params.id}},post{{$route.params.postid}}</div>',
            }
            //绑定组件到路由，设置路径

        const routes = [{
            path: '/user/:id/post/:postid',
            component: User
        }]

        const router = new VueRouter({
            routes
        })

        const app = new Vue({
            router
        }).$mount('#app')
    </script>


</body>

</html>
```
动态的路径属性，以`：`标记。当路由被匹配时，参数值设置到`this.$route.params`，可以在组件内使用。
在项目里，路由可设置多个路径参数，对应的值都会设置到$route.params中。还可以自定义匹配正则。
[这里有个例子怎么匹配正则。](https://github.com/vuejs/vue-router/blob/next/examples/route-matching/app.js)


#### 嵌套路由
我觉得就是一个路由下，子路由页面之间的切换。
定义路由的这个路径我老是写错，浏览器也不报错，很是郁闷。我记得前些日子写Vue单页demo的时候，第一次正常访问的时候，从父组件里点子路由组件，可以访问子路由页面，在不刷新页面的情况下，再次点击的时候，子路由页面怎么也渲染不出来。现在估计就是路径写错了吧。
```
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <title>嵌套路由</title>
    <script src="https://unpkg.com/vue/dist/vue.js"></script>
    <script src="https://unpkg.com/vue-router/dist/vue-router.js"></script>
</head>

<body>
    <div id="app">
        <router-link to="/user/foo">foo</router-link>
        <router-link to="/user/foo/profile">profile</router-link>
        <router-link to="/user/foo/posts">posts</router-link>
        <hr>
        <router-view></router-view>
    </div>
    <script>
        const User = {
            template: `     <div class="user">       <h2>User {{ $route.params.id }}</h2>       <router-view></router-view>     </div>   `
        }
        const home = {
            template: '<div>Home</div>'
        }
        const profile = {
            template: '<div>Profile</div>'
        }
        const posts = {
            template: '<div>Posts</div>'
        }
        const routes = [{
            path: '/user/:id',
            component: User,
            children: [{
                path: '',
                component: home
            }, {
                path: 'profile',
                component: profile
            }, {
                path: 'posts',
                component: posts
            }]
        }]

        const router = new VueRouter({
            routes
        })

        const app = new Vue({
            router
        }).$mount('#app')
    </script>
</body>

</html>
```
#### 编程试导航

类似于window.history的用法
`router.go()`
`router.push()`，它就是用来导航到不同的链接，等同于`<router-link to=""></router-link>`。
方法的参数：
```
router.push('home')
router.push({path:'home'})
router.push({name:'user',params:{userId:aa}})
//带参数的路由，/register?plan=private
router.push({path:'register',query:{plan:'private'}})
```

补充一下编程试导航
```
  methods: {
            query() {
                if (!this.number && !this.name) {
                    alert("必须输入一个条件！")
                } else {
                    this.$router.push({
                        name: 'Two',
                        params: {
                            number: this.number,
                            name: this.name
                        }
                    })
                }
            }
        }
```
通过这个，页面跳转到Two页面后，Url是不带参数的，一开始我以为类似于get方法，参数在外面。（不排除在该方法内可以把参数暴露的方法）
name：Tow，这个Two必须在路由里，把名称标记上，我第一次时忘了，我记得报错信息是没有找到Tow这个路径
```
{
    path: '/two',
    component: Two,
    name: 'Two'
}
```
至于怎么在跳转的页面里接受参数，

```
  export default {
        name: 'com2',
        data() {
            return {
                loading: false,
                post: null,
                error: null,
                name: '',
                number: ''
            }
        },
        created() {
            // 组件创建完后获取数据，
            // 此时 data 已经被 observed 了
            this.fetchData()
        },
        watch: {
            // 如果路由有变化，会再次执行该方法
            '$route': 'fetchData'
        },
        methods: {
            fetchData() {
				//获得参数
                this.name = this.$route.params.name;
                this.number = this.$route.params.number;

            }
        }
    }
```



####  命名试图

一个页面下，展示多个同级的组件。这样增加了组件的灵活性和复用性。
需要注意的是需要在定义router-view的时候给它定义身份标示。
```
<router-view name="footer"></router-view>
```
footer就是他的身份标示。如果name为空，则为default
```
const routes = [{
	path:'/user',
	componetns:{
		default:main,
		b:footer
 }
}]
```
#### 重定向
顾名思义，重定向就是我们访问页面a的时候，定向访问到了b页面。重定向的目标可以是路径，可以是个命名的路由，也可以是个方法。[可以参考这里。](https://github.com/vuejs/vue-router/blob/next/examples/redirect/app.js)


#### 02-04补


#### 参考文档
[参考文档。](http://router.vuejs.org/zh-cn/) 







