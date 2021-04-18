---
title: 译|API怎么选？比较SOAP、REST、GraphQL和RPC
date: 2020-12-13 23:32:15
img: https://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/comparing-api-architectural/banner.jpg
categories:
  - 服务端技术
tags:
  - GraphQL
  - 翻译
---

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/comparing-api-architectural/banner.jpg)

我们知道，两个单独的应用程序需要中介程序才能相互通信。因此，开发人员通常会搭建桥梁（应用程序编程接口），以允许一个系统访问另一个系统的信息或功能。

<!-- more -->

为了快速、大规模地集成应用程序，API 是使用协议或规范实现的，这些协议或规范定义了通过网络传递的消息的语义和语法。这些规范组成了 API 体系结构。

随着时间的推移，不同的 API 架构风格已经发布。 每一种都有自己的标准化数据交换模式，选择的丰富引发了关于哪种架构风格是最好的无休止的争论。

![API styles over time, Source: Rob Crowley](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/comparing-api-architectural/1.png)

今天，许多 API 消费者称 REST 为“安息”，并为 GraphQL 欢呼，而十年前则相反，REST 是取代 SOAP 的赢家。这些观点的问题在于，他们是片面地选择一种技术本身，而不是考虑它的实际属性和特性如何与当前的情况相匹配。

在本文中，我们将保持客观的态度，讨论四大 API 风格的出场顺序，比较它们的强弱面，并强调它们各自最适合的场景。

![Four major API styles compared](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/comparing-api-architectural/2.png)

## 远程过程调用（RPC）：在另一个系统上调用功能

**远程过程调用**是一种规范，它允许在不同的上下文中远程执行一个函数。RPC 扩展了本地过程调用的概念，但将其放在 HTTP API 的上下文中。

最初的 XML-RPC 存在问题，因为确保 XML 有效载荷的数据类型非常困难。所以，后来一个 RPC API 开始使用更具体的 JSON-RPC 规范，它被认为是比 SOAP 更简单的替代品。 gRPC 是 Google 在 2015 年开发的最新 RPC 版本，gRPC 对负载均衡、跟踪、健康检查和认证的支持可插拔，非常适合连接微服务。

### RPC 如何工作

客户端调用一个远程过程，将参数和附加信息序列化为消息，并将消息发送到服务器。服务器在收到消息后，对其内容进行反序列化，执行请求的操作，并将结果发回给客户端。服务器存根和客户端存根负责参数的序列化和反序列化。

![Remote Procedure Calling Mechanism, Source: Guru99](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/comparing-api-architectural/3.png)

### RPC 优点

**简单直接的互动。** RPC 使用 GET 来获取信息，并使用 POST 进行其他所有操作。服务器和客户端之间的交互机制归根结底是调用一个端点并获得响应。

**易于添加的功能。** 如果我们对 API 有了新的需求，我们可以很容易地添加另一个端点来执行这个需求：

1. 编写一个新函数并将其放到一个端点后，
2. 现在客户端可以打这个端点，并获得满足设定需求的信息。

**高性能。** 在提供高性能的网络上，轻量级有效载荷很容易实现，这对于共享服务器和在工作站网络上执行的并行计算非常重要。RPC 能够优化网络层，每天在不同服务之间发送大量的消息，效率非常高。

### RPC 缺点

**与底层系统紧密耦合**。一个 API 的抽象层次有助于其可重用性。它与底层系统的关系越紧密，对其他系统的可重用性就越低。 RPC 与底层系统的紧密耦合，不允许在系统中的函数和外部 API 之间建立抽象层，这就引发了安全问题，因为很容易将底层系统的实现细节泄露到 API 中。RPC 的紧密耦合使得可扩展性要求和松散耦合的团队很难实现，因此，客户端要么担心调用特定端点的任何可能的副作用，要么试图弄清楚调用哪个端点，因为它不理解服务器是如何命名其函数的。

**发现性低**。在 RPC 中，无法内省 API 或发送请求，也无法根据请求理解调用什么函数。

**函数爆炸**。创建新函数很容易。因此，我们不是编辑现有的函数，而是创建新的函数，并以一大堆难以理解的重叠函数结束。

### RPC 用例

RPC 模式大约在 80 年代开始使用，但这并不会自动使其过时。像 Google、Facebook (Apache Thrift)和 Twitch (Twirp)这样的大公司在内部使用 RPC 高性能变体来执行高性能、低开销的消息传递。他们庞大的微服务系统要求内部通信在安排短消息时要清晰。

