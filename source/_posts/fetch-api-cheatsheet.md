---
title: Fetch API速查表：9个最常见的API请求
date: 2020-11-24 21:40:46
img: http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/fetch-api-cheatsheet/banner.jpg
categories:
  - 技术
tags:
  - JavaScript
summary: 在本文中，我将列出 9 个最常见的 Fetch API 请求，在你忘记 API 的时候可以翻出来查看。
---

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/fetch-api-cheatsheet/banner.jpg)

在之前的文章[《Fetch 还是 Axios——哪个更适合 HTTP 请求？》](https://blog.zhangbing.site/2020/11/19/fetch-or-axios-which-is-better-for-http-requests/)中我对比了 Axios，在小型项目的情况下，使用 Fetch API 只需要几个简单的 API 调用，Fet 是一个很不错的解决方案。

在本文中，我将列出 9 个最常见的 Fetch API 请求，在你忘记 API 的时候可以翻出来查看。​

<!-- more -->

## 为什么要使用 Fetch API？

如今，我们被所有提供漂亮的 SDK 的服务宠坏了，这些 SDK 将实际的 API 请求抽象化，我们只需要使用典型的语言结构来请求数据，而不关心实际的数据交换。

但是，如果你所选择的平台没有 SDK 怎么办？或者如果你同时构建服务器和客户端呢？在这些情况下，你需要自己处理请求，这就是使用 Fetch API 的方法。

### 使用 Fetch API 的简单 GET 请求

```js
fetch("{url}").then((response) => console.log(response));
```

### 使用 Fetch API 的简单 POST 请求

```js
fetch("{url}", {
  method: "post",
}).then((response) => console.log(response));
```

### 在 Fetch API 中使用授权令牌 (Bearer) 进行 GET

```js
fetch("{url}", {
  headers: {
    Authorization: "Basic {token}",
  },
}).then((response) => console.log(response));
```

### 在 Fetch API 中使用查询字符串数据进行 GET

```js
fetch("{url}?var1=value1&var2=value2").then((response) =>
  console.log(response)
);
```

### 在 Fetch API 中使用 CORS 进行 GET

```js
fetch("{url}", {
  mode: "cors",
}).then((response) => console.log(response));
```

### 在 Fetch API 中使用授权令牌和查询字符串数据进行 POST

```js
fetch("{url}?var1=value1&var2=value2", {
  method: "post",
  headers: {
    Authorization: "Bearer {token}",
  },
}).then((response) => console.log(response));
```

### 在 Fetch API 中使用表单数据进行 POST

```js
let formData = new FormData();
formData.append("field1", "value1");
formData.append("field2", "value2");

fetch("{url}", {
  method: "post",
  body: formData,
}).then((response) => console.log(response));
```

### 在 Fetch API 中使用 JSON 数据进行 POST

```js
fetch("{url}", {
  method: "post",
  headers: {
    "Content-Type": "application/json",
  },
  body: JSON.stringify({
    field1: "value1",
    field2: "value2",
  }),
}).then((response) => console.log(response));
```

### 在 Fetch API 中使用 JSON 数据和 CORS 进行 POST

```js
fetch("{url}", {
  method: "post",
  mode: "cors",
  headers: {
    "Content-Type": "application/json",
  },
  body: JSON.stringify({
    field1: "value1",
    field2: "value2",
  }),
}).then((response) => console.log(response));
```

## 如何处理 Fetch API 请求的结果

Fetch API 返回一个 Promise。这就是为什么我总是使用 `.then()` 和回调函数来处理响应的原因：

```js
fetch(...).then(response => {
   // process the response
}
```

但是，如果你处于异步函数中，也可以等待结果：

```js
async function getData(){
  let data = await fetch(...);
   // process the response
}
```

现在让我们看一下如何从响应中提取数据：

### 如何检查 Fetch API 响应的状态码

发送 POST，PATCH 和 PUT 请求时，我们通常对返回状态代码感兴趣：

```js
fetch(...).then(response => {
  if (response.status == 200){
    // all OK
  } else {
    console.log(response.statusText);
  }
});
```

### 如何获取 Fetch API 响应的简单值

某些 API 端点可能会发回使用你的数据创建的新数据库记录的标识符：

```js
var userId;

fetch(...)
    .then(response => response.text())
    .then(id => {
        userId = id;
        console.log(userId)
    });
```

### 如何转换 Fetch API 响应的 JSON 数据

但是在大多数情况下，你会在响应正文中接收 JSON 数据：

```js
var dataObj;

fetch(...)
    .then(response => response.json())
    .then(data => {
        dataObj = data;
        console.log(dataObj)
    });
```

请记住，只有在两个 Promises 都解决后，你才能访问数据。这有时会让人有点困惑，所以我总是喜欢使用 async 方法并等待结果。

```js
async function getData(){
    var dataObj;

    const response = await fetch(...);
    const data = await response.json();
    dataObj = data;
    console.log(dataObj);
}
```

## 总结

这些示例应该涵盖了大多数情况。

我是否错过了什么，一个你每天都在使用的请求？或者是其他你正在苦恼的事情？请在评论区上告诉我。

最后，你也可以以可打印的形式获得这份备忘单：https://ondrabus.com/fetch-api-cheatsheet

---

> 原文：[https://www.freecodecamp.org/news](https://www.freecodecamp.org/news/fetch-api-cheatsheet/)
> 作者：Ondrej Polesny
