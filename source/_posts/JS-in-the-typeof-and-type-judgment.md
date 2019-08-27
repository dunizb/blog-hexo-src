---
title: JS中的typeof和类型判断
date: 2017-07-08 13:59:00
categories:
- 技术
tags:
- 前端
- JavaScript
description: "这篇文章讲述typeof运算符判断基本类型和引用类型的区别，以及怎么判断数组类型和空对象"
---

## 摘要

这篇文章讲述typeof运算符判断基本类型和引用类型的区别，以及怎么判断数组类型和空对象

## typeof

ECMAScript 有 5 种原始类型（primitive type），即 Undefined、Null、Boolean、Number 和 String。我们都知道可以使用typeof运算符求得一个变量的类型，但是对引用类型变量却只会返回`object`，也就是说typeof只能正确识别基本类型值变量。
```js
var a = "abc";
typeof a;// "string"

var b = 123;
typeof b;// "number"

var c = true;
typeof c;// "boolean"

var d = null;
typeof d;// "object"

var f = undefined;
typeof f;// "undefined"

var g;
typeof g;// "undefined"
typeof x;// "object"
```

您也许会问，为什么 typeof 运算符对于 null 值会返回 "object"。这实际上是 JavaScript 最初实现中的一个错误，然后被 ECMAScript 沿用了。现在，null 被认为是对象的占位符，从而解释了这一矛盾，但从技术上来说，它仍然是原始值。

最后一个比较奇怪，typeof一个不存在的变量`x`居然返回了"object"而不是"undefined"。

我们在来如下代码
```js
var a = function() {};
typeof a; // "function"

var b = [1,2,3];
typeof b; // "object"

var c = {};
typeof c; // "object"
```
对于数组和对象都返回"object"，因此我们日常开发中一个常见需求就是如何判断变量是数组还是对象。

## 类型判断

类型判断，一般就是判断是否是数组，是否是空对象。这是针对这个需求，我日常用过或见过的判断方法

**判断是否是数组**
有数组：`var a = [1,2,3,4,5];`
方法一：
```js
toString.call(a); // "[object Array]"
```
方法二：
```js
a instanceof Array; //true
```
方法三：
```js
a.constructor == Array; //true
```
第一种方法比较通用，也就是`Object.prototype.toString.call(a)`的简写。

`instanceof`和`constructor`判断的变量，必须在当前页面声明的，比如，一个页面（父页面）有一个框架，框架中引用了一个页面（子页面），在子页面中声明了一个a，并将其赋值给父页面的一个变量，这时判断该变量，`Array == object.constructor`会返回`false`；

**判断是否是空对象**
有变量：`var obj = {};`
方法一：
```js
JSON.stringify(obj); // "{}"
```
通过转换成JSON对象来判断是否是空大括号

方法二：
```js
if(obj.id){
   //如果属性id存在....
}
```
这个方法比较土，大多数人都能想到，前提是得知道对象中有某个属性。

方法三：
```js
function isEmptyObject(e) {  
    var t;  
    for (t in e)  
        return !1;  
    return !0  
} 

//true
isEmptyObject(obj); 
//false
isEmptyObject({
    "a":1,
    "b":2
}); 
```
这个方法是jQuery的isEmptyObject()方法的实现方式。

方法四：  
使用ES6语法`Object.keys(obj)`，返回一个数组，只需要判断数组长度是否大于0即可。
```js
function isEmptyObject(obj){
  if(Object.keys(obj).length > 0) return true;
  return false;
```
推荐使用方法四。

***
![文章首发于我的微信公众号，关注可获得每次最新推送](//ww4.sinaimg.cn/large/006tNc79ly1g5d7zpfq6qj30bz0dtt9b.jpg)