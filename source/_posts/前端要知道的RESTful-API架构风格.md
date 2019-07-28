---
title: 前端要知道的RESTful API架构风格
date: 2019-07-28 12:26:45
categories:
- 前端开发
description: "前端程序员在开发完页面后总是要对接口的，跟后端联调有时候还占用蛮大的时间的，那么你了解你和后端对的接口都是什么风格吗，你们公司接口设计的如何，你使用愉快吗？下面介绍一种API架构风格，也是目前主流的API设计风格，你或许一直在使用。"
---

> 前端程序员在开发完页面后总是要对接口的，跟后端联调有时候还占用蛮大的时间的，那么你了解你和后端对的接口都是什么风格吗，你们公司接口设计的如何，你使用愉快吗？下面介绍一种API架构风格，也是目前主流的API设计风格，你或许一直在使用。

RESTful 是一种遵守 REST 设计的架构风格。REST 既不是标准，也不是协议，而是一组架构约束条件和设计指导原则，一种基于HTTP、URI、XML 等现有协议与标准的开发方式。


## REST
REST这个词，源于HTTP协议（1.0版和1.1版）的主要设计者表于2000年的博士论文《架构风格与基于网络的软件架构设计》REST它是 Representational State Transfer的缩写，表示表述性状态转移，这个说明比较晦涩抽象，难以理解。接下来拆开解释。首先这句话省略了主语“表述性”其实指的是“资源”的“表述性”；其次，要先理解一个重要的概念:资源的表述；最后再体会状态转移。

**资源：REST**是面向资源的，资源是网络上的一个实体，可以是一个文件、一张图像、一首歌曲、甚至是一种服务。资源可以设计得很抽象，但只要是具体信息，就可以是资源，因为资源的本质是一串二进制数据。并且每个资源必须有URL，通过URL来找到资源。

**表述**：资源在某个特定时刻的状态说明被称为表述（representation），表述由数据和描述数据的元数据（例如HTTP报文）组成。资源的表述有多种格式，这些格式也被称为MIME类型，例如文本的txt格式、图像的png格式、视频的mk格式等。一个资源可以有多种表述，例如服务器响应一个请求返回的资源可以是JSON格式的数据，也可以是XML格式的数据。

**表述性状态转移**：表述性状态转移的目的是操作资源，通过转移和控制资源的表述就能实现此目的。例如客户端可以向服务器发送GET请求，服务器将资源的表述转移到客户端；客户端也可以向服务器发送POST请求，传递表述改变服务器中的资源状态。

## 约束条件
REST给出了6种约束条件，通信两端在遵循这些约束后，就能提高工作效率，改善系统的可伸缩性、可靠性和交互的可见性，还能促进服务解耦。

**客户端-服务器**：客户端与服务器分离关注点，客户端关注用户接口，服务器关注数据存储客户端向服务器发起接口请求（获取数据或提交数据），服务器返回处理好的结果给客户端，客户端再根据这些数据渲染界面，同一个接口可以应用于多个终端（例如Web、iOs或 Android）大大改善了接口的可移植性，并且只要接口定义不变，客户端和服务器可以独立开发、互不影响。

**无状态**：两端通信必须是无状态的，服务器不会保存上一次请求的会话状态，会话状态要全部保存在客户端，从客户端到服务器的每个请求都要附带一些用于理解该请求的信息，例如在后台管理系中，大部分都是需要身份认证的请求，所以都会附带用户登录状态。

**缓存**：响应的资源可以被标记为可缓存或禁止缓存，如果可以缓存，那么客户端可以减少与服务器通信的次数，降低延迟、提高效率。

**统一接口**：统一接口是REST区别于其他架构风格的核心特征，接口定义包括4个部分
- 资源的识别（identification of resources）也就是用一个URL指向资源，要获取这个资源，只要访问它的URL即可，URL就是资源的地址或标识符。REST对URL的命名也有要求，在URL中不能有动词，只能由名词组成。

- 通过表述对资源执行操作（manipulation of resources through representations）在表述中包含了操作该资源的指令，例如用HTTP的请求首部 n/firb4， l kn http airhs accept tw＆it， HTTP hiit（tn get，指定需要的表述格式，用HTTP方法（如get、POST等）完成对数据的增删改查工作，用HTTP响应状态码表示请求结果。

- 自描述的消息（self-descriptive messages）包含如何处理该消息的信息，例如消息所使用的表述格式、能否被缓存等。

- 作为应用状态引擎的超媒体（hypermedia as the engine of application state）超媒体并不是一种技术，而是一种策略，建立一种客户端与服务器之间的对话方式。超媒体可以将资源互相连接，并能描述它们的能力，告诉客户端如何构建HTTP请求。

**分层系统**：将架构分解为若干层，降低层之间的耦合性。每个层只能和与它相邻的层进行通信。

**按需代码**：这是一条可选的约束，支持客户端下载并执行一些代码（例如 Java AppletJavaScript或 Flash）进行功能扩展。


