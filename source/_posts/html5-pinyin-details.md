---
title: HTML5：给汉字加拼音？收起展开组件？让我秀给你看
date: 2020-04-07 14:44:19
categories:
  - 技术
tags:
  - 前端
  - HTML
---

来看看 HTML 的历史和规范常识。HTML 规范是 W3C 与 WHATWG 合作共同产出的，HTML5 因此也不例外。其中：
- W3C 指 World Wide Web Consortium
- WHATWG 指 Web Hypertext Application Technology Working Group

<!-- more -->

说好听了是“合作产出”，但其实更像是“HTML5 有两套规范”。但话说天下大势合久必分，分久必合，如今（就在前几天，2018.5.29）它们又表示将会开发单一版本的 HTML 规范。

HTML5新增的标签和功能，常规的我相信大家都知道，这里就不啰嗦了，这里介绍两个大家可能不知道的功能，很实用！

## 给汉字加拼音

代码如下

```html
<ruby>
  做工程师不做码农
  <rt>zuo gong cheng shi bu zuo ma nong</rt>
</ruby>
```

效果如下

![](https://user-gold-cdn.xitu.io/2020/4/7/17153500916fcfe0?w=1530&h=586&f=png&s=88299)

## 展开收起组件

简单几行代码

```html
<details>
  <summary>公众号《前端外文精选》</summary>
  欢迎大家关注我的公众号，专注大前端、全栈、程序员成长的公众号！如果对你有所启发和帮助，可以点个关注、收藏，也可以留言讨论，这是对作者的最大鼓励。
  作者简介：Web前端工程师，全栈开发工程师、持续学习者。
</details>
```

就可以实现如下效果

![](https://user-gold-cdn.xitu.io/2020/4/7/171535115788b38f?w=704&h=860&f=gif&s=540056)

是不是很棒啊 🤪

以往要实现这样的内容，我们都必须依靠 JavaScript 实现。现在来看，HTML 也变得更加具有“可交互性”。

## 原生进度条和度量

**progress** 标签显示进度：

![](https://user-gold-cdn.xitu.io/2020/4/7/171535226273cb9c?w=1240&h=413&f=jpeg&s=26018)

值得一提的是：`progress` 不适合用来表示度量衡，如果想表示度量衡，我们应该使用 `meter` 标签代替。这又是什么标签？

`meter` 用来度量给定范围（gauge）内的数据：

```html
<meter value="3" min="0" max="10"></meter> 十分之三<br>
<meter value="0.6"></meter> 60%
```

Chrome显示效果如下

![](https://user-gold-cdn.xitu.io/2020/4/7/1715353285f1a8d9?w=872&h=146&f=png&s=10150)

本文示例效果和完整代码已放在我的博客[小码](https://coding.zhangbing.site)页面。


![](https://user-gold-cdn.xitu.io/2020/4/7/1715354001840ed8?w=2558&h=1172&f=png&s=460797)