**指令 API**。 RPC 是向远程系统发送指令的正确选择。 例如，Slack 的 API 是非常注重指令的，加入一个频道，离开一个频道，发送一条消息。所以，Slack API 的设计者将其建模为类似 RPC 的风格，使其小巧、紧密、易于使用。

**内部微服务的客户特定 API**。 通过在单个提供者和消费者之间进行直接集成，我们不希望像 REST API 那样，花费大量时间在网络上传输大量元数据。gRPC 和 Twirp 具有较高的消息速率和消息性能，是微服务的有力案例。在 HTTP 2 的支持下，gRPC 能够优化网络层，每天在不同服务之间发送大量的消息，效率非常高。但是，如果您的目标不是高网络性能，而是发布高度独特微服务的团队之间的稳定 API 联系，REST 将确保这一点。

## 简单对象访问协议（SOAP）：使数据作为服务可用

**SOAP**是一种 XML 格式的、高度标准化的网络通信协议。 SOAP 在 Microsoft 于 XML-RPC 发行一年后发布，从中继承了很多东西。 当 REST 紧随其后时，它们首次并行使用，但很快 REST 赢得了流行度竞赛。

### SOAP 如何工作

XML 数据格式背后拖着很多形式化的东西，配合庞大的消息结构，使得 SOAP 成为最啰嗦的 API 风格。

SOAP 消息包括：

- 包含请求或响应的正文

- 标头（如果消息必须确定任何具体要求或额外要求），以及

- 故障通知在整个请求处理过程中可能发生的任何错误。

![An example of the SOAP message. Source: IBM](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/comparing-api-architectural/4.png)

SOAP API 逻辑是用 Web 服务描述语言（WSDL）编写的。这种 API 描述语言定义了端点，描述了所有可以执行的过程，这使得不同的编程语言和 IDE 可以快速建立通信。

SOAP 支持有状态和无状态消息传递。在有状态的情况下，服务器存储接收到的信息可能非常繁重。但这对于涉及多方和复杂交易的操作是合理的。

### SOAP 优点

**与语言和平台无关**。创建基于 Web 的服务的内置功能允许 SOAP 处理通信并做出与语言和平台无关的响应。

**绑定到各种传输协议**。SOAP 在传输协议方面很灵活，可以适应多种情况。

**内置错误处理**。SOAP API 规范允许返回带有错误代码和解释的重试 XML 消息。

**许多安全扩展**。与 WS-Security 协议集成后，SOAP 可以满足企业级的事务质量。它提供了交易内部的隐私和完整性，同时允许在消息层面进行加密。

![SOAP消息级别的安全性：头元素中的认证数据和加密的主体](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/comparing-api-architectural/5.png)

### SOAP 缺点

如今，由于多种原因，许多开发人员对必须集成 SOAP API 的想法感到不安。

**仅 XML**。 SOAP 消息包含大量元数据，并且仅支持请求和响应的详细 XML 结构。

**重量级的**。 由于 XML 文件的大小，SOAP 服务需要很大的带宽。

**狭义的专业知识**。构建 SOAP API 服务器需要深入了解所涉及的所有协议及其高度限制的规则。

**乏味的消息更新**。 需要额外的努力来添加或删除消息属性，严格的 SOAP 模式会减慢采用速度。

### SOAP 用例

目前，SOAP 架构最常见的是用于企业内部或与其信任的合作伙伴的集成。

**高度安全的数据传输**。SOAP 严格的结构、安全和授权能力使它成为在 API 和客户端之间执行正式软件契约的最合适的选择，同时遵守 API 提供者和 API 消费者之间的合法契约。这就是为什么金融组织和其他企业用户选择 SOAP 的原因。

## REST：使数据可用作资源

**REST**是一种由一组架构约束定义的自解释 API 架构风格，旨在为许多 API 消费者广泛采用。

今天最常见的 API 风格最初是由 Roy Fielding 在 2000 年的博士论文中描述的。REST 使服务器端数据可用简单的格式表示，通常是 JSON 和 XML。

### REST 如何工作

REST 并不像 SOAP 那样严格定义，RESTful 架构应该遵守六个架构约束。

- **统一接口**：允许以统一的方式与给定的服务器进行交互，而不考虑设备或应用类型。

