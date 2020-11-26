---
title: 测量JavaScript函数的性能的简单方法及与其他方式对比
date: 2020-05-17 20:42:51
img: https://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202005/performance-of-javascript/banner.jpg
categories:
  - 技术
tags:
  - 前端
  - JavaScript
  - 翻译
---

测量执行一个函数所需的时间总是一个很好的办法，证明某些实现比另一个实现的性能更好。这也是一个很好的方法，可以确保性能没有在某些改变后受到影响，也可以追踪瓶颈。

良好的性能有助于获得良好的用户体验，良好的用户体验会让用户回头客。一项研究显示，88%的在线消费者因为性能问题，在用户体验不佳后用户回来的可能性较小。

<!-- more -->

这就是为什么能够识别代码中的瓶颈并测量改进的原因。尤其是在为浏览器开发 JavaScript 时，要注意到你写的每一行 JavaScript 都有可能阻塞 DOM，因为它是一种单线程语言。

在这篇文章中，我将解释你如何测量你的功能的性能，以及如何处理你从它们中得到的结果。

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202005/performance-of-javascript/banner.jpg)

## Perfomance.now

performance API 通过其功能 `performance.now()` 提供对 [DOMHighResTimeStamp](https://developer.mozilla.org/en-US/docs/Web/API/DOMHighResTimeStamp) 的访问，该函数返回自页面加载以来经过的时间（以毫秒为单位），精度最高为 5µs（以分数为单位）。

所以在实践中，你需要取两个时间戳，保存在一个变量中，然后让第二个时间戳减去第一个时间戳。

```javascript
const t0 = performance.now();
for (let i = 0; i < array.length; i++) {
  // some code
}
const t1 = performance.now();
console.log(t1 - t0, "milliseconds");
```

Chrome 输出

```
0.6350000001020817 "milliseconds"
```

Firefox 输出

```
1 milliseconds
```

在这里，我们可以看到 Firefox 中的结果与 Chrome 完全不同，这是因为 Firefox 版本从 60 开始将 performance API 的精度降低到 2ms。

performance API 提供的功能远比只返回时间戳要多得多，它能够测量导航计时、用户计时或资源计时。请看[这篇](https://blog.logrocket.com/how-to-practically-use-performance-api-to-measure-performance/)文章，里面有更详细的解释。

但是，对于我们的用例，我们只想测量单个函数的性能，因此时间戳就足够了。

**那不是和 Date.now 一样吗？**

现在你可能会想：我也可以用 `Date.now` 来做这个啊。

是的，可以，但是有缺点。

`Date.now` 以毫秒为单位返回从 Unix 纪元（"1970-01-01-01T00:00:00:00Z"）开始的时间，并且取决于系统时钟。这不仅意味着它**没有那么精确**，而且也**不一定会递增**。WebKit 工程师（Tony Gentilcore）的解释如下：

> 也许较少考虑到的是，基于系统时间的 Date 也不是真正的用户监控的理想选择。大多数系统都会运行一个守护进程来定期同步时间。通常情况下，时钟每隔 15-20 分钟就会调整几毫秒。在这个速度下，大约有 1%的 10 秒的时间间隔是不准确的。

## Console.time

该 API 确实易于使用，只需将 `console.time` 放在你要测量的代码前面，将 `console.timeEnd` 放在要测量的代码后面，即可使用相同的 `string` 参数调用该函数，一页上最多可以同时使用 10,000 个计时器。

精度与 performance API 相同，但这又取决于浏览器。

```javascript
console.time("test");
for (let i = 0; i < array.length; i++) {
  // some code
}
console.timeEnd("test");
```

这样会自动生成易于理解的输出，如下所示：

Chrome 输出

```
test: 0.766845703125ms
```

Firefox 输出

```
test: 2ms - timer ended
```

这里的输出又与 Performance API 非常相似。

`console.time` 的优点是**易于使用**，因为它不需要手动计算两个时间戳之间的差。

## 缩短时间精度

如果你在不同的浏览器中使用上面提到的 API 来测量你的函数，你可能会发现结果会**有差异**。

这是由于浏览器试图**保护用户**免受[定时攻击](https://en.wikipedia.org/wiki/Timing_attack)和[指纹攻击](https://pixelprivacy.com/resources/browser-fingerprinting/)， 如果时间戳太准确，黑客可以使用它来识别用户。

例如，Firefox 之类的浏览器试图通过将精度降低到 2ms（版本 60）来防止这种情况。

## 需要注意的事项

现在，你已经拥有测量 JavaScript 函数的速度所需的工具。但是，最好避免一些陷阱。

### 分而治之

你注意到在过滤一些结果时有些东西很慢，但是你不知道瓶颈在哪里。

与其胡乱猜测代码中哪一部分是慢的，不如用上述这些函数来测量。

要追踪它，首先把你的 `console.time` 语句放在慢的代码块周围。然后测量它们的不同部分是如何执行的，如果其中一个部分比其他部分慢，那么就继续下去，每次深入到那里，直到找到瓶颈。

这些语句之间的代码越少，跟踪不感兴趣的内容的可能性就越小。

### 注意输入值

在实际应用中，给定函数的输入值可能会发生很大变化。仅针对任意随机值测量函数的速度并不能提供我们可以实际使用的任何有价值的数据。

确保使用相同的输入值运行代码。

### 多次运行函数

假设你有一个函数对一个数组进行迭代，对每个数组的值进行一些计算，并返回一个数组的结果。你想知道是`forEach` 还是简单的 `for` 循环更有效。

这是函数：

```javascript
function testForEach(x) {
  console.time("test-forEach");
  const res = [];
  x.forEach((value, index) => {
    res.push((value / 1.2) * 0.1);
  });

  console.timeEnd("test-forEach");
  return res;
}

function testFor(x) {
  console.time("test-for");
  const res = [];
  for (let i = 0; i < x.length; i++) {
    res.push((x[i] / 1.2) * 0.1);
  }

  console.timeEnd("test-for");
  return res;
}
```

你可以这样测试它们：

```javascript
const x = new Array(100000).fill(Math.random());
testForEach(x);
testFor(x);
```

如果你在 Firefox 中运行上述函数，你将获得类似以下的输出：

```javascript
test-forEach: 27ms - timer ended
test-for: 3ms - timer ended
```

看起来 forEach 变慢了，对吧？

让我们看看是否使用相同的输入两次运行相同的函数：

```javascript
testForEach(x);
testForEach(x);
testFor(x);
testFor(x);
```

```
test-forEach: 13ms - timer ended
test-forEach: 2ms - timer ended
test-for: 1ms - timer ended
test-for: 3ms - timer ended
```

如果我们第二次调用 `forEach` 测试，它的性能与 `for` 循环一样好。鉴于初始值较慢，可能无论如何都不值得使用 `forEach`。

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202005/performance-of-javascript/1.jpeg)

### ...在多个浏览器中

如果我们在 Chrome 中运行上述代码，结果会突然看起来不同：

```
test-forEach: 6.156005859375ms
test-forEach: 8.01416015625ms
test-for: 4.371337890625ms
test-for: 4.31298828125ms
```

这是因为 Chrome 和 Firefox 具有不同的 JavaScript 引擎，并且具有不同类型的性能优化。意识到这些差异是一件好事。

在这种情况下，Firefox 在相同输入的情况下，对 `forEach` 的使用进行了较好的优化。

`for` 在两个引擎上的性能都更好，因此最好坚持使用 `for` 循环。

这是为什么要在多个引擎中进行测量的一个很好的例子。如果仅使用 Chrome 进行测量，您可能会得出结论，与 `for` 相比，`forEach` 并不那么糟糕。

### 节流你的 CPU

这些数值看起来并不高。要知道，你的开发机器通常比你的网站所使用的普通手机浏览速度要快得多。

为了感受一下这个样子，浏览器有一个功能，可以让你节流你的 CPU 性能。

有了这个，那些 10 或 50ms 很快就变成了 500ms。

### 测量相对表现

这些原始结果实际上不仅仅取决于你的硬件，还取决于你的 CPU 和你的 JavaScript 线程的当前负载。尽量关注你的测量结果的相对改进，因为下次重启电脑时，这些数字可能会看起来很不一样。

## 总结

在本文中，我们看到了一些 JavaScript API，我们可以使用它们来测量性能，以及如何在“真实世界”中使用它们。**对于简单的测量，我发现使用 `console.time` 更容易**。

我觉得很多前端开发人员每天都没有对性能进行足够的考虑，即使这对收入有直接影响。

---

来源：[https://dev.to](https://dev.to/fgerschau/measuring-the-performance-of-javascript-functions-h6m)，作者：Felix Gerschau，翻译：前端外文精选
