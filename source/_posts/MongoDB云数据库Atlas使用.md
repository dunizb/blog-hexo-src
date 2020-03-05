---
title: 【图文教程】新手友好的MongoDB云数据库Atlas如何使用？
date: 2019-11-10 21:16:53
categories:
- 技术
tags:
- NodeJS
- MongoDB
---

学习使用 MongoDB 官方提供的免费云数据库，初学者的学习利器，手把手图文教程。
<!-- more -->
![MongoDB Atlas](https://gitee.com/dunizb/cloudimg/raw/jsdelivr/atlas/banner.png)

## 1. 云数据库 Atlas
如果你想在本地安装 MongoDB 可以去官网下载，MongoDB 支持 Windows、OSX、Linux，虽然你可以在你的电脑上下载安装 MongoDB，但作为初学研究学习，其实没必要这么折腾，除非你天天在本机用。所以，一个更好的使用方法就是云MongoDB，云 MongoDB 就是把 MongoDB 安装在远程的服务器上，并对外暴露一个服务地址，我们用这个服务地址来连接数据库进行操作，其实我们现在公司开发都是使用云数据库，比如阿里云 RDS 服务。

使用云数据库及 Atlas 的好处在于：
- 支持更大规模的存储
- 更安全
- 是免本地安装
- 无需手动开启，每次直接链接即可 
- 维护简单，不需要我们去维护数据的升级、安装等等，这些都交给云服务厂商去做了

使用 Atlas 的的缺点：最大的缺点就是有点慢！毕竟服务器在国外。其次只能创建一个集群，这个到无所谓，作为学习使用一个集群就够了，毕竟一个集群中可以创建N个数据库啊。

在国内，云大厂有阿里云、腾讯云等，但是都是收费的，而 MongoDB 官方也提供了 [MongoDB Atlas](https://www.mongodb.com/cloud/atlas/register)，它有免费版和收费版，免费版就适合我们学习使用。


## 2. 注册、创建和配置 Atlas
第1步，首先注册用户：[https://www.mongodb.com/cloud/atlas/register](https://www.mongodb.com/cloud/atlas/register)，创建后来到如下界面，填写组织名，云服务默认选择 MongoDB Atlas 即可。

![](https://gitee.com/dunizb/cloudimg/raw/jsdelivr/atlas/0.png)

第2步，添加成员并设置权限，可不填

![](https://gitee.com/dunizb/cloudimg/raw/jsdelivr/atlas/01.png)

创建成功后来到如下页面

![](https://gitee.com/dunizb/cloudimg/raw/jsdelivr/atlas/02.png)

第3步，创建一个Project，点击 New Project按钮，输入项目名称

![](https://gitee.com/dunizb/cloudimg/raw/jsdelivr/atlas/03.png)

然会又会来到类似第2步的页面提示增加成员并设置权限，可不填，点击 Create Project 按钮继续，然后会来到创建集群的页面

![](https://gitee.com/dunizb/cloudimg/raw/jsdelivr/atlas/04.png)

第4步，创建集群，选择创建免费的集群

![](https://gitee.com/dunizb/cloudimg/raw/jsdelivr/atlas/05.png)

选择服务商和节点

![](https://gitee.com/dunizb/cloudimg/raw/jsdelivr/atlas/06.png)

有两个选择，推荐选择第2个，毕竟香港更靠近大陆，速度更快。
1. AWS + Singapore(新加坡)
2. Azure + Hongkong(香港)【推荐】

然后填写集群名称，如果不知道取什么名字那就默认为Cluster0吧，然后点击 Create Cluster 按钮

![](https://gitee.com/dunizb/cloudimg/raw/jsdelivr/atlas/07.png)

集群创建中。。。

![](https://gitee.com/dunizb/cloudimg/raw/jsdelivr/atlas/08.png)

创建成功后显示如下

![](https://gitee.com/dunizb/cloudimg/raw/jsdelivr/atlas/09.png)

## 3. 配置数据库相关信息
第1步，添加数据库用户，配置用户名密码，用于连接 MongoDB 时登录

![](https://gitee.com/dunizb/cloudimg/raw/jsdelivr/atlas/010.png)

第2步，把IP地址添加到白名单里面

![](https://gitee.com/dunizb/cloudimg/raw/jsdelivr/atlas/011.png)

到这一步Atlas就创建和配置成功了。


## 4. 连接到 Atlas 集群
创建和配置好Atlas 集群后，我们就可以用 Mongoose 模块和 MongoDB 客户端工具进行连接了。

在连接之前，我们先拿到数据库连接信息，点击集群页面的 Connect 按钮，然后选择第二个

![](https://gitee.com/dunizb/cloudimg/raw/jsdelivr/atlas/12.png)

然后就可以Copy连接字符串了（将您的连接字符串添加到您的应用程序代码中）

![](https://gitee.com/dunizb/cloudimg/raw/jsdelivr/atlas/13.png)

我的连接字符串如下：
```
mongodb+srv://zhangbing:<password>@cluster0-jarma.azure.mongodb.net/test?retryWrites=true&w=majority
```

### 4.1 NoSQLBooster 连接 Atlas
MongoDB 客户端工具有很多，免费好用的这里推荐 [NoSQLBooster for MongoDB](https://nosqlbooster.com/)，支持Mac OS 和 Windows 系统，软件界面略有过时，由曾经风靡一时的 jQuery EasyUI 构建，界面美观程度还过得去。

![](https://gitee.com/dunizb/cloudimg/raw/jsdelivr/atlas/home-intellisense-v5.gif)

打开 NoSQLBooster 连接 MongoDB，选择 From URI，输入上面拿到的连接字符串，注意替换连接里面的`<password>`为你的 MongoDB 连接密码，比如123321。

![](https://gitee.com/dunizb/cloudimg/raw/jsdelivr/atlas/14.png)

然后点击 Test Connection 按钮进行连接测试，出现如下情况即连接成功！

![](https://gitee.com/dunizb/cloudimg/raw/jsdelivr/atlas/15.png)

关闭窗口，点击OK按钮，在点击OK按钮保存连接信息

![](https://gitee.com/dunizb/cloudimg/raw/jsdelivr/atlas/16.png)

双击连接信息即可进入

![](https://gitee.com/dunizb/cloudimg/raw/jsdelivr/atlas/17.png)

到了这一步，就成功了！

### 4.2 使用 mongoose 连接
```
const mongoose = require('mongoose')

const connection = 'mongodb+srv://zhangbing:123321@cluster0-jarma.azure.mongodb.net/test?retryWrites=true&w=majority'
mongoose.connect(connection, { 
    useUnifiedTopology: true,
    useNewUrlParser: true,
    useFindAndModify: true 
}, () => console.log('mongoose连接成功了！'))
mongoose.connection.on('error', console.error)
```

全文完。
