---
title: canvas将图片转化为base64
date: 2016-12-23 14:16:20
tags: work
---
![](http://p1.bpimg.com/567571/f5b4199c731650b6.jpg)
<!--more--><!--more-->
### 前言
同学看别人源码，发现img资源一堆乱七八糟的大长串字符，截图问我是什么？我说应该是base64编码，然后8l8l跟他随便说说。

### 正文

好处是什么？
我觉得长时间展示不变的图片可以利用localStorage往浏览器里存啊。
减少HTTP请求。
当然，就像我朋友说的，图片属性过长，CSS大小也会有影响。浏览器端IE8以上好像大多都支持了。

### 方法
从网上看了看，有通过FileReader获取图片的base64。
我使用canvas转的，说说这个方法的实现。

```
 var img = "http://7xib7m.com1.z0.glb.clouddn.com/img/article/2016/12-14/1-UuKf1FDEJp3bF96aY3KEqg.png";

 function getCanvas(img, style) {
      var canvas = document.createElement("canvas");
      var imgContext = canvas.getContext("2d");
      canvas.width = img.width;
      canvas.height = img.height;
      imgContext.drawImage(img, 0, 0, img.width, img.height);
      var canvasUrl = canvas.toDataURL("image/" + style);
      return canvasUrl;
  }

  function setLocal(key, value) {
      localStorage.setItem(key, value);
  }

 var imgStyle = img.substring(img.lastIndexOf(".") + 1).toLowerCase();
    var image = new Image();
    image.crossOrigin = '';
    image.src = img;
    image.onload = function() {
        var canvasImg = getCanvas(image, imgStyle);
        console.log(canvasImg);
        setLocal('t1', canvasImg);
    }
```
从网上随便找了张图片，
获得图片的信息，建一个canvas对象，并把图片信息的属性通过canvas重构了一遍，再通过canvas.toDataURL方法转换为base64编码。
最后通过localStorage，把内容存在本地。
![](http://i1.piimg.com/567571/108ee2a04c7f42cc.jpg)
我从浏览器取了一下。
不过这个localstorage有5M的使用上限，过度使用的话，还是要注意一点的。
### 最后
看了几篇别人的博客发现了几个小问题：
报错信息：
![](http://p1.bpimg.com/567571/4e62803c98ffa172.jpg)
如果是这种问题，在代码中
```
image.crossOrigin = '';
```
这是引用外部图片引起的跨域问题。

报错信息：
![](http://p1.bpimg.com/567571/f033a8f2a6eab8a7.jpg)
这是从别人的博客里，有网友提问的，好像博主也没有回答清楚。
我查了一下，这是由图片方的服务器不允许跨域访问造成的。
看这张图
![](http://p1.bpimg.com/567571/8c625f48038bfd50.jpg)
一般有这个属性的，一定可以成功的。

最后，安利一个 [本地图片生成base64的网址。](http://www.pjhome.net/web/html5/encodeDataUrl.htm)