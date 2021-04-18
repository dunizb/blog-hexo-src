---
title: 如何使用装饰器模式极大地增强fetch()
date: 2021-03-05 21:34:59
categories:
  - JavaScript
tags:
  - 翻译
summary: "fetch()很好，但你想要更好的！"
---

> 翻译自：[dmitripavlutin.com](https://dmitripavlutin.com/enhance-fetch-with-decorator-pattern/)，作者：Dmitri Pavlutin

## 1. fetch()很好，但你想要更好的

`fetch()` API 使你可以在 Web 应用程序中执行网络请求。

`fetch()` 的用法很直接：调用 `fetch('/movies.json’)` 来启动请求，请求完成后，你将获得一个 Response 对象，你可以从中提取数据。

这是一个简单的示例，说明如何从 `/movies.json` URL 获取 JSON 格式的电影：

```js
async function executeRequest() {
  const response = await fetch("/movies.json");
  const moviesJson = await response.json();
  console.log(moviesJson);
}

executeRequest();
// logs [{ name: 'Heat' }, { name: 'Alien' }]
```

如上面的代码片段所示，你必须手动从响应中提取 JSON 对象：`moviesJson = await response.json()`。只做一次，不是问题，但是如果你的应用程序执行了许多请求，那么使用 `await response.json()` 来提取所有的 JSON 对象的时候就会很乏味。

因此很想使用第三方库，比如 axios，大大简化了请求的处理，考虑到同样使用 axios 来获取电影。

```js
async function executeRequest() {
  const moviesJson = await axios("/movies.json");
  console.log(moviesJson);
}

executeRequest();
// logs [{ name: 'Heat' }, { name: 'Alien' }]
```

`moviesJson = await axios('/movies.json’)` 返回实际的 JSON 响应，你无需像 `fetch()` 一样手动提取 JSON。

但是……使用诸如 axios 之类的帮助程序库会带来一系列问题。

首先，它增加了你的 Web 应用的捆绑大小。其次，你的应用程序与第三方库结合在一起：你得到了该库的所有好处，但也得到了它的所有 bug。

我打算采用一种从两种情况中都汲取最大益处的方法-使用装饰器模式来提高 `fetch()` API 的易用性和灵活性。

在下一节中，我们将介绍如何做到这一点。

## 2. 准备 Fetcher 接口

装饰器模式很有用，因为它允许以灵活和松散耦合的方式在基本逻辑之上添加功能（换句话说，装饰）。

如果你对装饰器模式不熟悉，建议阅读一下它的工作原理。

应用装饰器来增强 `fetch()` 需要几个简单的步骤。

第一步是声明一个名为 `Fetcher` 的抽象接口：

```typescript
type ResponseWithData = Response & { data?: any };

interface Fetcher {
  run(
    input: RequestInfo,
    init?: RequestInit
  ): Promise<ResponseWithData>;
}
```

`Fetcher` 接口只有一个方法，接受相同的参数，并返回与常规 `fetch()` 相同类型的数据。

第二步是实现基本的 fetcher 类：

```typescript
class BasicFetcher implements Fetcher {
  run(
    input: RequestInfo,
    init?: RequestInit
  ): Promise<ResponseWithData> {
    return fetch(input, init);
  }
}
```

`BasicFetcher` 实现了 `Fetcher` 接口，它的一个方法 `run()` 调用了常规的 `fetch()` 函数，简单得很。

让我们使用基本的 fetcher 类来获取电影列表：

```typescript
const fetcher = new BasicFetcher();
const decoratedFetch = fetcher.run.bind(fetcher);

async function executeRequest() {
  const response = await decoratedFetch("/movies.json");
  const moviesJson = await response.json();
  console.log(moviesJson);
}

executeRequest();
// logs [{ name: 'Heat' }, { name: 'Alien' }]
```

[Try the demo](https://codesandbox.io/s/basic-fetch-nm7qm?file=/src/index.ts)

`const fetcher = new BasicFetcher()` 创建一个 fetcher 类的实例，`decoratedFetch = fetcher.run.bind(fetcher)` 创建一个绑定方法。

然后你可以使用 `decoratedFetch('/movies.json’)` 来获取电影 JSON，就像使用常规的 `fetch()` 一样。

在这一步，`BasicFetcher` 类并没有带来好处，而且，因为有了新的接口和新的类，事情变得更加复杂。稍等...将装饰器引入动作后，你将看到巨大的好处。

## 3. JSON 提取装饰器

装饰器模式的主力军是装饰器类。

装饰类必须符合 `Fetcher` 接口，包装被装饰的实例，并在 `run()` 方法中引入额外的功能。

让我们实现一个装饰器，该装饰器从响应对象中提取 JSON 数据：

```typescript
class JsonFetcherDecorator implements Fetcher {
  private decoratee: Fetcher;

  constructor(decoratee: Fetcher) {
    this.decoratee = decoratee;
  }

  async run(
    input: RequestInfo,
    init?: RequestInit
  ): Promise<ResponseWithData> {
    const response = await this.decoratee.run(input, init);
    const json = await response.json();
    response.data = json;
    return response;
  }
}
```

让我们仔细看看 `JsonFetcherDecorator` 是如何构造的。

`JsonFetcherDecorator` 符合 `Fetcher` 接口。

`JsonExtractorFetch` 有一个私有字段 `decoratee`，它也符合 `Fetcher` 接口。在 `run()` 方法里面，`this.decoratee.run(input, init)` 可以实际获取数据。

然后，`json = await response.json()` 从响应中提取 JSON 数据。最后，`response.data = json` 将提取的 JSON 数据分配给响应对象。

现在让我们用 `JsonFetcherDecorator` 装饰器组成装饰 `BasicFetcher`，并简化 `fetch()` 的使用。

```typescript
const fetcher = new JsonFetcherDecorator(
  new BasicFetcher()
);
const decoratedFetch = fetcher.run.bind(fetcher);

async function executeRequest() {
  const { data } = await decoratedFetch("/movies.json");
  console.log(data);
}

executeRequest();
// logs [{ name: 'Heat' }, { name: 'Alien' }]
```

[Try the demo](https://codesandbox.io/s/json-extractor-decorator-eyror?file=/src/index.ts)

现在，不用再从响应中手动提取 JSON 数据，你可以从响应对象的 `data` 属性中访问提取的数据。

通过将 JSON 提取器移至装饰器，现在在每个使用 `const { data } = decoratedFetch(URL)` 的地方，你都不必手动提取 JSON 对象。

## 4. 请求超时装饰器

`fetch()` API 默认在浏览器指定的时间超时，在 Chrome 浏览器中，网络请求的超时时间为 300 秒，而在 Firefox 浏览器中则为 90 秒。

用户最多可以等待 8 秒的时间来完成简单的请求，所以需要对网络请求设置超时，8 秒后告知用户网络问题。

装饰器模式的好处是，你可以用任意多的装饰器来装饰你的基本实现。所以，让我们为 fetch 请求创建一个超时装饰器。

```typescript
const TIMEOUT = 8000; // 8 seconds

class TimeoutFetcherDecorator implements Fetcher {
  private decoratee: Fetcher;

  constructor(decoratee: Fetcher) {
    this.decoratee = decoratee;
  }

  async run(
    input: RequestInfo,
    init?: RequestInit
  ): Promise<ResponseWithData> {
    const controller = new AbortController();
    const id = setTimeout(
      () => controller.abort(),
      TIMEOUT
    );
    const response = await this.decoratee.run(input, {
      ...init,
      signal: controller.signal,
    });
    clearTimeout(id);
    return response;
  }
}
```

`TimeoutFetcherDecorator` 是一个实现 `Fetcher` 接口的装饰器。

在 `TimeoutFetcherDecorator` 的 `run()` 方法里面：如果在 8 秒内还没有完成请求，就用 AbortController 来中止请求。

现在，让这个装饰器开始工作：

```typescript
const fetcher = new TimeoutFetcherDecorator(
  new JsonFetcherDecorator(new BasicFetcher())
);
const decoratedFetch = fetcher.run.bind(fetcher);

async function executeRequest() {
  try {
    const { data } = await decoratedFetch("/movies.json");
    console.log(data);
  } catch (error) {
    // 如果请求超时超过8秒
    console.log(error.name);
  }
}

executeRequest();
// 如果请求耗时超过8秒
// 打印"AbortError"
```

[Try the demo](https://codesandbox.io/s/timeout-decorator-ibsg7?file=/src/index.ts)

在 demo 中，对 `/movies.json` 的请求耗时超过 8 秒。

由于 `TimeoutFetcherDecorator` 的缘故，`decoratedFetch('/ movies.json’)` 引发超时错误。

现在，基本的 fetcher 被包裹在 2 个装饰器中：一个提取 JSON 对象，另一个在 8 秒内超时结束请求，这极大地简化了 `decoratedFetch()` 的使用。

## 5. 总结

`fetch()` API 提供了执行获取请求的基本功能，但你需要的不止这些，使用 `fetch()` 将强制你手动从请求中提取 JSON 数据、配置超时等等。

为了避免模板，你可以使用一个更友好的库，比如 axios。然而，使用像 axios 这样的第三方库会增加应用程序的捆绑大小，以及你与它的紧密耦合。

另一种解决方案是在 `fetch()` 之上应用装饰器模式。你可以制作装饰器，从请求中提取 JSON，超时请求等等。你可以随时组合、添加或删除装饰器，而不影响使用装饰的 fetch 的代码。

你想把 `fetch()` 和最常见的装饰器一起使用吗？我为你创建了要点！可以在你的应用程序中随意使用它，甚至可以根据自己的需要添加更多装饰器！

**还有什么其他的 `fetch()` 装饰器可能有用？请在下面的评论中写下你的实现 (请使用 TypeScript)**
