---
title: 实战Parcel构建一个基于Vue.js的相册应用
date: 2018-05-08 15:20:40
categories:
- 前端开发
tags:
- Vue.js
- Parcel
description: "零配置前端打包工具实战"
---
![](https://dunizb.b0.upaiyun.com/article/201805/parcel/banner.png)

前段时间发了一篇[《前端构建工具发展及其比较》](https://blog.dunizb.com/2018/04/23/%E5%89%8D%E7%AB%AF%E6%9E%84%E5%BB%BA%E5%B7%A5%E5%85%B7%E5%8F%91%E5%B1%95%E5%8F%8A%E5%85%B6%E6%AF%94%E8%BE%83/)，回顾了前端构建工具的发展历程和进化，其中最新出来的零配置打包工具[Parcel](https://parceljs.org/)我一直很好奇，它到底怎么零配置了？众所周知此前 Webpack 的配置简有点让人茫然和无措，虽然现在 Webpack 4 也号称零配置，但也是相对的，依然需要配置一些东西，而我使用了 Parcel 后我有点惊讶，这货居然连个配置文件也不需要，不像 Webpack 需要一个`webpack.config.js`这样的文件，Parcel真正是不需要配置，不需要指定什么入口、出口、插件配置之类的，看起来这货真的是个零配置工具。
<!-- more -->

## 实例介绍

Parcel有个中文网站：[https://parceljs.org/](https://parceljs.org/)，非常简洁，文档也比较清晰，但感觉也有点简陋吧，不然就不会那么简洁了。具体就不多说了，看一看官网就知道了。

我以这两天做的一[个人相册](https://photo.dunizb.com)应用为例子，这是一个Parcel结合Vue.js+VueRouter实现的一个简单应用，主要功能是展示相册列表，让后点击某个相册进入照片瀑布流布局页面，展示该相册下的所有照片。**全部源码戳[这里](https://github.com/dunizb/parceljs-vue-photo)**

对着官网文档搭建环境到跑起来，硬是花了我几个小时消化，试错，搜索等。下面是相册应用的整体目录：
![](https://dunizb.b0.upaiyun.com/article/201805/parcel/parcel_0.png)
这个目录结构大家做过`Vue.js`项目的应该很清楚吧，就把一一介绍是什么了。

## 开始

### 安装依赖
首先在你正在使用的项目目录下创建一个 `package.json` 文件，然后安装`npm install parcel-bundler --save`这个包，这是使用Parcel必须的，注意使用 Vue 需要安装`parcel-plugin-vue`，而不是直接安装vue，`parcel-bundler`是主要的工具，对于vue结尾的单文件，需要单独处理文件类型。使用vue-router安装`vue-router`，如果你需要使用 Less 或 Sass 安装相应包即可，这里我使用 Sass 安装`node-sass`。

### 配置babel，postcss

添加`postcss.config.js`文件：
```js
module.exports = {
    plugins: [
      require('autoprefixer')({ 
        browsers: [
          'last 20 versions',
          'IE 9',
          'iOS >= 8'
        ]
      })
    ]
  }
```
添加`.babelrc`文件：
```js
{
  "presets": [
    ["env"]
  ]
}
```

### 新建html

在根目录添加 index.html，只需有一个 #root 节点，然后引入`./src/index.js`即可。
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name=viewport content="width=device-width,user-scalable=no,initial-scale=1,maximum-scale=1,minimum-scale=1">
  <link href="//blog.dunizb.com/favicon.ico" rel="shotcut icon">
  <title>我的相册</title>
</head>
<body>
  <div id="root"></div>
  <script src="./src/index.js"></script>
</body>
</html>
```

### 添加脚本

在 package.json 的 scripts 中添加`dev`和`build`两个命令
```js
"scripts": {
    "dev": "parcel index.html",
    "build": "parcel build index.html --public-url /",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
```
只需要执行`npm run dev` 和 `npm run build` 就可以进行开发和构建，`public-url`就相当于资源的引用路径。

## 配置Vue和VueRouter

在 src 下的 index.js 中配置即可
```js
import Vue from 'vue'
import VueRouter from 'vue-router'
import createRouter from './config/router.js'
import App from './app.vue'
import gloable from './assets/styles/global.css'
Vue.use(VueRouter)
const router = createRouter()
new Vue({
  el: '#root',
  router,
  render: (h) => h(App)
});
```

config 目录下是 Router 的配置

router.js，这是 router 的主文件
```
import Router from 'vue-router'
import routes from './routers.js'
export default () => {
  return new Router({
    routes
  })
}
```
routers.js，这是具体路由的配置
```js
import Index from '../views/index.vue'
import List from '../views/list.vue'
export default [
  {
    path: '/',
    component: Index,
  },
  {
    path: '/list/:id',
    props: true,
    component: List,
  }
]
```
到这里环境搭建就算完成了，写好vue页面后，就可以执行`npm run dev`了，Parcel会自动读取脚本里的配置进行打包，然后会在根目录生成一个`dist`文件夹，里面的代码就是打包后的文件了，并且自动做了压缩操作。

并且Parcel的输出也是很美观
![](https://dunizb.b0.upaiyun.com/article/201805/parcel/parcel_1.png)
![](https://dunizb.b0.upaiyun.com/article/201805/parcel/parcel_2.png)

## 后记

全程没有配置什么插件啊，转换器啊，对于vue文件我们也只是安装了一个包而已，没有类似`parcel.config.js`这样的文件，是不是很酷？对于简单的项目是很好的选择。

为什么说适合简单的项目？因为没有配置，意味着可控性不可控，人类对于不可控的东西是怀有很大的恐惧的，Webpack配置多了让人抓狂，Pacel了配置少了同样会让人抓狂，当然也许这个实例太简单还没用到什么高级的东西....

喜欢折腾个人项目的，还不快来试试？

## 源码

[全部源码：https://github.com/dunizb/parceljs-vue-photo](全部源码：https://github.com/dunizb/parceljs-vue-photo)
