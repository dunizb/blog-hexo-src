---
title: Fetch还是Axios——哪个更适合HTTP请求？
date: 2020-11-19 21:55:27
categories:
  - 技术
tags:
  - JavaScript
---

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/fetch-or-axios/banner.png)

前端开发最重要的部分之一是通过发出 HTTP 请求与后端进行通信，我们有几种方法可以异步地在 Javascript 中进行 API 调用。

几年前，大多数应用程序都使用 Ajax 发送 HTTP 请求，Ajax 代表异步 Javascript 和 XML。但是现在，开发人员通常会决定在 fetch() API 和 Axios 之间进行选择。

在本文中，我想比较这两种方法，并简要介绍一下基本知识和语法。除此之外，我还将比较在两种情况下以及在错误处理中将数据转换为 JSON 格式的过程。我还将讨论 HTTP 拦截和下载进度。

开始吧！

<!-- more -->

## Fetch 概述和语法

在构建 Javascript 项目时，我们可以使用 window 对象，并且它带有许多可以在项目中使用的出色方法。这些功能之一是 Fetch API，它提供了一种简单的全局 `.fetch()` 方法，这是一种从 API 异步获取数据的逻辑解决方案。

让我们看一下 `.fetch()` 方法的语法。

```javascript
fetch(url)
  .then((res) =>
    // handle response
  )
  .catch((error) => {
    // handle error
  })
```

在上面的示例中，您可以看到简单的获取 GET 请求的语法。在 `.fetch()` 方法中，我们有一个强制性参数**url**，它返回一个 Promise，可以使用 Response 对象来解决。

`.fetch()` 方法的第二个参数是选项，它是可选的。如果我们不传递 `options`，请求总是 GET，它从给定的 URL 下载内容。

在选项参数里面，我们可以传递方法或头信息，所以如果我们想使用 POST 方法或其他方法，我们必须使用这个可选的数组。

正如我之前提到的，Promise 会返回 Response 对象，正因为如此，我们需要使用另一个方法来获取响应的主体。有几种不同的方法可以使用，取决于我们需要的格式：

- **response.json()**
- **response.text()**
- **response.formData()**
- **response.blob()**
- **response.arrayBuffer()**

让我们看一下带有可选参数的代码示例。

```javascript
fetch(url, {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json'
  },
  body: JSON.stringify(data)
});
  .then((response) => response.json())
  .catch((error) => console.log(error))
```

在上面的代码示例中，你可以看到简单的 POST 请求，包括 `method`、`header` 和 `body` params。然后我使用 `json()` 方法将响应转换为 JSON 格式。

现在，让我们仔细看看**axios**。

## Axios 概述和语法

Axios 是一个 Javascript 库，用于从 Node.js 或 XMLHttpRequests 或浏览器发出 HTTP 请求。作为一个现代的库，它是基于 Promise API 的。

`axios` 有一些优势，比如对 XSRF 的保护或取消请求。

为了能够使用 `axios` 库，我们必须将其安装并导入到我们的项目中。可以使用 CDN，npm 或 bower 安装 `axios`。现在，让我们来看一个简单的 GET 方法的语法。

```js
axios.get(url)
  .then(response => console.log(response));
  .catch((error) => console.log(error));
```

在上面的代码中，你可以看到我使用 `.get()` 方法创建一个简单的 GET 请求。如果你想在函数中使用 POST 方法，那么只需使用 `.post()` 方法代替，并将请求数据作为参数传递即可。

当我们创建配置对象时，我们可以定义一堆属性，最常见的是：

- **baseUrl**
- **params**
- **headers**
- **auth**
- **responseType**

作为响应，`axios` 返回一个 promise，该 promise 将与响应对象或错误对象一起解析。在响应对象中，具有以下值：

- **data**，这是实际的响应主体
- **status**，调用的 HTTP 状态，例如 200 或 404
- **statusText**，以文本消息形式返回的 HTTP 状态，例如 `ok`
- **headers**，服务器发回标头
- **config**，请求配置
- **request**，XMLHttpRequest 对象

现在，让我们看一下带有数据的 POST 方法的代码示例。

```js
axios.post({
  '/url',
  { name: 'John', age: 22},
  { options }
})
```

在上面的代码中，你可以看到 `post` 方法，我们把 config 对象作为 param，其中有 URL、数据和附加选项。

我们还可以将 config 对象定义为变量，然后像下面的示例一样将其传递给 `axios`。

```js
const config = {
  url: "http://api.com",
  method: "POST",
  header: {
    "Content-Type": "application/json",
  },
  data: {
    name: "John",
    age: 22,
  },
};
axios(config);
```

在这里，你可以看到所有的参数，包括 URL、数据或方法，都在 config 对象中，所以在一个地方定义所有的东西可能更容易。

## JSON

如前所述，当我们在使用 `.fetch()` 方法的时候，需要对响应数据使用某种方法，当我们在发送带有请求的 body 时，需要对数据进行字符串化。

在 `axios` 中，它是自动完成的，所以我们只需在请求中传递数据或从响应中获取数据。它是自动字符串化的，所以不需要其他操作。

让我们看看如何从 `fetch()` 和 `axios` 获取数据。

```js
// fetch
fetch("url")
  .then((response) => response.json())
  .then((data) => console.log(data))
  .catch((error) => console.log(error));
// axios
axios
  .get("url")
  .then((response) => console.log(response))
  .catch((error) => console.log(error));
```

