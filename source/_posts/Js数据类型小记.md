---
title: Js数据类型小记
date: 2017-03-22 19:55:17
tags: js 面试题
---
![](http://upload-images.jianshu.io/upload_images/2235778-71242edfe6eef970.png)
console.log(([])?true:false); 
console.log(([]==false?true:false));
console.log(({}==false)?true:false) 
<!--more--><!--more-->

#### 前言

今天在群里看到一道题
```
console.log(([])?true:false); 
console.log(([]==false?true:false));
console.log(({}==false)?true:false) 
得到的结果分别是什么？（） 
A. false true true
B. true true true
C. true false true
D. true true false
```
主要考察数据类型的隐性转换，以及关于“==”类型的比较规则。


#### 开始

##### 第一题
```
console.log(([])?true:false);
```
在这里需要把[]转换为布尔值在进行三目运算。

##### Boolean类型的转换

```
布尔类型的转换只有以下几种返回false,其它都为true
Boolean(undefined) // false
Boolean(null) // false
Boolean(0) // false 包含（-0，+0）
Boolean(NaN) // false
Boolean('') // false

对所有对象，包括空对象以及false对应的布尔对象 new Boolean(false)返回也是true
Boolean({}) // true
Boolean([]) // true
Boolean(new Boolean(false)) // true
```
那么第一题就返回true了。

##### 第二，三题
```
2.console.log(([]==false?true:false));

3.console.log(({}==false)?true:false) 
```
其实我觉得后面返回true和false挺有迷惑作用的，迷惑在不绕弯，前面就做布尔进行比较了 。从上题指导Boolean([])返回的是true，直接做比较 就返回false了。但是
>布尔类型与其它任何类型进行比较，布尔类型将会转换为number类型。

也就是说里我们又加入了Number类型的转换。

关于<strong>Number</strong>类型的转换

首先调用其valueOf方法，如果返回的是一个原始类型的值，那么通过Number函数对其进行调用。-> 结束.
如果返回还是对象，则改为调用对象自身的toString方法，如果返回的是一个原始类型的值，那么通过Number函数对其进行调用。-> 结束。
如果返回的还是对象，那么报错。->结束。
```
// 数值：转换后还是原来的值
Number(324) // 324

// 字符串：如果可以被解析为数值，则转换为相应的数值
Number('324') // 324

// 字符串：如果不可以被解析为数值，返回NaN
Number('324abc') // NaN

// 空字符串转为0
Number('') // 0

// 布尔值：true 转成1，false 转成0
Number(true) // 1
Number(false) // 0

// undefined：转成 NaN
Number(undefined) // NaN

// null：转成0
Number(null) // 0
// []:转为0
Number([]) //0
//{}：转为NaN
Number({}) // NaN

```


这里插一句关于toString的应用。我们可以通过toString来判断数据类型。

>实例对象可能会自定义toString方法，覆盖掉Object.prototype.toString方法。通过函数的call方法，可以在任意值上调用Object.prototype.toString方法，帮助我们判断这个值的类型。

```
console.log([].toString()); // 返回空
console.log(Object.prototype.toString.call([]))
//[object Array]
我们使用typeof 来判断类型时,对于引用对象和数组对象都返回的是Object
typeof []
//"object" 

Object.prototype.toString.call(2) // "[object Number]"
Object.prototype.toString.call('') // "[object String]"
Object.prototype.toString.call(true) // "[object Boolean]"
Object.prototype.toString.call(undefined) // "[object Undefined]"
Object.prototype.toString.call(null) // "[object Null]"
Object.prototype.toString.call(Math) // "[object Math]"
Object.prototype.toString.call({}) // "[object Object]"
Object.prototype.toString.call([]) // "[object Array]"
```

那么 Number([])，返回的是0；Number({}),返回的是NaN。

>简单记一下如果Number方法对象的参数是Object时，返回的就是NaN;

那么二三题，依次返回的就是true和false。

#### 结尾

我只是从题目出发，简单梳理了一下需要的知识点，但是并没有把所有的知识点梳理一遍。
文中部分内容参考于[JavaScript 标准参考教程（alpha）](http://javascript.ruanyifeng.com/)。
这次也解决了我的一个问题，我一直以为null是Object来着。 [笑哭]。

曾经在
```
Number(null) //0
```
null返回0里还当一个特殊点来记。
因为在前文中说到嘛，如果Number方法对象的参数是Object时，返回的就是NaN。
