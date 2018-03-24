---
title: jQuery Core Style Guide（规范）
date: 2015-08-29 20:43:04
categories:
- 前端开发
tags:
- jQuery
- 规范
---

[Google JavaScript Style Guide](http://google-styleguide.googlecode.com/svn/trunk/javascriptguide.xml)定义了如下规范：
- 除了意图明确的需要创建一个全局变量之外，总是使用var声明变量；
- 总是使用分号。只对于压缩代码非常重要；
- 常量使用大写，每个单词之间用一个下划线分隔；
- 函数、变量和方法名都是用驼峰写法，但整个变量名第一个字母小写；
- 类和枚举名也采用驼峰写法，但整个变量名的第一个字母大写。
<!-- more -->
JQuery团队还发表了一些开发[jQuery核心库的规范](http://learn.jquery.com/style-guide/)。在该规范中描述了以下规则：

**1. 使用代码有间隔**
在代码中明确的使用空格，并使用制表符（tab）缩进代码。在代码行末尾不要使用空格字符，在空行中也不应该使用空格字符。下面的代码描述了

一种在代码中使用间隔的较好方式：
```js
if ( test === "test string" ) { 
      methodCall( "see", "our", "spacing" ); 
}
```

**2. 使用注释**
对于多行注释使用/* */，对于单行注释，使用//，在注释上保留一个空行。请将单行注释放在所有注释代码行之上，并且在注释行中仅包含注释的说明。
```js
// 注释
var x = 'blah';
var x = 'blah'; // 不良方式
```

**3.  相等性** 
总是使用等同(===)进行比较，而不是简单的使用等于(==)进行比较。对于这一规则，jQuery团队的一个例外，就是检查null值时使用简单的等于操作符进行比较。正如规范所说，执行==null或者！=null的比较操作符实际上非常有用，因为当值为null嚯undefined时，这种比较会成功（或失败）。

**4.  以块方式组织代码** 
对于控制结构（if/else/for/while/try）总是使用花括号，并且总是以多行的代码块方式来编写代码。不要使用没有花括号的单行if语句。应该将其实花括号与else/else if/catch放在同一行上。建议不要使用三元操作符来取代if/else语句。下面是一些例子：
```js
//不良方式
if( success ) alert( '操作成功' );
//良好方式
if( success ){
     alert( '操作成功' );
}

//良好方式
if( option ) {
     //代码
} else {
     //代码
}
```

**5. 函数调用格式 **
在函数调用参数的两侧包含一个多余的空格。但当函数调用的是嵌套的、或是函数的参数为空、或者传入的参数是一个对象字面量或数组时例外。
```js
//正确的函数调用方式
f(arg );
f( g(arg) );
f();
f( { } );
f( [ ] );
```

**6. 数组和对象**
对于空对象或数组字面量不要使用额外的空格，但在逗号和冒号之后使用一个空格：
```js
var a = {};
var b = [];
var c = [ 1, 2, 3, 4 ];
```

**7. 对变量/对象赋值**
在赋值语句后总是使用一个分号结尾并结束改行。Google Style Guide也要求，在一行的代码结尾总是使用分号。

**8. 类型检查**
下表描述了执行类型检查的代码

对象：类型检查表达式
- String：typeof object === "String"
- Number：typeof object === "number"
- Boolean：typeof object === "boolean"
- Object：typeof object === "object"
- Element：object.nodeType
- null：object === null
- null 或 undefined：object == null

下表列出了使用jQuery API对对象执行类型检查的方法。

对象：用于类型检查的jQuery方法
- Plain Object(测试对象是否是纯粹的对象(通过 "{}" 或者 "new Object" 创建的))：jQuery.isPlainObject(object)
- Function：jQuery.isFunction(object)
- Array：jQuery.isArray(object)

下表列出了用于对undefined执行类型检查的方法

对象：类型检查表达式
- Global Variables（全局变量）：typeof variables === "undefined"
- Local Variables（局部变量）：variable === undefined
- Properties（属性）：object.prop === undefined

**9. RegExp**
在.text()或.exec() 中创建正在表达式（RegExp）

**10. 字符串**
使用双引号而不是单引号

开发规范的文档中还提到了使用JSLint进行验证。

Google JavaScript Style Guide与jQuery的Style Guide之间也存在着一些差别，例如：Google JavaScript Style Guide建议使用单引号，而jQuery团队则将使用双引号作为一种标准。