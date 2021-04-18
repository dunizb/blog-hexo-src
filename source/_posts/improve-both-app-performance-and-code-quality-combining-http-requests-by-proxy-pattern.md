---
title: 改善应用程序性能和代码质量：通过代理模式组合HTTP请求
date: 2021-03-12 22:30:25
img: http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202103/http-requests-proxy-pattern/banner.png
categories:
  - JavaScript
tags:
  - 设计模式
summary: "客户机实际上访问代理函数(或对象)，代理函数对请求进行一些处理，然后将请求传递给目标"
---

> 翻译自[levelup.gitconnected.com](https://levelup.gitconnected.com/improve-both-app-performance-and-code-quality-combining-http-requests-by-proxy-pattern-2cce132d60e)，作者：bitfish

在前端项目中，我们的网页通常需要向服务器发送多个 HTTP 请求。

<!-- more -->

假设我们的产品具有一项功能，即每当用户单击 `li` 标记时，客户端都会向服务器发送一个 HTTP 请求。

这是一个简单的 Demo：

```html
<html>
  <body>
    <ul>
      <li>1</li>
      <li>2</li>
      <li>3</li>
      <li>4</li>
      <li>5</li>
      <li>6</li>
      <li>7</li>
      <li>8</li>
      <li>9</li>
    </ul>

    <script>
      // Suppose this function is used to make HTTP requests to the server
      var sendHTTPRequest = function(message) {
        console.log(
          "Start sending HTTP message to the server: ",
          message
        );
        console.log("1000ms passed");
        console.log("HTTP Request is completed");
      };

      var ul = document.getElementsByTagName("ul")[0];

      ul.onclick = function(event) {
        if (event.target.nodeName === "LI") {
          // Executes this function every time the <li> tag is clicked.
          sendHTTPRequest(event.target.innerText);
        }
      };
    </script>
  </body>
</html>
```

在上面的代码中，我们直接使用简单的 `sendHTTPRequest` 函数来**模拟**发送 HTTP 请求。这样做是为了更好地专注于核心目标，因此我简化了一些代码。

然后，我们将 click 事件绑定到 `ul` 元素。每次用户单击诸如 `<li> 5 </li>` 之类的标记时，客户端将执行 `sendHTTPRequest` 函数以向服务器发出 HTTP 请求。

上面的程序是这样的：

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202103/http-requests-proxy-pattern/1.gif)

为了使你们更容易尝试，我制作了一个 Codepen 演示：https://codepen.io/bitfishxyz/pen/PobOZMm

当然，在真实的项目中，我们可能会向服务器发送一个文件，推送通知，或者发送一些日志。但为了演示的惯例，我们将跳过这些细节。

好了，这是一个很简单的演示，那么上面的代码有没有什么缺点呢？

---

如果您的项目非常简单，那么编写这样的代码应该没有问题。但是，如果您的项目很复杂，并且客户端需要频繁向服务器发送 HTTP 请求，则此代码效率很低。

在上面的示例中，如果任何用户反复快速单击 `li` 元素会发生什么？这时，我们的客户端需要向服务器发出频繁的 HTTP 请求，并且每个请求都会消耗大量时间和服务器资源。

客户端每次与服务器建立新的 HTTP 连接时，都会消耗一些时间和服务器资源。因此，在 HTTP 传输机制中，一次传输所有文件比多次传输少量文件更为有效。

例如，您可能需要发送五个 HTTP 请求，每个 HTTP 请求的 HTTP 数据包大小为 1MB。现在，您一次发送一个 HTTP 请求，数据包大小为 5MB。通常预期后者的性能要比前一个更好。

网页上的大量 HTTP 请求可能会减慢网页的加载时间，最终损害用户体验。如果加载速度不够快，这可能会导致访问者更快地离开该页面。

因此，在这种情况下，我们可以考虑合并 HTTP 请求。

在我们目前的项目中，我的思路是这样的：我们可以在本地设置一个缓存，然后在一定范围内收集所有需要发送给服务器的消息，然后一起发送。

你可以暂停一下，自己试着想办法。

提示：您需要创建一个本地缓存对象来收集需要发送的消息。然后，您需要使用定时器定时发送收集到的消息。

这是一个实现。

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202103/http-requests-proxy-pattern/2.png)

```js
var messages = [];
var timer;
var sendHTTPRequest = function(message) {
  messages.push(message);
  if (timer) {
    return;
  }
  timer = setTimeout(function() {
    console.log(
      "Start sending messages: ",
      messages.join(",")
    );
    console.log("1000ms passed");
    console.log("HTTP Request is completed.");

    clearTimeout(timer);
    timer = null;
    messages = [];
  }, 2000);
};
```

