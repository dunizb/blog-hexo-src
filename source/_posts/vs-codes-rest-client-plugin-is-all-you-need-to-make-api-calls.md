---
title: 只需使用VS Code的REST客户端插件即可进行API调用
date: 2020-11-23 23:32:24
categories:
  - 工具
tags:
  - VS Code
---

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/vs-codes-rest-client/banner.jpeg)

**为什么要离开 IDE 去测试新的 API？现在你不必这样做了。**

<!-- more -->

## 我们如何获取数据

如果你已经做了很长时间的 Web 开发，你可能知道我们的很多工作都是围绕着数据展开的：读取数据、写入数据、操作数据，并以合理的方式在浏览器中显示出来。

而这些数据绝大部分都是由 REST API 端点提供的，通俗地说：我们想要的数据存在于其他服务或数据库中，我们的应用程序查询该服务来检索数据，并根据自己的需要使用数据。

在过去，为了在连接 UI 以接受数据之前测试 REST API，通常必须通过终端的命令行查询 API，或者使用像 Insomnia 或 Postman 这样的 GUI(我在之前的博客中对它们进行了比较)。

但现在，如果你使用 VS Code（为什么不呢，用它写代码多好啊！），生活就变得简单了。我们不再需要退出 IDE 来测试 API，因为现在已经有一个插件可以做到这一点：[REST Client](https://marketplace.visualstudio.com/items?itemName=humao.rest-client)。

使用 REST Client 是非常简单的，我将向您展示这个插件是多么简单，而且功能齐全。

## 认识 VS Code REST Client 插件

我是 VS Code 这个代码编辑器的粉丝，已经有好几年了，每次得知有人创建了一个新的有用的插件并添加到 VS Code 市场，我都会无比感激。

所以当我决定每次需要测试一个新的 API 路由时，都要启动 Postman 或 Insomnia 是一件很痛苦的事情，我发现了 REST Client 这个插件，可以让这一切变得不必要。

REST Client 是迄今存在的工具的最明显名称，其 VS Code 市场描述准确地概括了其功能：“REST Client 允许您发送 HTTP 请求并直接在 Visual Studio Code 中查看响应。”

就这么简单。然后，它会提供大量的详细信息以及使用方法的示例，但实际上，它是 VS Code 中内置的 HTTP 工具。因此，让我们开始使用它。

### 安装 REST Client

要找到它，打开 VS Code 中的市场扩展（左侧面板上的俄罗斯方块小图标），在搜索栏中输入 “rest client”，然后安装列表中的第一个结果（作者应该是 Huachao Mao）。

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/vs-codes-rest-client/1.png)

安装完成后，我们可以继续进行设置。

### 设置 REST Client 脚本

只需在项目的根目录下创建一个以 `.http` 结尾的文件，REST Client 可以识别出这一点，并且知道它应该能够运行来自该文件的 HTTP 请求。

在测试的时候，我把几年前做的一个 docker 化的全栈 MERN 登录应用，把一个我命名为 `test.http` 的文件丢到项目文件夹的根目录。

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/vs-codes-rest-client/2.png)

## 测试一下：基本操作

这是很酷的部分：在我的经验中，这个小小的 REST Client 插件能够做的事情和 Postman 等更复杂的 API 客户端一样多。

下面，我将向你展示如何进行每一种类型的基本 CRUD 操作，再加上如何像 JWT 令牌一样进行需要认证的 API 调用，使用我在本地运行的 MERN 用户注册应用来指向调用。

### POST 示例

我将介绍的第一个示例是 REST Client 的 `POST`，因为用户在我的应用程序中必须先注册才能进行其他任何操作（毕竟，这只是一个登录服务）。

因此，该代码将在 `test.http` 文件中显示。

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/vs-codes-rest-client/3.png)

好的，让我们回顾一下上面的代码片段中发生的事情。

REST Client 为了正常工作所需要的第一件事是发出请求的类型及其尝试访问的路由的完整 URL 路径。在这种情况下，请求是 POST，URL 是 `http://localhost:3003/registerUser`。第一行末尾的 `HTTP/1.1` 与 RFC 2616 建立的标准有关，但是我不确定是否有必要，因此我将其保留只是为了安全。

然后，因为这是一个 `POST`，所以在请求中要包含一个 JSON 体，注意 `Content-Type` 和 `body` 之间有一行空行——这是 REST Client 有意要求的。所以，我们把所需的字段填好，然后，在 `POST` 上面应该会出现一个小小的 `send Request` 选项。把鼠标放在上面，然后点击，看看会有什么结果。

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/vs-codes-rest-client/4.png)

您最后要注意的是 `test.http` 文件中请求后的 `###` ，这是请求之间的分隔符，只要在每个请求之间插入 `###` 就可以在文件中包含任意数量的请求。

如果请求成功，您将看到与我上面发布的内容类似的内容。即使请求不成功，你仍然会得到所有这些关于刚才发生的信息，以及（希望）出了什么问题。爽啊