## 常见笔试题:什么是 RESTful API，如何设计RESTful API？

答案: RESTful API Web API。为了使得接口安全、易用、可维护以及可扩展，一般设计 RESTful API需要考虑以下几个方面:
1. 通信用HTPS安全协议。
2. 在URL中加入版本号，例如“vl/animals＇s
3. URL中的路径（endpoint）不能有动词，只能用名词。
4. 用HTTP方法对资源进行增删改查的操作。
5. 用HTTP状态码传达执行结果和失败原因。
6. 为集合提供过滤、排序、分页等功能。
7. 用查询字符串或HTTP首部 （7）AifF＊f＊ HTTP ＃ accpet i1tw＃t，ti＆ pETiT.进行内容协商，指定返回结果的数据格式。
8. 及时更新文档，每个接口都有对应的说明。


## RESTful API 示例
下面是我是真实API截图，用Swagger管理，基本遵循RESTful API架构风格

![RESTful API 示例](https://i.loli.net/2019/07/28/5d3d1a28d056647013.png)

路径
- https://api.example.com/v1/zoos
- https://api.example.com/v1/animals
- https://api.example.com/v1/employees

HTTP动词
- GET（SELECT）：从服务器取出资源（一项或多项）。
- POST（CREATE）：在服务器新建一个资源。
- PUT（UPDATE）：在服务器更新资源（客户端提供改变后的完整资源）。
- PATCH（UPDATE）：在服务器更新资源（客户端提供改变的属性）。
- DELETE（DELETE）：从服务器删除资源。
- HEAD：获取资源的元数据。
- OPTIONS：获取信息，关于资源的哪些属性是客户端可以改变的。

下面是一些例子
- GET /zoos：列出所有动物园
- POST /zoos：新建一个动物园
- GET /zoos/ID：获取某个指定动物园的信息
- PUT /zoos/ID：更新某个指定动物园的信息（提供该动物园的全部信息）
- PATCH /zoos/ID：更新某个指定动物园的信息（提供该动物园的部分信息）
- DELETE /zoos/ID：删除某个动物园
- GET /zoos/ID/animals：列出某个指定动物园的所有动物
- DELETE /zoos/ID/animals/ID：删除某个指定动物园的指定动物

状态码，服务器向用户返回的状态码和提示信息，常见的有以下一些（方括号中是该状态码对应的HTTP动词）。
- 200 OK - [GET]：服务器成功返回用户请求的数据，该操作是幂等的（Idempotent）。
- 201 CREATED - [POST/PUT/PATCH]：用户新建或修改数据成功。
- 202 Accepted - [*]：表示一个请求已经进入后台排队（异步任务）
- 204 NO CONTENT - [DELETE]：用户删除数据成功。
- 400 INVALID REQUEST - [POST/PUT/PATCH]：用户发出的请求有错误，服务器没有进行新建或修改数据的操作，该操作是幂等的。
- 401 Unauthorized - [*]：表示用户没有权限（令牌、用户名、密码错误）。
- 403 Forbidden - [*] 表示用户得到授权（与401错误相对），但是访问是被禁止的。
- 404 NOT FOUND - [*]：用户发出的请求针对的是不存在的记录，服务器没有进行操作，该操作是幂等的。
- 406 Not Acceptable - [GET]：用户请求的格式不可得（比如用户请求JSON格式，但是只有XML格式）。
- 410 Gone -[GET]：用户请求的资源被永久删除，且不会再得到的。
- 422 Unprocesable entity - [POST/PUT/PATCH] 当创建一个对象时，发生一个验证错误。
- 500 INTERNAL SERVER ERROR - [*]：服务器发生错误，用户将无法判断发出的请求是否成功。

状态码的完全列表参见[这里](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)。

## 传统接口写法与Restful API 区别
这里再区分以下传统接口写法与Restful API 的区别

一个文件操作接口，传统模式：
- api/getfile.php - 获取文件信息，下载文件
- api/uploadfile.php - 上传创建文件
- api/deletefile.php - 删除文件

RESTfu，api/file 只需要这一个接口：
- GET 方式请求 api/file - 获取文件信息，下载文件
- POST 方式请求 api/file - 上传创建文件
- DELETE 方式请求 api/file - 删除某个文件

你的公司使用的是RESTful API吗？如果不是可以考虑辞职了，太落伍了！RESTful API 现在也要让位新宠 [GraphQL](https://graphql.cn/) 了，一种更高效、强大和灵活的数据提供方式。

![GraphQL](https://i.loli.net/2019/07/28/5d3d1c263f5c491907.png)

*******
推荐文章
- [《前端程序员面试笔试宝典》P162](https://book.douban.com/subject/30324146/)
- [理解RESTful架构](http://www.ruanyifeng.com/blog/2011/09/restful.html)
- [RESTful 架构风格概述](https://blog.igevin.info/posts/restful-architecture-in-general/)
- [前后端分离与restful api介绍](https://zhuanlan.zhihu.com/p/32901317)