- **无状态**：处理请求的必要状态，就像请求本身所包含的那样，服务器不需要存储任何与会话有关的内容。

- **缓存**

- **客户端-服务器架构**：允许任何一方独立进化

- 应用程序的**分层系统**

- 服务器向客户机提供**可执行代码**的能力

事实上，有些服务只是在一定程度上是 RESTful 的。它们以 RPC 风格为核心，将较大的服务分解为资源，并有效地使用 HTTP 基础设施。但关键部分是使用超媒体又名 HATEOAS，即 Hypertext As The Engine of Application State 的缩写。基本上，这意味着每一个响应，REST API 都会提供元数据，链接到所有关于如何使用 API 的相关信息。这就是实现客户端和服务器解耦的原因。因此，API 提供者和 API 消费者都可以独立发展而不妨碍他们的交流。

![Richardson成熟度模型是实现真正完整和有用的API的一个目标标杆](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/comparing-api-architectural/6.png)

“HATEOAS 是 REST 的一个关键特性。它是真正的 REST REST 的原因。因为大多数人没有使用 HATEOAS，他们实际上是在使用 HTTP RPC。”这是 Reddit 上表达的一些激进意见。事实上，HATEOAS 是 REST 最成熟的版本。然而，要实现这一目标，需要比目前通常使用和构建的 API 客户端先进得多的智能 API 是很困难的。所以，如今即使是非常好的 REST API 也不一定能做到。这也是为什么 HATEOAS 主要作为 RESTful API 设计的长期发展愿景。

当一个服务实现了 REST 的一些功能和 RPC 的一些功能时，REST 和 RPC 之间确实可能存在一个灰色地带。REST 是基于资源或名词，而不是基于动作或动词。

![以动词为中心的RPC与以名词为中心的REST中的操作相反](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/comparing-api-architectural/7.png)

在 REST 中，使用 HTTP 方法，如 GET、POST、PUT、DELETE、OPTIONS，以及希望的 PATCH，来完成事情。

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/comparing-api-architectural/8.png)

### REST 优点

**解耦客户端和服务器**。尽可能地将客户端和服务器解耦，REST 可以实现比 RPC 更好的抽象。一个具有抽象层次的系统，能够对其细节进行封装，以更好地识别和维持其属性。这使得 REST API 具有足够的灵活性，可以随着时间的推移而发展，同时保持一个稳定的系统。

**可发现性**。客户端和服务器之间的通信描述了一切，因此不需要外部文档就能理解如何与 REST API 交互。

**缓存友好**。重用了很多 HTTP 工具，REST 是唯一允许在 HTTP 层面上缓存数据的风格。相比之下，在任何其他 API 上实现缓存都需要配置一个额外的缓存模块。

**支持多种格式**。支持多种格式存储和交换数据的能力是目前 REST 成为构建公共 API 的主流选择的原因之一。

### REST 的缺点

**没有单独的 REST 结构**。构建 REST API 没有确切的正确方法。如何对资源进行建模，对哪些资源进行建模，将取决于每个场景。这使得 REST 在理论上很简单，但在实践中却很难。

**大载荷**。REST 会返回很多丰富的元数据，这样客户端就可以仅仅从它的响应中了解应用状态的一切必要信息。对于一个拥有大量带宽容量的大型网络管道来说，这种丰富的元数据并不是什么大问题。但情况并不总是这样，这是 Facebook 在 2012 年推出 GraphQL 风格描述的关键驱动因素。

**过度获取和获取不足问题**。REST 响应包含的数据要么太多，要么不够，经常会产生对另一个请求的需求。

### REST 用例

**管理 API**。最常见的 API 类型是专注于管理系统中的对象并面向许多使用者的 API。REST 使此类 API 具有强大的可发现性和良好的文档，并且非常适合这种对象模型。

**简单的资源驱动型应用程序**。REST 是连接不需要灵活查询的资源驱动型应用的宝贵方法。

## GraphQL：仅查询所需的数据

要调用 REST API 多次才能返回所需信息，所以 GraphQL 的发明是为了改变游戏规则。

**GraphQL**是一种描述如何进行精确数据请求的语法。对于具有大量相互引用的复杂实体的应用程序数据模型来说，实现 GraphQL 是值得的。

![如何从GraphQL端点只检索需要的数据](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/comparing-api-architectural/9.png)

