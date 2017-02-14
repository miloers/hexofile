---
title: Hexo + Next 遇到的小坑
date: 2016-11-09 14:24:35
tags: Hexo
categories: blog 
---

![alt text](http://7xjh3u.com1.z0.glb.clouddn.com/new-hexo-1.jpg)


### 前言

趁着热乎劲，把自己的坑记录下来。网上的教程N多，很详细，就不多说了。这篇就记录下自己遇到的小问题及几个常用命令。
<!--more--><!--more-->

### 正文

关于 hexo 的常用命令：
``` hexo
hexo new "postName" #新建文章
hexo new page "pageName" #新建页面
hexo generate #生成静态页面至public目录
hexo server #开启预览访问端口（默认端口4000，'ctrl + c'关闭server）
hexo deploy #将.deploy目录部署到GitHub
```

简写 ：
``` hexo
	 hexo n === hexo new，新建文章
	 hexo g === hexo generate，用于生成静态文件
	 hexo s === hexo server，本地预览
	 hexo d === hexo deploy，用于将本地文件发布到github上
	
```

小坑：

```javascript

You should configure deployment settings in _config.yml first!

Available deployer plugins:

For more help, you can check the online docs: http://hexo.io/
```

我的坑就是这个：

引起的可能

```yml   
    _config.yml 文件 少了一个空格的原因
    deploy: 
		  type: git
		  repository: http://github.com/miloers/miloers.github.io.git
		  branch: master
	冒号后面都需要有一个空格的
```

我当然不是这种错，按别人教程走的，别人特意提起过。[害羞]
当我上传到github上，准备浏览时，一直显示404 found，尼玛，我都等了10多分钟，你就一直给我显示这个 [愤怒] [懵逼]。

> 仓库名必须为 http://your_user_name.github.io 。
> 用我的举例：我的用户名为miloers ，而仓库名写的miloer。这样就不行，不行[委屈].
>更多的问题可以去这里看看 [来自知乎的小坑](https://www.zhihu.com/question/30670216)。


17.1.28(更新)

起因：换了台电脑，然后再新的电脑配置博客嘛
过程：
我的方法可能比较蠢，但是比较简单。
1）先把所有的东西push到另一个仓库里
2）本地clone过来
3）安装模块
发生了错误：WTF？
```
$ npm install
npm WARN deprecated swig@1.4.2: This package is no longer maintained
npm WARN optional SKIPPING OPTIONAL DEPENDENCY: fsevents@^1.0.0 (node_moduleschokidarnode_modulesfsevents):
npm WARN notsup SKIPPING OPTIONAL DEPENDENCY: Unsupported platform for fsevents@1.0.17: wanted {"os":"darwin","arch":"any"} (current: {"os":"win32","arch":"x64"})
npm ERR! Windows_NT 6.1.7601
npm ERR! argv "D:\Node\node.exe" "D:\Node\node_modules\npm\bin\npm-cli.js" "install"
npm ERR! node v7.4.0
npm ERR! npm v4.0.5

npm ERR! shasum check failed for C:UsersdellAppDataLocalTempnpm-3568-bffa2b65registry.npmjs.orgyargs-yargs-3.5.4.tgz
npm ERR! Expected: d8aff8f665e94c34bd259bdebd1bfaf0ddd35361
npm ERR! Actual: 46f08eb94d7130ad575d5d58ccb38510ae532d9a
npm ERR! From: https://registry.npmjs.org/ya...
npm ERR!
npm ERR! If you need help, you may report this error at:
npm ERR! https://github.com/npm/npm/is...

npm ERR! Please include the following file with any support request:npm ERR! F:mydatahexonpm-debug.log
```
上网问了一下，也没有解决方法
当时我的电脑在安装更新，然后报错了
之后重启了一下
莫名其妙就行了
不知道是不是和更新内容有关
Windows系统。。
一脸懵逼

4）然后安装Next主题
因为clone过来时，内容都是空的
5）完成
回来准备试试这个[Hexo的版本控制与持续集成](https://formulahendry.github.io/2016/12/04/hexo-ci/)
17.1.28(更新完)

关于NEXT：

至于NEXT主题，我想没有比它官方文档介绍的更完美的了。地址戳[这里这里](http://theme-next.iissnan.com/getting-started.html).

### 参考

[使用Hexo搭建个人博客(基于hexo3.0)](http://opiece.me/2015/04/09/hexo-guide/)
[hexo你的博客](http://ibruce.info/2013/11/22/hexo-your-blog/)