### GET 示例

现在已经创建了一个用户，比方说我们忘记了他们的密码，他们发了一封邮件来找回密码。电子邮件中包含令牌和链接，该链接会将他们带到页面以重置密码。

一旦他们点击了链接并登陆页面，一个 `GET` 请求就会被启动，以确保邮件中包含的用于重置密码的令牌是有效的，这就是它可能的样子。

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/vs-codes-rest-client/5.png)

我的 `GET` 指向了 `/reset` 端点，并在服务端附加了验证所需的 `resetPasswordToken` 查询参数。`Content-Type` 仍为 `application/json`，底部的 `###` 将此请求与文件中的任何其他请求分开。

如果令牌确实有效，则服务器的响应如下所示：

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/vs-codes-rest-client/6.png)

而这就是 GET 请求所需要的全部内容，他们不用担心请求体的问题。

### Update 示例

接下来是 CRUD 中的 U：更新。假设用户想更新其个人资料信息中的某些内容。使用 REST Client 也不难。

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/vs-codes-rest-client/7.png)

对于这个请求，请求类型更新为 `PUT`，body 包括该对象上需要更新的任何字段。在我的应用程序中，用户可以更新其名字，姓氏或电子邮件。

因此，在传递正文时，如果 REST Client 成功击中 PUT 端点，则这就是 VS Code 中的 Response 选项卡的样子。

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/vs-codes-rest-client/8.png)

到此为止，让我们继续进行身份验证示例。因为据我所知，没有保护路由的应用程序很少，需要某种认证。

### Authentication 示例

REST Client 支持的不同身份验证格式的广度再一次让我印象深刻。在撰写本文时，REST Client 的文档说它支持六种流行的身份验证类型，包括对 JWT 身份验证的支持，这是我的应用程序在所有受保护的路由上都依赖的身份验证类型。

因此，事不宜迟，这里是我需要验证的端点之一：在数据库中查找用户的信息。

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/vs-codes-rest-client/9.png)

在 REST Client 请求中添加授权真的很简单：简单地在路由和 content-type 被声明的地方下面添加键 `Authorization`，然后（至少对我的情况而言）我添加 JWT 的键和值（因为它们出现在浏览器的本地存储中）作为 `Authorization` 头的值。

这样就变成了：

```
Authorization: jwt XXXXXXXXXXXXXXXXXX
```

然后只需发送请求，看看会发生什么。

如果您的身份验证配置正确，您将收到来自服务器的某种类型的 200 响应，对于我的请求，它将返回存储在数据库中的与该用户相关的所有信息，以及一个成功找到该用户的消息。

这部分可能需要一些尝试和错误，但如果您能够弄清楚一个成功的请求是如何在浏览器的 Dev Tools 网络调用中发出的，通过现有的 Swagger 端点，或者通过其他类似的文档，这是非常值得的。

### DELETE 示例

经过我上面提供的其他例子，这个示例应该很简单

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/vs-codes-rest-client/10.png)

这个 `DELETE` 需要的查询参数是 `username`，这样它就知道到底要删除数据库中的哪个用户，而且还需要验证这个用户是否有资格提出这个请求。除此以外，这里就没有什么其他的新东西可以介绍了。

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/vs-codes-rest-client/11.png)

这实际上只是 REST Client 可以做的冰山一角。我涵盖了 REST 请求和一种形式的认证，但它也可以支持 GraphQL 请求、多种其他类型的认证、环境和自定义变量、查看和保存原始响应等等。

我强烈建议您查阅文档，以了解 REST Client 的所有功能，它非常强大。

> REST Client 文档：https://marketplace.visualstudio.com/items?itemName=humao.rest-client

## 结束

数据驱动着互联网，而随着职业生涯的进一步发展，Web 开发人员最终会变得非常善于访问和转换数据以满足自己的需求。

以前，当获取托管在其他地方的数据时，Web 开发人员经常会求助于 Postman 或 Insomnia 这样的工具，以拥有比命令行稍微好一点的界面，但现在有一个 VS Code 插件，它让代码编辑器之外的需求成为了过去，它叫 REST Client，非常棒。

CRUD 操作？没问题！支持 GraphQL？没问题！认证选项？没问题！REST Client 提供了所有这些选项以及更多，而且设置和使用起来非常简单。我肯定会在以后的项目中更多地使用它。

请过几周再回来看看——我将写更多有关 JavaScript，React，ES6 或其他与 Web 开发相关的内容。

谢谢你的阅读。我希望你能考虑用 REST Client 来处理你未来可能需要做的任何 API 查询，我想你会对它能提供的愉快体验感到惊喜，不需要任何 API GUI。 🙂

---

原文：[https://blog.bitsrc.io/](https://blog.bitsrc.io/vs-codes-rest-client-plugin-is-all-you-need-to-make-api-calls-e9e95fcfd85a)

作者：Paige Niedringhaus