每当客户端需要发送消息，也就是触发一个 `onclick` 事件的时候，`sendHTTPRequest` 并不会立即向服务器发送消息，而是先将消息缓存在消息中。然后，我们有一个计时器，该计时器在 2 秒钟后执行，并且在 2 秒钟后，该计时器会将所有先前缓存的消息发送到服务器。此更改达到了组合 HTTP 请求的目的。

测试结果如下：

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202103/http-requests-proxy-pattern/3.gif)

如你所见，尽管我们多次触发点击事件，但在两秒钟内，我们只发送了一个 HTTP 请求。

当然，为了方便演示，我将等待时间设置为 2 秒。如果你觉得这个等待时间太长，你可以缩短这个等待时间。

对于不需要太多实时交互的项目，2 秒的延迟并不是一个巨大的副作用，但它可以减轻服务器的很多压力。在适当的情况下，这是非常值得的。

---

上面的代码确实为项目提供了一些性能改进。但是就代码设计而言，上面的代码并不好。

第一，违反了单一责任原则。`sendHTTPRequest` 函数不仅向服务器发送 HTTP 请求，而且还组合 HTTP 请求。该函数执行过多操作，使代码看起来非常复杂。

如果某个功能（或对象）承担了过多的责任，那么当我们的需求发生变化时，该功能通常将不得不发生重大变化。这样的设计不能有效地应对可能的更改，这是一个糟糕的设计。

我们理想的代码如下所示：

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202103/http-requests-proxy-pattern/4.png)

我们没有对 `sendHTTPRequest` 进行任何更改，而是选择为其提供代理。这个代理函数执行合并 HTTP 请求的任务，并将合并后的消息传递给 `sendHTTPRequest` 发送。然后我们以后就可以直接使用 `proxySendHTTPRequest` 方法了。

您可以暂停片刻，然后尝试自己解决。

这是一个实现：

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202103/http-requests-proxy-pattern/5.png)

```js
var proxySendHTTPRequest = (function() {
  var messages = [],
    timer;
  return function(message) {
    messages.push(message);
    if (timer) {
      return;
    }
    timer = setTimeout(function() {
      sendHTTPRequest(messages.join(","));
      clearTimeout(timer);
      timer = null;
      messages = [];
    }, 2000);
  };
})();
```

其基本思想与前面的代码类似，该代码使用 `messages` 变量在一定时间内缓存所有消息，然后通过计时器统一地发送它们。此外，这段代码使用了闭包技巧，将 `messages` 和 `timer` 变量放在局部作用域中，以避免污染全局名称空间。

这段代码与前面的代码最大的区别是它没有更改 `sendHTTPRequest` 函数，而是将其隐藏在 `proxySendHTTPRequest` 后面。我们不再需要直接访问 `sendHTTPRequest`，而是使用代理 `proxySendHTTPRequest` 来访问它。`proxySendHTTPRequest` 与`sendHTTPRequest` 具有相同的参数列表和相同的返回值。

这样的设计有什么好处？

- 发送 HTTP 请求和合并 HTTP 请求的任务交给了两个不同的函数，每个函数专注于一个职责。它遵从单一责任原则，并使代码更容易理解。
- 由于两个函数的参数是相同的，我们可以简单地用 `proxySendHTTPRequest` 替换 `sendHTTPRequest` 的位置，而不需要做任何重大更改。

想象一下，如果将来网络性能有所提高，或者由于某些其他原因，我们不再需要合并 HTTP 请求。在这一点上，如果我们使用以前的设计，我们将不得不再次大规模地更改代码。在当前的代码设计中，我们可以简单地替换函数名。

事实上，这个编码技巧通常被称为设计模式中的**代理模式**。

所谓的代理模式，其实在现实生活中很好理解。

- 比方说，你想访问一个网站，但你不想泄露你的 IP 地址。那么你可以使用 VPN，先访问你的代理服务器，然后通过代理服务器访问目标网站。这样目标网站就无法知道你的 IP 地址了。
- 有时候，你会把你的真实服务器隐藏在 Nginx 服务器后面，让 Nginx 服务器为你的真实服务器处理一些琐碎的操作。

这些都是现实生活中代理模式的例子。

我们不需要为代理模式（或任何其他设计模式）的正式定义而烦恼，我们只需要知道，当客户端没有直接访问它的便利（或能力）时，我们可以提供代理功能（或对象）来控制对目标功能（或对象）的访问即可。客户机实际上访问代理函数(或对象)，代理函数对请求进行一些处理，然后将请求传递给目标。
