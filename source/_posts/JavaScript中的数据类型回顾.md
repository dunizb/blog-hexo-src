---
title: JavaScript中的数据类型回顾
date: 2015-08-30 19:29:31
categories:
- 前端开发
- JavaScript
tags:
- 总结
description: "标志符、数值、字符串、布尔、类型比较、日期"
---

文本讲解标志符、数值、字符串、布尔、类型比较、日期数据类型
<!-- more -->
## 标识符（Names）

标识符由一个字母、下划线和美元符开头，其后可以选择性的加上一个或多个字母、数字或下划线。标识符不能使用下面这些保留字：
```js
abstract
boolean、break、byte
case、catch、char、class、const、continue
debugger、default、delete、do、double
else、enume、export、extends
false、final、finally、float、for、function
goto
if、implement、import、in、instanceof、int、interface
long
native、new、null
package、private、protected、public
return
short、static、supper、switch、synchronized
this、throw、throws、transient、true、try、typeof
var、volatile、void
while、with
```

在这个列表中大部分保留字尚未在这门语言中使用。这个列表不包括一些本应该被保留而没有保留的字，诸如undefined、NaN和Infinity。JavaScript不允许使用保留字来命名变量或参数。更糟糕的饿是，JavaScript不逊雨在对象字面量中，或者用点运算符提取对象属性时，使用保留字作为对象的属性名。

标识符被用于语句、变量、参数、属性名、运算符和标记。

## 数值（Numbers）

与其他任边吃语言一样，JavaScript可以处理诸如数据或文本的值。一门语言可以使用的值的类型，称为该语言的数据类型。JavaScript支持基本的数值和字符串的数据类型。在JavaScript中，所有数值都是64位双精度的，取值范围从-5e-324到1.7976931348623157e308。也就是说，在JavaScript中整数和浮点数之间并没有什么区别，二者都是数值。下面的例子使用了typeof操作符进行演示：
```js
> typeof 1;
"number"
> typeof 1.5;
"number"
```

所有JavaScript数值都是按照IEEE-754双精度二进制数标准进行表示。当执行算术运算时应该注意一些问题。例如，在把两个数值相加时，在你的脑海中这是一个通用的操作，然而在JavaScript中可能会获得令人大感意外的结果，下面的代码演示看着一问题：
```js
> .1 + .2;
0.30000000000000004
```

JavaScript没有内置的十进制数据类型，但JavaScript为数值提供了两个方法：toPrecision和toFixed，这两个方法可以按照固定位数的小数来格式化数值。下面的代码演示了着两个方法的使用：
```js
> var num = 1234.123454321;
> num.toFixed(2);
"1234.12"
> var num2 = 1234.123454321;
> num2.toPrecision(8);
"1234.1235"
```

如果使用了一个超出64位范围的数值，或者获得一个超出64位范围的值，JavaScript将返回一个特殊的值：Infinity（无穷大）或者-Infinity（负无穷大）。除数为0将返回Infinity。其他特殊值还包括NaN，他表示一个“非数值”，她是一个容易产生错误的值，常常是一些BUG的根源。

当试图将一个无效字符串对象转换为一个数值时，结果为NaN值。NaN具有“毒性”，在NaN值与数值之间执行一个操作将返回一个NaN值。可以使用内置的isNaN()函数来检查一个变量是否是NaN值。
```js
> 10 * 1 + 100 - 1 -NaN;
NaN
> var x = NaN;
> isNaN(x);
true
```
JavaScript支持八进制（基数为8）和十六进制（基数为16）。八进制字面值用一个0（即零）作为前缀，十六进制数值则以一个x作为前缀。

JavaScript内置Math对象用于常见的数学运算。例如，可以使用Math.round()方法获得两位数的精度。
```js
> Math.round( (.1+.2)*100)/100;
0.3
```

充分利用内置对象可以节省时间、提高效率。

## 字符串（Strings）