在上面的例子中，你可以看到，使用 `axios` 我们没有额外的一行代码，在 `.fetch()`的例子中，我们必须将数据转换为 JSON 格式。在一个较大的项目中，如果你创建了大量的调用，那么使用 `axios` 来避免重复代码会更舒服。

## 错误处理

在这一点上，我们还需要给 `axios` 点赞，因为处理错误是非常容易的。如果出现像 404 这样的错误响应，promise 就会被拒绝并返回一个错误，所以我们需要捕获一个错误，我们可以检查它是什么类型的错误，就是这样。让我们看看代码示例。

```js
axios
  .get("url")
  .then((response) => console.log(response))
  .catch((error) => {
    if (error.response) {
      // When response status code is out of 2xx range
      console.log(error.response.data);
      console.log(error.response.status);
      console.log(error.response.headers);
    } else if (error.request) {
      // When no response was recieved after request was made
      console.log(error.request);
    } else {
      // Error
      console.log(error.message);
    }
  });
```

在上面的代码中，当响应良好时，我返回了数据，但是如果请求以任何方式失败，我就能够检查 `.catch()` 部分中的错误类型并返回正确的消息。

对于 `.fetch()` 方法，就比较复杂了。每次我们从 `.fetch()` 方法中得到响应时，我们需要检查状态是否成功，因为即使不是，我们也会得到响应。在 `.fetch()` 的情况下，只有当请求没有完成时，promise 才会被解决。让我们看一下代码示例。

```js
fetch("url")
  .then((response) => {
    if (!response.ok) {
      throw Error(response.statusText);
    }
    return response.json();
  })
  .then((data) => console.log(data))
  .catch((error) => console.log(error));
```

在这段代码中，我已经在承诺对象中检查了代码的状态，如果响应有状态 ok，那么我就可以处理并使用 `.json()` 方法，但如果没有，我必须在 `.then()` 里面返回错误。

为了方便和正确的错误处理，对于你的项目来说，`axios` 绝对会是一个更好的解决方案，但如果你正在构建一个只有一两个请求的小项目，使用 `.fetch()` 是可以的，但你需要记住正确处理错误。

## 下载进度

当我们需要下载大量的数据时，一种跟踪进度的方法会很有用，特别是当用户的网络速度很慢时。早期，为了实现进度指标，开发者使用了 `XMLHttpRequest.onprogress` 回调。在 `.fetch()` 和 `axios` 中，有不同的方法来实现。

为了在 `.fetch()` 中跟踪下载进度，我们可以使用其中一个 `response.body` 属性，一个 `ReadableStream` 对象。它逐块提供主体数据，并允许我们计算时间消耗了多少数据。

在 axios 中，实现一个进度指示器也是可能的，而且更容易，因为存在一个现成的模块，可以安装和实现，它叫做 Axios Progress Bar。

如果你有大量的大数据要下载，你想跟踪进度指标的进度，你可以用 `axios` 来管理，更容易更快，但 `.fetch()` 也提供了这种可能性，只是它需要更多的代码来开发同样的结果。

## HTTP 拦截

当我们需要检查或改变我们从应用程序到服务器的 HTTP 请求时，或者以其他方式，例如，为了验证，HTTP 拦截可能是重要的。

在 `axios` 的情况下，HTTP 拦截是这个库的关键功能之一，这就是为什么我们不需要创建额外的代码来使用它。让我们看一下代码示例，看看我们能做到多么容易。

```js
// 请求拦截
axios.interceptors.request.use((config) => {
  console.log("Request sent");
});
// 响应拦截
axios.interceptors.response.use((response) => {
  // do an operation on response
  return response;
});
axios
  .get("url")
  .then((response) => console.log(response))
  .catch((error) => console.log(error));
```

在代码中，您可以看到请求拦截和响应拦截。在第一种情况下，我创建了一个 `console.log`，告知发送请求的情况，在响应拦截中，我们可以对响应做任何操作，然后返回。

`.fetch()` 默认不提供 HTTP 拦截功能，我们可以覆盖 `.fetch()` 方法，定义发送请求过程中需要发生的事情，当然，这需要更多的代码，可能比使用 `axios` 功能更复杂。

## 总结

在这篇文章中，我比较了用于创建 HTTP 请求的两种方法，从简单的概述开始，通过语法和一些重要的功能，如下载进度或错误处理。

通过比较可以看出，对于有大量 HTTP 请求，需要良好的错误处理或 HTTP 拦截的应用，Axios 是一个更好的解决方案。在小型项目的情况下，只需要几个简单的 API 调用，Fetch 也是一个不错的解决方案。

在选择项目的最佳解决方案时，还要注意一个因素，这是非常重要的。大多数浏览器和 Node.js 环境都支持 Axios，而现代浏览器仅支持 Fetch，并且某些版本可能会与旧版本一起发布。

通过这些知识的了解，希望大家能够选择出最适合自己的方案，也希望大家觉得这个比较有帮助。

**感谢您的阅读 ❤**

---

原文：[https://medium.com/javascript-in-plain-english/](https://medium.com/javascript-in-plain-english/fetch-or-axios-which-is-better-for-http-requests-a8e18d0defe2)

作者：Harsh Patel
