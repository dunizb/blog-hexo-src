---
title: [译]构建RESTful API的13种最佳实践
date: 2020-08-26 17:33:34
categories:
  - 技术
tags:
  - Node
  - 翻译
---

Facebook、GitHub、Google 以及其他许多巨头都需要一种服务和消费数据的方式。在当今的开发环境中，RESTful API 仍然是服务和消费数据的最佳选择之一。

但是你是否考虑过学习行业标准？设计 RESTful API 的最佳实践是什么？从理论上讲，任何人都可以在不到五分钟的时间内快速启动数据 API——无论是 Node.js，Golang 还是 Python。

我们将探讨在构建 RESTful API 时应考虑的 13 种最佳实践。但首先，让我们快速阐明 RESTful API。

<!-- more -->

## 什么是 RESTful API？

RESTful API 需要满足以下约束才能被称为 RESTful API。

1. **客户端-服务器模型**：RESTful API 遵循客户端-服务器模型，其中服务器为数据提供服务，而客户端连接到服务器以使用数据。客户端和服务器之间的交互是通过 HTTP（S）请求进行的，该请求传输了请求的数据。
2. **无状态**：更重要的是，RESTful API 应该是无状态的。每个请求都被视为独立请求。服务器不应跟踪可能影响将来请求结果的任何内部状态。
3. **统一接口**：最后，一致性定义了客户端和服务器之间的交互方式。RESTful API 定义了命名资源的最佳实践，但定义了允许你修改资源/与之交互的固定 HTTP 操作。可以在 RESTful API 中访问以下 HTTP 操作：
   - GET 请求：检索资源
   - POST 请求：创建资源或将信息发送到 API
   - PUT 请求：创建或替换资源
   - PATCH 请求：更新现有资源
   - DELETE 请求：删除资源

在对 RESTful API 的特性有了更深入的了解后，是时候了解更多关于 RESTful API 的最佳实践了。

本文为你提供了 13 种最佳实践的可行清单。让我们来探索！

## 1.正确使用 HTTP 方法

我们已经讨论了可用于修改资源的 HTTP 方法：GET，POST，PUT，PATCH 和 DELETE。

尽管如此，许多开发人员还是倾向于滥用 GET 和 POST 或 PUT 和 PATCH。通常，我们看到开发人员使用 POST 请求来检索数据。此外，我们看到开发人员使用 PUT 请求来替换资源，而他们只想更新该资源的单个字段。

确保使用正确的 HTTP 方法，因为这将为使用你的 RESTful API 的开发人员增加很多混乱。最好是坚持使用预定的准则。

## 2.命名约定

了解 RESTful API 命名约定将对你有组织地设计 API 有很大帮助。根据你服务的资源设计一个 RESTful API。

例如，你的 API 管理着作者和书籍（是的，一个经典的例子）。现在，我们要添加一个新作者或访问一个 ID 为 `3` 的作者。你可以设计下面的路由来达到这个目的：

- api.com/addNewAuthor
- api.com/getAuthorByID/3

想象一下，一个 API 承载了许多资源，每个资源都有许多属性。可能的端点列表将变得无穷无尽，而且对用户不是很友好。所以我们需要一种更有条理和标准化的方式来设计 API 端点。

RESTful API 最佳实践描述了端点应以资源名称开头，而 HTTP 操作则描述操作。现在我们得到：

- POST api.com/authors
- GET api.com/authors/3

如果我们想访问 ID 为 `3` 的作者曾经写过的所有书籍怎么办？对于这种情况，RESTful API 也有解决办法：

- GET api.com/authors/3/books

最后，如果您要为 ID 为 `3` 的作者删除 ID 为 `5` 的书，该怎么办？同样，让我们遵循相同的结构化方法来形成以下端点：

- DELETE api.com/authors/3/books/5

简而言之，利用 HTTP 操作和资源映射的结构化方式来形成易于理解的端点路径。这种方法的最大优点是，每个开发人员都了解 RESTful API 的设计方式，他们可以立即使用 API，而不必阅读你的每个端点的文档。

## 3.使用复数资源

资源应始终使用其复数形式。为什么？假设你要检索所有作者。因此，你将调用以下端点：`GET api.com/authors`。

