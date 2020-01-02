---
title: 面试官：手写一个 JavaScript 的 Array.sort 方法
date: 2017-01-02 22:15:06
categories:
- 技术
tags:
- 前端
- JavaScript
---

我们先来分析一下 JavaScript 的 `Array.sort()` 方法的功能特性。
<!-- more -->

## Array.sort 详解
在JavaScript中，Array 对象的 `sort()` 方法是用来排序的，但是这个方法在默认情况下可能不是我们想要的，比如对于如下数组
```js
var arr = [2,5,10,20,7,15];
```

使用sort排序会得到如下结果：`[10, 15, 2, 20, 5, 7]`，在不传递参数的情况下，它是按字符的 Unicode 编码来排序的。

为了解决这个问题，可以为 `sort()` 方法传递一个参数，这个参数 ECMAScript 是这么定义的：
```js
/**
@param {function} [compareFn]
@return {Array.<T>}
*/
Array.prototype.sort = function(compareFn) {};
```

参数为一个 `function`，具体叫比较函数。我们可以改写为如下形式，传递一个比较函数：
```js
arr.sort(function(a,b){
    return a - b;
});
```

a 和 b 即是要比较的两个数，其返回值如下：
- 若 a 小于 b，在排序后的数组中 a 应该出现在 b 之前，则返回一个小于 0 的值。
- 若 a 等于 b，则返回 0。
- 若 a 大于 b，则返回一个大于 0 的值。

对于返回值的三种结果，我们可以直接使用 `a-b`，这样就可以正确得到结果 [2, 5, 7, 10, 15, 20]

## 手写实现 Array.sort
那么，接下来我们就来模拟一下这个方法和比较函数的实现。

首先，如果我们不用这个方法，而是自己实现排序，那么我们可以使用传统的冒泡排序方法：

```js
function sort(array){
    for(var i=0; i<array.length - 1; i++){
        // 假设数组已经排好序了
        var isSorted = true;
        for(var j=0; j<array.length - 1 - i; j++){
            if(array[j] > array[j + 1]){
                var temp = array[j];
                array[j] = array[j + 1];
                array[j + 1] = temp;
                // 还有比较，说明排序还未结束
                isSorted = false;
            }
        }
        // 如果排序已经完成，跳出循环
        if(isSorted){
            break;
        }
    }
}
```

这是一个循环次数最少的排序方法，但是，这个排序的适应性不强，对于字符串数组就不行了，假设有如下字符串数组，要求按字符串个数排序该如何实现？

```js
var arry = ["aaa","aa","c","bb"."xxxxxxxx"];
```

这样的数组我们不得不重新写一个方法来对字符串数组进行排序，需要改动上面冒泡排序的第6行的判断条件：

```js
if(array[j].length > array[j+1].length){}
```

既然只需要改动这一句，我们可以把这一句作为参数传递，以后想怎么排就传什么样的参数，这个参数就是一个函数，回调函数。下面重新改写上面的冒泡排序，传递一个回调函数。

```js
// 模拟sort()
function sort(array,fn){
    for(var i=0; i<array.length - 1; i++){
        var isSorted = true;
        for(var j=0; j<array.length - 1 - i; j++){
            if(fn(array[j], array[j + 1]) > 0){
                var temp = array[j];
                array[j] = array[j + 1];
                array[j + 1] = temp;
                    isSorted = false;
                }
            }
        }
        if(isSorted){
           break;
        }
    }
}
```

注意第 2 行和第 6 行，给 `sort` 传递了一个 `fn` 参数，这是一个函数，然后在第 6 行调用，`array[j]` 和 `array[j+1]` 分别就是回调函数的 a,b 两个比较值。用这个改写的方法即可对数值数组排序也可以对字符串数组排序了。

```js
var arr = ["aaa","a","xxxxxx","abcd","ab"];
sort(arr, function (a,b) {
    return a.length - b.length;
});
console.log(arr);
```

输出：["a", "ab", "aaa", "abcd", "xxxxxx"]。

全文完，你学会了吗？

*************
关注公众号，第一时间接收最新文章。如果对你有一点点帮助，可以点喜欢点赞点收藏，还可以小额打赏作者，以鼓励作者写出更多更好的文章。
![关注公众号](https://gitee.com/dunizb/cloudimg/raw/jsdelivr/关注名片-大礼包_横版二维码_2020-01-01-0.jpg)