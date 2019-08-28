---
title: JS的String()、toString()、valueOf()的一些隐秘特性
date: 2017-07-06 22:06:50
categories:
- 技术
tags:
- 前端
- JavaScript
description: "要把一个值转换为一个字符串，最常用的就是，使用几乎每个值都有的toString()方法，这个方法唯一要做的就是返回相应值的字符串表现。"
---

## toString()方法

要把一个值转换为一个字符串，最常用的就是，使用几乎每个值都有的toString()方法，这个方法唯一要做的就是返回相应值的字符串表现。
<!-- more -->
数值、布尔值、对象和字符串值（没错，每个字符串也都有一个toString()方法，该方法返回字符串的一个副本）都有`toString()`方法。但`null`和`undefined`值没有这个方法。因此在对一个变量进行了`toString()`后，如果变量为`null`或者`undefined`的时候就会报错。

多数情况下，调用`toString()`方法不必传递参数。但是，在调用数值的`toString()`方法时，可以传递一个参数：输出数值的基数。默认情况下，`toString()`方法以十进制格式返回数值的字符串表示。而通过传递基数，`toString()`可以输出以二进制、八进制、十六进制，乃至其他任意有效进制格式表示的字符串值。
```js
var num = 10; 
// "10" 
alert(num.toString()); 
// "1010" 
alert(num.toString(2)); 
// "12" 
alert(num.toString(8)); 
// "10" 
alert(num.toString(10)); 
// "a" 
alert(num.toString(16));
```

通过这个例子可以看出，通过指定基数，toString()方法会改变输出的值。而数值10根据基数的不同，可以在输出时被转换为不同的数值格式。注意，默认的（没有参数的）输出值与指定基数10时的输出值相同。

## valueOf()方法

说实话这个方法存在感很低，在JS中对数据进行字符串转型，通常都用`toString()`方法，或者直接在变量后面加上空字符串。`valueOf()`方法的返回值通常与`toString()`都是一样的。但是，在Object上，他们两个表现出了截然不同的形式，在对一个对象类型（Object、Array）进行valueOf()时，valueOf()直接返回原对象，而toString()则返回`[object Object]`。**在《JavaScript高级程序设计（第三版）》中，作者说valueOf()返回与toString()相同的值，即对Array调用valueOf()返回字符串表现形式，我在多个现代浏览器中（Chrome、Egde）和IE8文档模式下测试均返回原数组对象。所以看到这里的读者要注意了，书中部分内容到现在可能并不准确了。**
```js
var a = { "a":"123", "b": "456" };
// Object {a: "123", b: "456"}
a.valueOf()
// [object object]
a.toString()
```
对于数组也一样
```
var b = [1,2,3,45,6];
// (5) [1, 2, 3, 45, 6]
b.valueOf()
// 1,2,3,45,6
b.toString()
```

## String()方法

在不知道要转换的值是不是`null`或`undefined`的情况下，还可以使用转型函数`String()`，这个函数能够将任何类型的值转换为字符串。String()函数遵循下列转换规则：
- 如果值有toString()方法，则调用该方法（没有参数）并返回相应的结果；
- 如果值是null，则返回"null"；
- 如果值是undefined，则返回"undefined"。

toString()与String()的区别就在于String()还能转换`null`和`undefined`值，可以说是toString()的增强版。在开发中直接使用String()似乎更好，这样能避免潜在的转换风险。

***
《JavaScript高级程序设计》笔记之一

