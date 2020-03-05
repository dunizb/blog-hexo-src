---
title: 小技巧|package.json中homepage属性的作用
date: 2020-02-25 21:18:52
categories:
- 技术
tags:
- 前端
- 小技巧
---

做前端开发的同学对 `package.json` 文件一定不陌生，但我们通常很少去关注它，最熟悉的莫过于几个最基本的属性<!-- more -->，如：
- name，项目名称
- version，项目版本号
- dependencies，项目依赖包
- scripts，npm命令

`package.json` 其实还有很多属性可以配置的，这里就介绍一个 `homepage` 属性的作用。

`homepage` 的作用是设置应用的跟路径，我们的项目打包后是要运行在一个域名之下的，有时候可能是运行在跟域名下，也有可能运行在某个子域名下或或域名的某个目录下，这时候我们就需要让我们的应用知道去哪里加载资源，这时候就需要我们设置一个跟路径，而且有时候我们的资源会部署在 CDN 上，你必须告诉打包工具你的CDN地址是什么。

比如我们用 `create-react-app` 开发的 React 应用，以及 Vue CLI 开发的项目，默认是继承了 webpack 的，当不配置 `homepage` 属性，build 打包之后的文件资源应用路径默认是 `/` ，如下图

![](https://gitee.com/dunizb/cloudimg/raw/jsdelivr/20200226/1.png)

当你设置了 `homepage` 属性后，比如我这里`homepage` 设置为 github 的 pages 服务地址

![](https://gitee.com/dunizb/cloudimg/raw/jsdelivr/20200226/3.png)

打包后的资源路径就会加上 `homepage` 的地址。比如上面图片配置好 `homepage` 之后我打包一个 React 项目，打包后 `index.html` 页面的资源路径就是：

![](https://gitee.com/dunizb/cloudimg/raw/jsdelivr/20200226/2.png)

全文完。