如今，GraphQL 的生态系统正在通过 Apollo、GraphiQL 和 GraphQL Explorer 等库和强大的工具进行扩展。

### GraphQL 如何工作

GraphQL 从构建一个模式开始，它是对你在 GraphQL API 中可能进行的所有查询以及它们返回的所有类型的描述。构建模式很困难，因为它需要在模式定义语言(SDL)中使用强类型。

在查询之前有了模式，客户可以根据模式来验证他们的查询，以确保服务器能够响应它。在到达后端应用时，GraphQL 操作会针对整个模式进行解释，并与前端应用的数据进行解析。API 向服务器发送一个大型查询，返回一个 JSON 响应，其中包含我们请求的数据的确切形状。

![在GraphQL中执行查询](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202011/comparing-api-architectural/10.png)

除了 RESTful CRUD 操作外，GraphQL 还有订阅功能，可以从服务器上获得实时通知。

### GraphQL 的优点

**类型化的模式**。GraphQL 提前公布了它能做的事情，这提高了它的可发现性，通过将客户端指向 GraphQL API，我们可以发现有哪些查询。

**能很好地适应图形类数据**。数据关系很深，但不适合平面数据。

**没有版本控制**。版本化的最佳实践是完全不对 API 进行版本化。

虽然 REST 提供了多个 API 版本，但 GraphQL 使用的是一个单一的、不断发展的版本，可以持续地访问新的功能，并有助于更干净、更可维护的服务器代码。

**详细的错误消息**。与 SOAP 类似，GraphQL 提供了发生错误的细节。它的错误信息包括了所有的解析器，并提到了出错的具体查询部分。

**灵活的权限**。GraphQL 允许有选择地暴露某些功能，同时保留私人信息。同时，REST 架构并不显示数据的部分。要么全有，要么全无。

### GraphQL 的缺点

**性能问题**。GraphQL 以复杂度换取其强大的功能。在一个请求中拥有过多的嵌套字段会导致系统过载。所以，对于复杂的查询，REST 仍然是一个更好的选择。

**缓存复杂**。由于 GraphQL 并没有重用 HTTP 缓存语义，所以需要进行自定义缓存工作。

**大量的开发前教育**。由于没有足够的时间来了解 GraphQL 的小众操作和 SDL，许多项目决定采用众所周知的 REST 方法。

### GraphQL 用例

**移动设备 API**。在这种情况下，网络性能和单条消息的有效载荷优化是很重要的。所以，GraphQL 为移动设备提供了更高效的数据加载。

**复杂的系统和微服务**。GraphQL 能够将多个系统集成的复杂性隐藏在其 API 背后。它从多个地方聚合数据，然后将它们合并到一个全局模式中。这对于传统的基础设施或随着时间的推移而扩展的第三方 API 来说，尤其重要。

## 哪种 API 模式最适合您的用例？

每个 API 项目都有不同的要求和需求。通常情况下，架构的选择取决于：

- 使用的编程语言
- 您正在开发的环境，以及
- 你所拥有的资源，包括人力和财力。

了解每一种设计风格的所有权衡，API 设计师就可以选择最适合项目的那一种。

凭借其紧密的耦合性，RPC 适用于内部微服务，但对于强大的外部 API 或 API 服务来说，它不是一个好选择。

SOAP 虽然麻烦，但其丰富的安全功能对于计费业务、预订系统和支付来说，仍然是不可替代的。

REST 具有 API 的最高抽象和最佳建模。但这往往会增加在线和聊天的负担——如果您使用的是移动设备，这是不利的一面。

GraphQL 是数据获取方面的一大进步，但并不是每个人都有足够的时间和精力去掌握它。

在一天结束的时候，用一个特定的风格尝试几个小的用例是有意义的，看看它是否适合你的用例并解决你的问题。如果确实如此，就尝试扩展，看看它是否适合更多的用例。

---

原文：[https://blog.zhangbing.site/2020/12/13/comparing-api-architectural-styles-soap-vs-rest-vs-graphql-vs-rpc/](https://blog.zhangbing.site/2020/12/13/comparing-api-architectural-styles-soap-vs-rest-vs-graphql-vs-rpc/)  
来源：[https://levelup.gitconnected.com/](https://levelup.gitconnected.com/comparing-api-architectural-styles-soap-vs-rest-vs-graphql-vs-rpc-84a3720adefa)  
作者：AltexSoft Inc