字符串是一个由0个或多个16位的Unicode字符组成的系列，使用单引号或双引号将字符串括起。这里强调它是Unicode字符，是出于国际化环境中使用JavaScript的重要性。JavaScript中没有为字符串定义特殊的数据类型。字符串也是（不可变）对象，一旦被创建，就永远无法改变它。但你可以很容易的通过 + 运算符连接其他字符串来创建一个新字符串。两个包含着完全相同的字符且字符顺序也相同的字符串被认为是相同的字符串。所以：
```js
> 'c' + 'a' + 't' === 'cat';
true
```

字符串是对象，因此字符串具有一些相应的属性和方法。字符串有一个length属性。例如，"JavaScript".length是10；再比如下面的代码：
```js
> 'test String'.indexOf('s');
2
> 'test String'.charAt(5);
"S"
```

我们也可以扩展内置的String对象以满足开发人员的需要。

## 布尔（Boolean）

Boolean类型表示true值和false值。在适当的上下文中，比如在一个if语句中，任何条件判断的值都将被转换为Boolean值以判断“真”或“假”。在判断条件中，空字符串、NaN值、null、undefined、数值0和关键字false都将被计算为false，其它的任何值都将被解析为true。
```js
if('') {
  console.log('something happens');
} else {
  console.log('nothing happens');
}
//输出：nothing happens
```

JavaScript支持的布尔操作包括：逻辑与（&&）、逻辑或（||）和逻辑非（!）。在很多常见任务中，布尔操作对于检验要求输入的字符串非常有用。
```js
function validate(){
    var name_input = 'java';
    var age_input;
    return name_input && age_input;
}
if(validate()){
    console.log('pass');
} else {
    console.log('fail');
}
//输出 fail
```

NaN值表示一个非数值的值，但你如果输入下面的代码，结果将会很奇怪：
```js
> typeof NaN;
"number"
```
这是typeof操作符奇怪的行为之一。

## 类型比较

JavaScript具有等于（==）操作符和等同（===）操作符。==操作符是危险的，因为它在执行比较之前，强制执行类型转换。例如：
```js
> 1 == '1';
true
```

显然，这不是我们想要的比较结果。如果左操作数和右操作数真正完全相同，===操作符才会返回true。
```js
> 1 === '1';
false
```
对应的还有!=和!==操作符，请总是使用===和!==操作符。

## 日期（Data）

JavaScript内置了Date对象，可以使用new操作符和Date()构造函数来创建Date对象，Date对象用于表示日期和时间。不带任何参数创建一个新的Date对象，获得的是一个表示当前日期和时间的Date对象。
```js
> var thisMoment = new Date();
> console.log(thisMoment);
Date {Sun Aug 30 2015 10:47:14 GMT+0800}
> thisMoment.getFullYear();
2015
```

虽然Date是一个方便的对象，了解该对象当然还是有用的。强烈建议使用开源的Date.js库来执行日期/时间的计算，可以从https://github.com/datejs/Datejs找到该js库，datajs官方网站现在已经无法打开，返回503状态。

## 其他类型

声明一个变量时未对其赋值，或者访问了一个不存在的对象属性，结果都会产生一个称为undefined的类型。null时JavaScript的一个内置对象，它表示没有值。在执行比较操作时，undefined和null二者都被装换成false值，但是最好避免使用undefined。在很多JavaScript解析器中，undefined是可以重新赋值的，因而可能会产生存在弊病的代码：
```js
undefined = true;
if(undefined){
    console.log('骗你！');
}
//输出：骗你！
```

下面列出了JavaScript支持的各种数据类型。正则表达式，或称为RegEx不在这里介绍。
- Number
- String
- Boolean
- Object
- Function
- Array
- Date
- RegEx
- Null
- Undefined 

在使用try/catch语句时，某些附加的内置error类型是非常有用的。通常在throw语句中创建error对象。
```js
try{
    throw new Error('非常糟糕的事情发生了!');
}catch(e){
    console.log(e.name + '：' + e.message);
}
//输出：Error：非常糟糕的事情发生了!
```

下面的列表列出了各种不同的error类型
- Error
- EvalError
- RangError
- ReferenceError
- SyntaxError
- TypeError
- URIError

对于我来说，最常见的莫过于SyntaxError这个经典的语法错误，太熟悉了。