当你读取请求时，你无法判断 API 响应是否只包含一个或所有作者。因此，API 端点应该使用复数资源。

## 4.正确使用状态码

状态码在这里不只是为了好玩，它们有一个明确的目的，状态码通知客户端请求的成功。

最常见的状态码类别包括：

- 200（OK）：请求已成功处理并完成。
- 201（Created）：指示成功创建资源。
- 400（Bad Request）：代表客户端错误。也就是说，请求的格式不正确或缺少请求参数。
- 401（Unauthorized）：未授权，你尝试访问你没有权限的资源。
- 404（Not Found）：请求的资源不存在。
- 500（Internal Server Error）：内部服务器错误，服务器在执行请求期间引发异常。

状态码的完整列表可以在[Mozilla Developers](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status)找到。

## 5.遵循相同约定

最常见的是，RESTful API 提供 JSON 数据，因此，应遵循 camelCase 大小写惯例。但是，不同的编程语言使用不同的命名约定。

## 6.如何处理搜索，分页，过滤和排序

搜索，分页，过滤和排序等操作并不代表单独的端点。这些操作可以通过使用随 API 请求提供的查询参数来完成。

例如，让我们检索按名称升序排列的所有作者。你的 API 请求应如下所示：`api.com/authors?sort=name_asc`。

此外，我想检索一个名称为“ Michiel”的作者。该请求看起来像这样 `api.com/authors?search=Michiel`。

幸运的是，许多 API 项目都带有内置的搜索、分页、过滤和排序功能。这将为你节省很多时间。

## 7.API 版本控制

我不常看到这一点，但这是对你的 API 进行版本调整的最佳实践。这是一种有效的方式来向你的用户传达重大的变化。

通常，API 的版本号包含在 API URL 中，例如：`api.com/v1/authors/3/books`。

## 8.通过 HTTP 标头发送元数据

HTTP 标头允许客户端随其请求发送其他信息。例如，`Authorization` 标头通常用于发送身份验证数据以访问 API。

你可以在[此处](https://en.wikipedia.org/wiki/List_of_HTTP_header_fields)找到所有可能的 HTTP 标头的完整列表。

## 9.限速

速率限制是控制每个客户端请求数量的一种有趣方法。这些是服务器可能返回的速率限制标头：

- X-Rate-Limit-Limit：告诉客户端在指定时间间隔内可以发送的请求数。
- X-Rate-Limit-Remaining：告诉客户端在当前时间间隔内仍可以发送多少个请求。
- X-Rate-Limit-Reset：告诉客户端速率限制何时重置。

## 10.有意义的错误处理

如果出现问题，请务必向开发人员提供有意义的错误消息，这一点很重要。例如，Twilio API 返回以下错误格式：

```json
{
  "status": 400,
  "message": "Resource books does not exist",
  "code": 24801,
  "more_info": "api.com/docs/errors/24801"
}
```

在此示例中，服务器返回状态代码和人类可读的消息。此外，还返回内部错误代码，供开发人员查找特定错误，这使开发人员可以快速查找有关该错误的更多信息。

## 11.选择正确的 API 框架

存在许多用于不同编程语言的框架，选择一个支持 RESTful API 最佳做法的框架非常重要。

对于 Node.js，后端开发人员喜欢使用 Express.js 和 Koa，而对于 Python，Falcon 是一个不错的选择。

## 12.文档化你的 API

最后，写文档！我不是在开玩笑，这仍然是传递你新开发的 API 知识最简单的方法之一。

尽管你的 API 遵循 RESTful API 列出的所有最佳实践，但仍然值得你花时间记录各种元素，比如 API 处理的资源或应用于服务器的速率限制。

想想你的其他开发人员，文档大大减少了学习 API 所需的时间。

## 13.把事情简单化！

不要让你的 API 过于复杂，保持资源简单。正确定义你的 API 处理的不同资源，将帮助你在未来避免资源相关的问题。定义你的资源，还要准确定义它的属性和资源之间的关系。这样一来，如何连接不同的资源就没有争议的空间了。

如果您喜欢这篇介绍 API 最佳实践的文章，那么您可能还喜欢[从头开始学习构建 RESTful API](https://www.sitepoint.com/build-rest-api-scratch-introduction/)。

---

原文：https://www.sitepoint.com/build-restful-apis-best-practices/
