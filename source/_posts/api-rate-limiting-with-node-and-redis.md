---
title: 译|通过Node和Redis进行API速率限制
date: 2020-09-16 11:04:22
categories:
  - 技术
tags:
  - 全栈开发
  - 翻译
---

速率限制可以保护和提高基于 API 的服务的可用性。如果你正在与一个 API 对话，并收到 HTTP 429 Too Many Requests 的响应状态码，说明你已经被速率限制了。这意味着你超出了给定时间内允许的请求数量。你需要做的就是放慢脚步，稍等片刻，然后再试一次。

<!-- more -->

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202009/api-rate-limiting-with-node-and-redis/banner.jpeg)

## 为什么要速率限制？

当你考虑限制你自己的基于 API 的服务时，你需要在用户体验、安全性和性能之间进行权衡。

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202009/api-rate-limiting-with-node-and-redis/1.png)

控制数据流的最常见原因是保持基于 API 的服务的可用性。但也有安全方面的好处，一次无意或有意的入站流量激增，就会占用宝贵的资源，影响其他用户的可用性。

通过控制传入请求的速率，你可以：

- 保障服务和资源不被“淹没”。
- 缓和暴力攻击
- 防止分布式拒绝服务（DDOS）攻击

## 如何实施限速？

速率限制可以在客户端级别，应用程序级别，基础架构级别或介于两者之间的任何位置实现。有几种方法可以控制 API 服务的入站流量：

- **按用户**：跟踪用户使用 API 密钥、访问令牌或 IP 地址进行的调用
- **按地理区域划分**：例如降低每个地理区域在一天的高峰时段的速率限制
- **按服务器**：如果你有多个服务器处理对 API 的不同调用，你可能会对访问更昂贵的资源实施更严格的速率限制。

你可以使用这些速率限制中的任何一种（甚至组合使用）。

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202009/api-rate-limiting-with-node-and-redis/2.png)

无论你选择如何实现，速率限制的目标都是建立一个检查点，该检查点拒绝或通过访问你的资源的请求。许多编程语言和框架都有实现这一点的内置功能或中间件，还有各种速率限制算法的选项。

这是使用 Node 和 Redis 制作自己的速率限制器的一种方法：

1. 创建一个 Node 应用
2. 使用 Redis 添加速率限制器
3. 在 Postman 中测试

> 💻 在[GitHub](https://github.com/loopDelicious/rate-limiter)上查看代码示例。

在开始之前，请确保已在计算机上安装了 Node 和 Redis。

## 步骤 1：建立 Node 应用程序

从命令行设置一个新的 Node 应用。通过 CLI 提示，或添加 `—yes` 标志来接受默认选项。

```shell
$ npm init --yes
```

如果在项目设置过程中接受了默认选项，则为入口点创建一个名为 `index.js` 的文件。

```shell
$ touch index.js
```

安装 Express Web 框架，然后在 `index.js` 中初始化服务器。

```javascript
const express = require("express");
const app = express();
const port = process.env.PORT || 3000;

app.get("/", (req, res) => res.send("Hello World!"));
app.listen(port, () =>
  console.log(`Example app listening at http://localhost:${port}`)
);
```

从命令行启动服务器。

```shell
$ node index.js
```

回到 `index.js` 中，创建一个路由，先检查速率限制，如果用户没有超过限制再允许访问资源。

```javascript
app.post("/", async (req, res) => {
  async function isOverLimit(ip) {
    // to define
  }
  // 检查率限制
  let overLimit = await isOverLimit(req.ip);
  if (overLimit) {
    res.status(429).send("Too many requests - try again later");
    return;
  }
  // 允许访问资源
  res.send("Accessed the precious resources!");
});
```

![应用级速率限制](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202009/api-rate-limiting-with-node-and-redis/3.png)

在下一步中，我们将定义速率限制器函数 `isOverLimit`。

## 步骤 2：使用 Redis 添加速率限制器

Redis 是一个内存中键值数据库，因此它可以非常快速地检索数据。使用 Redis 实施速率限制也非常简单。

- 存储一个像用户 IP 地址一样的 key。
- 增加从该 IP 发出的调用数量
- 在指定时间段后使记录过期

下图所示的限速算法是一个**滑动窗口计数器**的例子。一个用户如果提交的调用数量适中，或者随着时间的推移将它们分隔开，就永远不会达到速率限制。超过 10 秒窗口内最大请求的用户必须等待足够的时间来恢复其请求。

![限速算法：滑动窗口计数器](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202009/api-rate-limiting-with-node-and-redis/4.png)

从命令行为 Node 安装一个名为 ioredis 的 Redis 客户端。

```shell
$ npm install ioredis
```

在本地启动 Redis 服务器。

```shell
$ redis-server
```

然后在 `index.js` 中要求并初始化 Redis 客户端。

```javascript
const redis = require("ioredis");
const client = redis.createClient({
  port: process.env.REDIS_PORT || 6379,
  host: process.env.REDIS_HOST || "localhost",
});
client.on("connect", function () {
  console.log("connected");
});
```

定义我们上一步开始写的 isOverLimit 函数，按照 Redis 的这个模式，按照 IP 来保存一个计数器。

```javascript
async function isOverLimit(ip) {
  let res;
  try {
    res = await client.incr(ip);
  } catch (err) {
    console.error("isOverLimit: could not increment key");
    throw err;
  }
  console.log(`${ip} has value: ${res}`);
  if (res > 10) {
    return true;
  }
  client.expire(ip, 10);
}
```

这就是速率限制器。

当用户调用 API 时，我们会检查 Redis 以查看该用户是否超出限制。如果是这样，API 将立即返回 HTTP 429 状态代码，并显示消息 `Too many requests — try again later` 。如果用户在限制之内，我们将继续执行下一个代码块，在该代码块中，我们可以允许访问受保护的资源（例如数据库）。

在进行速率限制检查期间，我们在 Redis 中找到用户的记录，并增加其请求计数，如果 Redis 中没有该用户的记录，那么我们将创建一个新记录。最后，每条记录将在最近一次活动的 10 秒内过期。

在下一步中，请确保我们的限速器正常运行。

## 步骤 3：在 Postman 中进行测试

保存更改，然后重新启动服务器。我们将使用 Postman 将 `POST` 请求发送到我们的 API 服务器，该服务器在本地运行，网址为 `http:// localhost:3000`。

![在速率限制内](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202009/api-rate-limiting-with-node-and-redis/5.png)

继续快速连续发送请求以达到你的速率限制。

![超过速率限制-HTTP 429请求过多](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202009/api-rate-limiting-with-node-and-redis/6.gif)

## 关于限速的最终想法

这是 Node 和 Redis 的速率限制器的简单示例，这只是开始。有一堆策略和工具可以用来架构和实现你的速率限制。而且还有其他的增强功能可以通过这个例子来探索，比如：

- 在响应正文或作为 `Retry-after` 标头中，让用户知道在重试之前应该等待多少时间
- 记录达到速率限制的请求，以了解用户行为并警告恶意攻击
- 尝试使用其他速率限制算法或其他中间件

请记住，当你研究 API 限制时，你是在性能、安全性和用户体验之间进行权衡。你理想的速率限制解决方案将随着时间的推移而改变，同时也会考虑到这些因素。

> 原文：[https://codeburst.io](https://codeburst.io/api-rate-limiting-with-node-and-redis-95354259c768)，作者：Joyce Lin
