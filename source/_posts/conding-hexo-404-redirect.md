---
title: 小技巧|Coding+Hexo的博客设置404页面跳转
date: 2017-12-26 16:16:18
categories:
  - 其它
tags:
  - Hexo
  - 小技巧
description: "一句话说不清，直接看文..."
---

看这个标题可能不知道啥意思，具体点来说

## 背景

我的博客地址本来是这样的：[https://dunizb.com](https://dunizb.com)，而这几天我开发了一个新的博客独立首页，我准备用这个域名，博客用二级域名：[https://blog.dunizb.com](https://blog.dunizb.com)来访问。这就会到导致我在 SF 和掘金上的文章原文连接地址就失效了，但是已经发表了那么多文章，我可不想一篇一篇文章的去修改。。。。为了解决以前的原文连接也能正确访问不报 404，就是这么回事。

<!-- more -->

## Coding Pages

Coding Pages 是由[https://coding.net](https://coding.net)提供的跟 GitHub Pages 一样的服务，它比 GitHub Pages 的优点就在于不会被墙，提供更加丰富的功能，最重要的还是两点：

1. 不会被墙！访问速度快
2. 提供 HTTPS 服务

我的博客（Hexo）之前使用 GitHub Pages，现在迁移到 Coding Pages 上来了，还提供免费的 HTTPS 服务，一键开启你的网站 HTTPS 之旅。

## 解决老链接访问 404 的问题

Coding Pages 本身自带 404 错误页面，当你访问你的博客中一个不存在的文章链接时，就会显示 Coding Pages 的 404 页面，如下：
![Coding Pages的404页面](https://raw.githubusercontent.com/dunizb/cloudimg/master/blog/article/201712/coding-hexo-404/404.jpg)

 其实 Hexo 也可以设置自己的 404 页面的，具体可以查看 Hexo 文档。当你设置了 Hexo 的 404 后，访问不存在的页面就会跳转到 Hexo 你设置的 404 页面。

而对于我的情况而言，由于我的博客域名已经变成二级域名：blog.dunizb.com， 一级域名 dunizb.com 已经作为独立新首页使用了， 这个新首页和博客放在两个仓库中，现在访问以前的一个页面地址是这样的：

> 原文连接：https://dunizb.com/2017/12/08/You-have-to-know-the-basic-HTTP-concepts/

很明显，这个域名指向新首页， 所以我设置 Hexo 的 404 页面是没用的，访问依然会走 Coding Pages 的 404 页面。
![Coding Pages的404页面](https://raw.githubusercontent.com/dunizb/cloudimg/master/blog/article/201712/coding-hexo-404/404_3.png)

于是我在想我怎么样不去修改以前的链接也能照样跳转到对应的文章呢？

于是我  我在博客独立新首页项目中建一个 404 页面来覆盖 Coding Pages 的 404 页面，z 只要你在你的项目中有 404 页面，Coding Pages 就默认会去读你的 404 页面。然后在我的 404 页面中做一些小工作就行了。

现在你可以访问这个链接  体验一下：[https://dunizb.com/2017/12/08/You-have-to-know-the-basic-HTTP-concepts/](https://dunizb.com/2017/12/08/You-have-to-know-the-basic-HTTP-concepts/)

我的 404 页面代码如下:

```html
<template lang="html">
  <div ref="page" class="page">
    <img src="../../assets/images/404.jpg" />
    <h1>很抱歉，你所访问的页面不存在了，可能是我调整了网站域名所至</h1>
    <h3>可能是我变更了网站URL，你可以试试下面的解决方案：</h3>
    <p>
      <a href="javascript:;" @click="to"> 【点击这里试试】 </a> 或者 修改当前URL的域名为：blog.dunizb.com
    </p>
    <p>
      <p>页面找不到了，不要慌，你可以随便到处看看其他的东西</p>
      <a href="https://dunizb.com">网站首页</a> - <a href="https://blog.dunizb.com">博客首页</a>
      - <a href="https://store.dunizb.com">我的小店</a> - <a href="https://blog.dunizb.com/categories/%E5%89%8D%E7%AB%AF%E5%BC%80%E5%8F%91/">前端开发</a>
    </p>
  </div>
</template>

<script>
  export default {
    mounted () {
      if (confirm('该页面URL可能已经变更，是否去新的地址？')) {
        this.to()
      }
    },
    methods: {
      to () {
        var url = window.location.href
        // 'https://dunizb.com/2017/12/08/You-have-to-know-the-basic-HTTP-concepts/'
        location.href = 'https://blog.dunizb.com/' + url.split('.com')[1]
      }
    }
  }
</script>

<style lang="scss">
@import "../../assets/scss/main.scss";
.page{
  text-align:center;
  img{margin: 0 auto;}
}
</style>
```

长相如下：
![我的404页面](https://raw.githubusercontent.com/dunizb/cloudimg/master/blog/article/201712/coding-hexo-404/404_2.png)

很简单，就是简单的转发而已、、、、首先询问用户是否直接跳转到修改好的  地址页面，【to()方法】自己把域名改成 blog.dunizb.com 然后重新跳转....很简单粗暴的页面。这样我其他社区  发的文章我就不用管了。

就纯前端来说，我觉得这只能这样做了 ，你还有  更好的方式？欢迎交流

---

Hexo 圣诞彩蛋
![Hexo 圣诞彩蛋](https://raw.githubusercontent.com/dunizb/cloudimg/master/blog/article/201712/coding-hexo-404/hexo-sd.png)
