---
title: 强大的Http监控工具Fidder简单介绍
date: 2015-08-30 18:47:07
categories:
- 其他
tags:
- http
description: "Fiddler是一个http调试代理，它能 够记录所有的你电脑和互联网之间的http通讯，Fiddler 可以也可以让你检查所有的http通讯，设置断点，以及Fiddle 所有的“进出”的数据（指cookie,html,js,css等文件，这些都可以让你胡乱修改的意思）。 Fiddler 要比其他的网络调试器要更加简单，因为它仅仅暴露http通讯还有提供一个用户友好的格式。"
---

## 1、简介与安装
Fiddler是一个http调试代理，它能 够记录所有的你电脑和互联网之间的http通讯，Fiddler 可以也可以让你检查所有的http通讯，设置断点，以及Fiddle 所有的“进出”的数据（指cookie,html,js,css等文件，这些都可以让你胡乱修改的意思）。 Fiddler 要比其他的网络调试器要更加简单，因为它仅仅暴露http通讯还有提供一个用户友好的格式。

Fiddler 包含一个简单却功能强大的基于JScript .NET 事件脚本子系统，他非常灵活性非常棒，可以支持众多的http调试任务。Fiddler 是用C#写出来的。

软件下载：
http://fiddler2.com/get-fiddler

软件学习：
//www.cnblogs.com/TankXiao/archive/2012/02/06/2337728.html#request

Fiddler还支持丰富的插件，官方也停供大量的插件：
//www.telerik.com/fiddler/add-on

## 2、Fiddler工作原理
![Fiddler工作原理](//ww2.sinaimg.cn/large/006tNc79ly1g5d7wzjr0yj30gp06y0ty.jpg)

两种代理模式：
- 流模式（streaming）：更接近浏览器的真实情况
- 缓冲模式（buffering）：HTTP请求所有的数据都准备好之后才把数据返回给客户端

## 3、使用场景
- 开发环境HOST配置：通常情况下配置host需要修改系统文件，很不方便；
- 在多个环境下切换很低效;Fiddler提供了先对高效的配置方法；
- 前后端接口调试：通常情况下调试前后端接口需要真实的环境、一大堆的假
 数据、写JavaScript代码。Fiddler只需一个UI界面配置即可。
- 线上bug fix：Fiddler可将发布文件代理到本地，快速定位线上Bug。
- 性能分析和优化：Fiddler会提供请求的实际图，清晰明了网站需要优化的地方。

## 4、工具条、状态栏常用功能
工具条:
![工具条](//ww4.sinaimg.cn/large/006tNc79ly1g5d7x1y9daj30yg0am108.jpg)

状态栏:
![状态栏](//ww4.sinaimg.cn/large/006tNc79ly1g5d7x2yjkxj30rr05r3yt.jpg)

## 5、监控面板的使用
![监控面板的使用](//ww2.sinaimg.cn/large/006tNc79ly1g5d7x4h39mj30yg07mte7.jpg)

## 6、文件、文件夹代理和Host配置
![Host配置](//ww1.sinaimg.cn/large/006tNc79ly1g5d7x5rt21j30l40c3wg0.jpg)