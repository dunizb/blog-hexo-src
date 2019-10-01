---
title: 前端要知道的RESTful API架构风格
date: 2019-07-28 12:26:45
categories:
- 跨端
tags:
- RESTful
description: "前端程序员在开发完页面后总是要对接口的，跟后端联调有时候还占用蛮大的时间的，那么你了解你和后端对的接口都是什么风格吗，你们公司接口设计的如何，你使用愉快吗？下面介绍一种API架构风格，也是目前主流的API设计风格，你或许一直在使用。"
---
![封面图](https://raw.githubusercontent.com/dunizb/cloudimg/master/blog/article/201907/restful-api/banner.jpg)

> 前端程序员在开发完页面后总是要对接口的，跟后端联调有时候还占用蛮大的时间的，那么你了解你和后端对的接口都是什么风格吗，你们公司接口设计的如何，你使用愉快吗？下面介绍一种API架构风格，也是目前主流的API设计风格，你或许一直在使用。

RESTful 是一种遵守 REST 设计的架构风格。REST 既不是标准，也不是协议，而是一组架构约束条件和设计指导原则，一种基于HTTP、URI、XML 等现有协议与标准的开发方式。
<!-- more -->
![RESTful API 示例](https://raw.githubusercontent.com/dunizb/cloudimg/master/blog/article/201907/restful-api/1.png)

## REST是什么？
如果有问这么问你，你可以非常言简意赅的告诉他：“REST是一个风格！”，用英文说就是 Style，那他是什么风格呢？它是**万维网软件架构风格**。

风格这个词是非常关键的，因为它告诉我们，REST 不是协议，也不是什么硬性的规范，仅仅就是一种架构风格而已。是一组架构约束条件和设计指导原则，一种基于HTTP、URI、XML 等现有协议与标准的开发方式。

## 为何叫REST？
REST并不是REST这个单词休息的意思，而是 Representational State Transfer 的缩写，表示表述性状态转移，这个说明比较晦涩抽象，难以理解。接下来拆开解释。
- Representational：在整个词语中表示“数据的表现形式”，如（JSON、XML......）,REST其实对数据的传输是不做任何限制的，尽管它不做任何限制，但我们在写REST服务时的最佳实践还是用JSON比较多。
- State：当前状态或者数据。什么意思呢？比如说我们写了一个用户接口，一个用户列表或单个用户的数据，比如说姓名性别这些都是 State 都是数据，在 REST 这个词组里为什么要用 State 来代表这些数据呢？因为如果我们对数据进行增删改查那么数据就变了，在变化的每一个阶段它都是一种状态，从状态1变到状态2等等，用状态来描述数据更好的显示了数据是会变化的是会被我我们所修改的。
- Transfer：数据传输。在 REST 这个词组里它代表的是数据在互联网上进行传输，比如从服务端传输到客户端。

其实 REST 的字面意思是很难表达它的精髓的，接下来我们通过 REST 的 6 个限制来详细了解它。这6个限制是REST的精髓，是它的重中之重，在面试中会经常考到。

## REST的六个限制
REST给出了6种约束条件，通信两端在遵循这些约束后，就能提高工作效率，改善系统的可伸缩性、可靠性和交互的可见性，还能促进服务解耦。

### 客户端-服务器（Client-Server）
这个限制其实已经非常常见了，现在几乎没有什么不是CS架构的，所以它也是没有任何争议的，值得一提的是这个限制的本质其实是一种软件架构思想，叫做**分离关注点**，所谓关注点分离，用大白话说其实就是各扫门前雪，自己管好自己的事。这里是指服务端专注数据存储，提升了**简单性**；前端专注于用户界面，提升了**可移植性**。

### 无状态（Sateless）
所谓无状态就是所有用户会话信息都保存在客户端，意思就是所有的会话信息服务端都不管，不要妄想让服务端存着你的用户信息、用户会话信息、当前所处的状态，服务端都不知道，因为服务端不管事了，所以每次请求必须包括所有信息，不能依赖上下文信息。

无状态有什么好处？好处就是服务端不用保存会话信息，提升了简单些、可靠性、可见性。
- 简单性。服务端少了很多代码自然就简单了。
- 可靠性。可靠性是指一个软件的稳定程度，以及它从依次故障中恢复正常的能力。为什么说它提升了可靠性呢？因为如果服务端要管用户的会话信息的话，一旦服务端出错出现故障用户会话信息就会完全丢失，想要恢复起来机会是不可能的，所以说它的可靠性就会很差，但如果服务端不管你用户会话信息的话，那么从故障中恢复起来就回非常的容易。
- 可见性。是指在软件工程中这些模块、接口之间的透明程度。为什么说提升了可见性了呢？因为每次请求都必须包括所有信息，所以说接口之间就更加透明了。

### 缓存（Cache）
这个很好理解。是指所有服务端响应都要被标为可缓存或不可缓存，响应的资源可以被标记为可缓存或禁止缓存，如果可以缓存，那么客户端可以减少与服务器通信的次数，降低延迟、提高效率。

### 统一接口（Uniform Interface）
这个限制是所有限制中最重要的一个，别的限制如果不是在 REST 里面也可以遵循，比如CS架构，现在生活中几乎都是CS架构 了，也不一定是REST风格，比如缓存，别的风格也可以用到缓存。但是只有统一接口凸出了REST的特点，区别于其他架构风格的核心特征。（后面详细讲解）

统一接口是什么意思呢，我们分开来看：
- 统一。所谓统一指的是接口设计尽可能通用统一，遵循同一个规范，提升了简单性、可见性。
- 接口。接口与实现解耦，使前后端可以独立开发迭代。

### 分层系统（Layers）
这个限制的意思是，软件架构是分很多层的，而且每一层只知道相邻额有一层，后面隐藏的就不知道了，比如客户端不知道自己是在和代理还是在和真实的服务器通信，这里的代理就是软件分层中的一层，其它层比如：安全层、负载均衡层、缓存层等。

### 按需代码（Code-On-Demand 可选）
这是一条可选的限制，也不是很重要。所谓按需代码是指客户端可以下载运行服务端传来的代码（比如JS），按需代码的好处是通过减少一些功能，简化了客户端。


## 常见笔试题:什么是 RESTful API，如何设计RESTful API？

答案: RESTful API。为了使得接口安全、易用、可维护以及可扩展，一般设计 RESTful API需要考虑以下几个方面:
1. 通信用HTPS安全协议。
2. 在URL中加入版本号，例如"vl/animals"
3. URL中的路径（endpoint）不能有动词，只能用名词。
4. 用HTTP方法对资源进行增删改查的操作。
5. 用HTTP状态码传达执行结果和失败原因。
6. 为集合提供过滤、排序、分页等功能。
7. 用查询字符串或HTTP首部进行内容协商，指定返回结果的数据格式。
8. 及时更新文档，每个接口都有对应的说明。


## RESTful API 示例
下面是我是真实API截图，用Swagger管理，基本遵循RESTful API架构风格

![RESTful API 示例](https://raw.githubusercontent.com/dunizb/cloudimg/master/blog/article/201907/restful-api/1.png)

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
- GET `/zoos`：列出所有动物园
- POST `/zoos`：新建一个动物园
- GET `/zoos/ID`：获取某个指定动物园的信息
- PUT `/zoos/ID`：更新某个指定动物园的信息（提供该动物园的全部信息）
- PATCH `/zoos/ID`：更新某个指定动物园的信息（提供该动物园的部分信息）
- DELETE `/zoos/ID`：删除某个动物园
- GET `/zoos/ID/animals`：列出某个指定动物园的所有动物
- DELETE `/zoos/ID/animals/ID`：删除某个指定动物园的指定动物

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
- `api/getfile.php` - 获取文件信息，下载文件
- `api/uploadfile.php` - 上传创建文件
- `api/deletefile.php` - 删除文件

RESTfu，`api/file` 只需要这一个接口：
- `GET` 方式请求 `api/file` - 获取文件信息，下载文件
- `POST` 方式请求 `api/file` - 上传创建文件
- `DELETE` 方式请求 `api/file` - 删除某个文件

你的公司使用的是RESTful API吗？如果不是可以考虑辞职了，太落伍了！RESTful API 现在也要让位新宠 [GraphQL](https://graphql.cn/) 了，一种更高效、强大和灵活的数据提供方式。

![GraphQL](https://raw.githubusercontent.com/dunizb/cloudimg/master/blog/article/201907/restful-api/2.png)

*******
推荐文章
- [《前端程序员面试笔试宝典》P162](https://book.douban.com/subject/30324146/)
- [理解RESTful架构](//www.ruanyifeng.com/blog/2011/09/restful.html)
- [RESTful 架构风格概述](https://blog.igevin.info/posts/restful-architecture-in-general/)
- [前后端分离与restful api介绍](https://zhuanlan.zhihu.com/p/32901317)