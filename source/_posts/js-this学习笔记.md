---
title: js this学习笔记
date: 2016-11-16 10:50:36
tags: js
---

![enter image description here](http://www.zaixian-fanyi.com/images/thisthat.jpg)
<!--more--><!--more-->
#### this 指向什么？它从哪来？
>this 不是编写时绑定，而是运行时绑定。它依赖于函数调用的上下文条件，this绑定和函数声明的位置无关，反而和被调用的方式有关。
>this实际上是在函数被调用时建立的一个绑定，指向什么完全由函数被调用的调用点来决定。

```javascript
function ct() {
     //全局作用域
     console.log("funct");
     t();
}

function t() {
   // ct->t
    console.log("funt");
    tct();
}

function tct() {
   // ct->t->tct
     console.log("funtct");
}
ct();
```

#### 默认绑定

```javascript
function ct() {
   console.log(this.a)
}

var a = 2;

ct(); //2
```
这时解析this.a解析成全局变量a.函数ct()是一个很直接，并无修饰应用调用的函数。

在严格模式中：

```javascript
function ct() {
   'use strict';
   console.log(this.a);
}
var a = 2;
ct(); // TypeError: Cannot read property 'a' of undefined
```

#### 隐含绑定

这个我自己理解就是 obj和foo相互之间是挨着的。中间没有缝隙。
想象一下这个状态一对情侣坐在一起，手牵着手。
```javascript
function foo() {
    console.log(this.a);
}
 var obj = {
     a: 2,
     foo: foo
 };

 var a = 3;

 obj.foo(); //2
```
隐含调用的时候,注意最后调用者(obj)的函数属性, ct调用点-》obj调用点。只有对象属性引用链的最后一层是影响调用点的。
想象一下这个状态 ：一个你的同伴(ct)，你(obj)，你的老婆(foo)坐在一起（注意这个顺序）。你们三个人手牵着手。
```javascript
function foo() {
     console.log(this.a);
}
var obj = {
     a: 2,
     foo: foo
 };


 var ct = {
     a: 4,
     obj: obj
 }

 var a = 3;

 ct.obj.foo(); //2
```
#### 隐含的丢失

你和你老婆坐在一起，你老婆想和你牵手，而你拒绝了，那么你老婆就要牵其他人的手拉~~。

```javascript
 function foo() {
     console.log(this.a);
 }       
var obj = {
     a: 2,
     foo: foo
 };

 var bar = obj.foo

 var a = 3;

 bar(); //3
```
在这里 bar 只是调用了obj.foo的引用。
想象一下这个顺序状态：你 =》你的基友 =》你老婆。

参数传递仅仅是一种隐含的赋值，而且因为我们在传递一个函数，它是一个隐含的引用赋值，fn是obj.foo的引用，其实整体意思是和上面的例子是一样的。
```javascript
 function foo() {
	 console.log(this.a);
 }

 function doFoo(fn) {
     fn();
 }

 var obj = {
     a: 2,
     foo: foo
 };

 var a = "oops,global";

 doFoo(obj.foo); //oops,global
```
#### 明确绑定 call apply bind

我觉得每次学习都会刷新对知识的感知，主要就是因为我对知识认识的不足够透彻。

```javascript
function foo() {
    console.log(this.a);
}
var a = 4;
var obj = {
    a: 2
}
foo.call(obj);
```
你需要强制一个函数调用使用某个特定对象作为this绑定，而不在这个对象上放一个函数引用。  通过foo.call()  来明确调用obj，这时this指向obj。

#### 进化

```javascript
  function foo() {
      console.log(this.a);
  }
   var a = 4;
   var obj = {
       a: 2
   }
   var bar = function() {
       foo.call(obj);
   }
   setTimeout(bar, 100); //2
   bar.call(window); //2
```
bar函数被调用时，总是把this强制到obj上，并调用函数foo。

我自己体会了一下这几个例子，感觉挺通透的。

>fun.apply(thisArg[, argsArray])
    thisArg
        在 fun 函数运行时指定的 this 值。 需要注意的是， 指定的 this 值并不一定是该函数执行时真正的 this 值， 如果这个函数处于非严格模式下， 则指定为 null 或 undefined 时会自动指向全局对象（ 浏览器中就是window对象）， 同时值为原始值（ 数字， 字符串， 布尔值） 的 this 会指向该原始值的自动包装对象。
  argsArray
        一个数组或者类数组对象， 其中的数组元素将作为单独的参数传给 fun 函数。 如果该参数的值为null 或 undefined， 则表示不需要传入任何参数。
        apply 与 call() 非常相似，不同之处在于提供参数的方式。
        来源MDN
```javascript
function foo(other) {
      return this.a + other;
}

var obj = {
    a: 2
}
var bar = function() {
    return foo.apply(obj, arguments);
}

var b = bar(10);
console.log(b); //12
```

##### bind的例子
bind()返回一个硬编码的新函数，它使用你指定的this环境来调用原本的函数。
```javascript
function foo(other) {
    return this.a + other;
}

 var obj = {
     a: 2
 }

 var bar = foo.bind(obj);
 var b = bar(11);
 console.log(b); //12
```
##### 这里有个关于Bind的趣味详解

你（son）和你爸（papa）在一块的时候，大家都会知道你爸爸的名字（不知道可以直接问啊！），用代码来说就是：
```javascript
var papa = {
    name: "li gang",
    son: function() {
        return this.name;
    }
};
alert( papa.son() );
```
但是你不和你爸在一块的时候呢？就成了这样：
```javascript
var son = papa.son;
alert( son() );
```
你会发现你大家没办法知道你爸的名字了，然后你就不能为非作歹了。好在你还记得你爸的电话号码，打个电话就又可以兴风作浪又知道了：
```javascript
alert( son.call(papa) );
```
然后你想了想，不行啊，不能我每次兴风作浪的时候都得打我爸电话吧，我爸还不得弄死我。诶！这么笨，我随身带着我爸的身份证复印件不就好了嘛，所以你就成了这样：
```javascript
son = son.bind( papa );
alert( son() );
```
哈哈，爸爸再也不用担心我出门被打叻(๑´ㅂ`๑)！

就这样又过了半年你爸爸大人作死收了一个养子！什么！遗产全部都是我的！养子的中文名叫 二狗子，英文名叫 er~Gou~Zi：
```javascript
function erGouZi() {
    return this.name;
}
```
这个二狗子养子也想知道你爸的名字好出去泡妞把妹歌功颂德，哀求了半天之后，老爸也给了他一份复印件：
```
erGouZi = erGouZi.bind( papa );
alert( erGouZi() );
```
二狗子这货啊表示非常高兴，决定每次报出爸爸的大名的时候都要加上敬爱的三个字，就像我们经常说敬爱的 x 总理 一样，于是他机智的做了一次小手术：
```
erGouZi = function() {
    var prefix = arguments[0] || "";
    return prefix + this.name;
}
erGouZi = erGouZi.bind( papa, "great " );
alert( erGouZi() );
```
又没过多久，这事被老爸知道了。老爸对于二狗子这种舔脚后跟的行为表示非常满意，对他越来越恩爱亲切，对你越来越冷板凳。最后终于老爸把所有的遗产都给了二狗子而你因为平常只会坑蒙拐骗啥技能也不会只能上街乞讨最后被城管暴打而死。

这个关于bind的理解来自于 [如何理解，javascript bind](https://segmentfault.com/q/1010000002508005)感兴趣的可以去溜溜。

这个解答很有意思，也特别容易理解。

#### new绑定

先来定义一下new，来自MDN
>new运算符的作用是创建一个对象实例。这个对象可以是用户自定义的，也可以是一些系统自带的带构造函数的对象。

语法

>new constructor[([arguments])]

参数

>构造函数(constructor)
一个指明了对象类型的函数。
传参(arguments)
一个用来被构造函数调用的参数列表。

当new foo 函数执行时，来看看它做了什么？


>1.一个新对象被创建。它继承自foo.prototype.
2.构造函数 foo 被执行。执行的时候，相应的传参会被传入，同时上下文(this)会被指定为这个新实例。new foo 等同于 new foo(), 只能用在不传递任何参数的情况。
3.如果构造函数返回了一个“对象”，那么这个对象会取代整个new出来的结果。如果构造函数没有返回对象，那么new出来的结果为步骤1创建的对象，ps：一般情况下构造函数不返回任何值，不过用户如果想覆盖这个返回值，可以自己选择返回一个普通对象来覆盖。当然，返回数组也会覆盖，因为数组也是对象。

```javascript
function foo(a) {
	this.a = a;
}

 var a = 12;

 var bar = new foo(2);

 console.log(bar.a); //2
```

tips:new 和apply和call不能连用
也就是说 new foo.call(obj)这样是不行的。

文章学习来源主要来自于[You-Dont-Know-JS](https://github.com/getify/You-Dont-Know-JS)感兴趣的可以去溜溜。 
