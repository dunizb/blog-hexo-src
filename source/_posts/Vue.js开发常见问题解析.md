---
title: Vue.js开发常见问题解析
date: 2017-06-19 22:04:36
categories:
- 前端开发
tags:
- Vue.js
- 学习笔记
description: "《Vue.js权威指南》读书笔记之一"
---

![Vue.js](//ww1.sinaimg.cn/large/006tNc79ly1g5d800a721j30p00b0q2x.jpg)
此前的Vue.js系列文章：

 - [Vue.js常用开发知识简要入门（一）](http://dunizb.com/2016/12/18/Vue.js常用开发知识简要入门（一）)
 - [Vue.js常用开发知识简要入门（二）](//www.jianshu.com/p/ce9fc4c8a7ce)
 - [Vue.js常用开发知识简要入门（三）](http://dunizb.com/2017/02/13/Vue.js常用开发知识简要入门（三）)

## camelClass & kebab-case

HTML标签中的属性名不区分大小写。设置prop名字为camelClass形式的时候，需要转换为kebab-case形式（短横线）在HTML中使用。
```js
Vue.component('child', {
    //这里可以是camelClass形式
    props: ['myMessage'],
    template: '<span>{{ myMessage }}</span>'
});
<!-- 对应在HTML中必须是短横线分隔 -->
<child my-message="hello"></child>
```

## 字面量语法 & 动态语法

这个问题比较绕，也算是一个笔记常犯的一个错误吧，使用字面量语法传递数值：
```html
<!-- 传递了一个字符串“1” -->
<comp some-prop="1"></comp>
```
因为他是一个字面prop，它的值是字符串“1”，而不是以实际的数字传递下去。如果想传递一个真实的JavaScript类型的数字，则需要使用动态语法，从而让它的值被当做JavaScript表达式计算。
```html
<!-- 传递实际的数字 -->
<comp :some-prop="1"></comp>
```

## 模板解析

Vue的模板是DOM模板，使用浏览器原生的解析器而不是自己实现一个。相比字符串模板，DOM模板有一些好处，但是也有问题，它必须是有效的HTML片段。一些HTML元素对什么元素可以放在它里面有限制。常见的限制有：
- a不能包行其他的交互元素（如按钮、链接）
- ul和ol只能直接包含li。
- select只能包含option和optgroup。
- table只能直接包含thead、tbody、ftoot、tr、caption、col、colgroup。
- tr只能直接包含th和td。

在实际应用中，这些限制会导致意外的结果。尽管再简单的情况下它可能可以工作，但是我们不能依赖自定义组件在浏览器验证之前展开结果。例如`<my-select><option>....</option></my-select>`不是有效的模板，即使`my-select`组件最终展开为`<select>...</select>`。

另一个结果是，自定义标签（包括自定义元素和特殊标签，如`<component>`、`<template>`、`<partial>`）不能用在ul、select、table等对内部元素有限制的标签内。放在这些元素内的自定义标签将被提到元素的外面，因而渲染不正确。

自定义元素应当使用`is`特性，如
```html
<table>
    <tr is="my-component"></tr>
</table>
```
`<template>`不能用在`<table>`内，这时应该使用`<tbody>`，`<table>`可以有多个`<tbody>`。如下：
```html
<table>
    <tbody v-for="item in items">
        <tr>Even row</tr>
        <tr>Odd row</tr>
    </tbody>
</table>
```

## 如何解决数据层级结构太深的问题

在开发业务的时候，经常会出现异步获取数据的情况，有时候数据层次比较深。如：
```html
<span class="airport" v-text="ticketInfo.flight.fromSegments[ticketInfo.flight.fromSegment - 1].depAirportZh"></span>
```
我们可以使用`vm.$set`手动定义一层数据。
```js
vm.$set("depAirportZh" ,ticketInfo.flight.fromSegments[ticketInfo.flight.fromSegments - 1] .depAirportZh)
```

## data中没有定义计算属性，它是如何被使用的

如下代码：
```js
<div id="example">
    a = {{ a }}, b = {{ b }}
</div>

var vm = new Vue({
    el: '#example',
    data: {
        a: 1
    },
    computed: {
        b: function() {
            return this.a + 1;
        }
    }
});
```
对于上面计算属性b是怎么被使用的？实际上它并没有把计算数据放到`$data`里，而是通过`Object.definePropert(tihs, key, def)`直接定义到了实例上。

****
《Vue.js权威指南》读书笔记

**++++++++++[本人出售《Vue.js权威指南》，二手价20元！](http://dunizb.com/obook/)++++++++++**
[![Vue.js权威指南](//ww2.sinaimg.cn/large/006tNc79ly1g5d806ctb3j30g20jkdjd.jpg)](http://dunizb.com/obook/)