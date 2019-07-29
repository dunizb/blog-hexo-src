---
title: Array的 every、some、filter、map的区别，以及和reduce的区别
date: 2018-03-04 15:36:11
categories:
- 前端开发
tags:
- JavaScript
description: "这几个方法有时候总是傻傻分不清，尤其map，总是一下子有点懵逼记不清和其他方法的区别，每次都需要查一下API，他们的相同点都是需要遍历数组中的每一项，重点是他们的区别，不要搞混了，搞清楚他们的返回结果有什么区别。"
---
![](https://raw.githubusercontent.com/dunizb/cloudimg/master/blog/article/201803/array/banner.png)

这几个方法有时候总是傻傻分不清，尤其map，总是一下子有点懵逼记不清和其他方法的区别，每次都需要查一下API，他们的相同点都是需要遍历数组中的每一项，重点是他们的区别，不要搞混了，搞清楚他们的返回结果有什么区别。

## every、some

这两个比较好理解，测试数组的元素是否都通过了指定函数的测试，测试一个数组是否符合某个条件，`every`表示每一项都必须通过才会返回`true`，`some`表示只要数组元素某一项满足即可，比如下面的例子，分别用 `every` 和 `some` 调用。
```js
var arr = [1, 12, 32, 2, 3, 44, 120, 3, 5];
var res1 = arr.every(function(item, index){
    return item > 20;
});
console.log(res1); // false

var res2 = arr.some(function(item, index){
    return item > 20;
});
console.log(res2); // true
```
## filter

调用 `filter` 的结果是创建一个新数组，数组的元素是通过所提供函数通过测试的所有元素
```js
var res3 = arr.filter(function(item, index, array){
  return item > 20;
});
console.log(res3); // 32,44,120
```
如果处理表达式是运算，将无效，返回元素组成员组成的数组
```js
var res33 = arr.filter(function(item, index, array){
  return item * 2;
});
console.log('res33', res33); // 1, 12, 32, 2, 3, 44, 120, 3, 5
```
这个一点可以和 map 比较一下

## map
调用`map` 的结果也是创建一个新数组，不同的是：
- 如果给定的处理函数的表达式是逻辑判断，它返回的是布尔值组成的数组
```js
var res4 = arr.map(function(item, index, array){
  return item > 20;
});
console.log(res4); // false,false,true,false,false,true,true,false,false
```
- 如果给定处理函数的表达式是运算表达式，它返回的是每一项运算后的结果的数组
```js
var res5 = arr.map(function(item, index, array){
  return item * 2;
});
console.log(res5); // 2,24,64,4,6,88,240,6,10
```
## reduce
`reduce`方法对累加器和数组中的每个元素（从左到右）应用一个函数，将其减少为单个值。回调函数有两个必须的参数：
- accumulator，累加器累加回调的返回值; 它是上一次调用回调时返回的累积值
- currentValue，数组中正在处理的元素。
```js
var res6 = arr.reduce(function(accumulator, item){
  return accumulator + item;
});
console.log(res6); // 222
```
`reduce`的最重要点就是利用累加器参数(accumulator)了，如果值操作第二个参数 item, 将会只处理数组最后一项，跟`for`循环中的`i`的效果一样
```js
var arr = [1, 12, 32, 2, 3, 44, 120, 3, 5];
var res66 = arr.reduce(function(accumulator, item){
  return item * 2;
});
console.log('res66', res66); // 10
```
上面的代码试图只操作 item ，结果只是返回了数组最后一项的处理结果：`5 * 2 = 10`

以上个人总结，有不对的地方请指正。