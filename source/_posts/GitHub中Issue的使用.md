---
title: GitHub中Issue的使用
date: 2016-04-28 23:21:00
categories:
  - 其它
tags:
  - github
---

在软件开发过程中，开发者们为了跟踪 BUG 及进行软件相关讨论，进而方便管理，创建了 Issue。管理 Issue 的系统称为 BTS（Bug Tracking System，Bug 跟踪系统）。当今具有代表性的 BTS 有[Redmine](//www.redmine.org/)、[Trac](http://trac.edgewall.org/)、[BugZilla](https://www.bugzilla.org/)等。

<!-- more -->

GitHub 自身也加入了 BTS 的功能。在 GitHub 上，可以将它作为软件开发者之间的交流工具，多多加以利用。遇到下面几种情况时，各位就可以使用这个功能。

- 发现软件的 BUG 并报告
- 有事想向作者询问、探讨
- 事先列出今后准备实施的任务

Issue 除 BUG 管理之外还有许多其他用途。在软件开发者圈子中，将 Issue 用于多种用途的情况已经司空见惯。作为 GitHub 的功能之一，我们来学习 Issue 的一些简单用法。

# 简洁且表现力丰富的描述方法

GitHub 的 Issue 及评论可以使用 GFM（Github Flavored Markdown）语法进行描述，从而获得丰富的表现力。比如像下图 1 那样描述，然后点击 Preview，就可以看到图 2 中那种标记后的效果。
![1.png](//ww4.sinaimg.cn/large/006tNc79ly1g5d89uezzhj30ll065wen.jpg)
注意：“#”和后面的文字之间要有一个空格，否则无效
![2.png](//ww3.sinaimg.cn/large/006tNc79ly1g5d89uv766j30lx08kq3a.jpg)
在文本框的下面可以找到 GFM 语法样式相关帮助的链接
![3.png](//ww3.sinaimg.cn/large/006tNc79ly1g5d89vt28bj30ku02rjra.jpg)

### 1. 语法高亮

假设我们像下面这样，先指定语言再描述代码。

```js
var viewName = function(userName) {
  alert("你好，" + userName + "，欢迎光临！");
};
```

以上 JavaScript 代码，这样一来，代码就会自动如图 3 所示被添加语法高亮，变得直观易读。
![4.0.png](//ww3.sinaimg.cn/large/006tNc79ly1g5d89x6dytj30jl05pjrd.jpg)

### 2.添加图片

添加图片也十分简单，只需要将图片拖拽到文本框中便可以粘贴图片。

### 3.添加标签以便整理

Issue 可以通过添加标签（Label）来进行整理。添加标签后，Issue 的标题后面就会显示标签（图 4）。点击标签，还可以只显示该类标签的 Issue，GitHub 默认给我们设定了几个标签类型。
![4.png](//ww2.sinaimg.cn/large/006tNc79ly1g5d89y4x8rj30s30a03za.jpg)

### 4.添加里程碑以便管理

处标签外，还可以通告添加里程碑来管理 Issue。通过图 5 可以看出，项目距离下一个版本还有 12 个 Issue 需要实施，整体的 14%已经实施完毕并 Close。从这里的链接我们可以看出剩余的 Issue。

![5.png](//ww1.sinaimg.cn/large/006tNc79ly1g5d89z4qb6j30sj0bb0te.jpg)

注意：在添加 Issue 时常会看到图 5 中这种贡献规范的链接。改仓库的根目录下添加 CONTRIBUTING.md 文件后该链接就会显示出来。

![6.png](//ww3.sinaimg.cn/large/006tNc79ly1g5d8a02e97j30lw06kjrr.jpg)

规范的内容一般包括报告时 Issue 的描述方法、Pull Request 时的规则或要求、许可证的相关信息等。为了在开源项目开发中能与其他人和谐相处，请务必在贡献之前仔细阅读这些规则。

### 5.Tasklist 语法

我们使用 GFM 的一项独有功能，那就是 Tasklist(任务列表)语法。首先试着按下面的格式进行描述。

```
# 本月要做的任务
- [x] 完成部署工具的设置
- [ ] 博客文章更新
- [ ] 实现抽奖功能
```

这样一来，这段文字就会呗标记成复选框列表的样式。这个复选框列表可以直接勾选或者取消，不必打开 Issue 的编辑页面重新编辑，十分方便。
![7.png](//ww2.sinaimg.cn/large/006tNc79ly1g5d8a0yks7j30lr066glv.jpg)

# 通过提交信息操作 Issue

在 GitHub 上，只要按照特定的格式描述提交信息，就可以像一般 BTS 带有的功能那样对 Issue 进行操作。

### Close Issue

如果一个处于 Open 状态的 Issue 已经处理完毕，只要在该提交中以下列任意一种格式描述提交信息，对应的 Issue 就会被 Close。

- fix #24
- fixes #24
- fixed #24
- close #24
- closes #24
- closed #24
- resolve #24
- resolves #24
- resolved #24

利用这个方法，每次提交并 Push 之后，就不必再大费周章地到 GitHub 的 Issue 中寻找相应的 Issue 手动 Close，省去了不少麻烦。像这样，只要按照特定的格式描述提交信息，Github 就会自动识别并处理，很多 GitHub 之外的 BTS 也实现了这一功能。
