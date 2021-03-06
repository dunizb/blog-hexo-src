---
title: 小技巧|在GitHub中查看分支差别
date: 2016-04-18 11:05:00
categories:
  - 其它
tags:
  - github
  - 小技巧
---

在 GitHub 上，直接修改 URL 就可以让用户以多种形式查看差别。这里我以[Ruby on rails](https://github.com/rails/rails)的仓库为例，给各位介绍直接修改 URL 的一些技巧。

<!-- more -->

# 查看分支之间的差别

比如我们想看 1-2-stable 分支与 2-0-stable 分支之间的差别，可以像下面这样将分支名加到 URL 里。

```
https://github.com/rails/rails/compare/1-2-stable...2-0-stable
```

这样，就可以查看两个分支间的差别了。可以看到，有 12 名程序员经过 1989 次提交，完成了 1.2 版本到 2.0 版本的升级工作。
![1.png](//ww1.sinaimg.cn/large/006tNc79ly1g5d8fdvaakj30s60grabr.jpg)

# 查看与几天前的差别

加入我们想查看 master 分支在最近 7 天的差别，可以像下面这样将时间加入 URL。

```
https://github.com/rails/rails/compare/master@{7.day.ago}...master
```

这样，就可以查看这段时间内的差别。
![2.png](//ww1.sinaimg.cn/large/006tNc79ly1g5d8feuk8sj30ru0f9jsw.jpg)

- day
- week
- month
- year

指定期间可以使用以上四个时间单位。如果差别过大则不会列出所有提交，只显示最近的一部分。

# 查看与指定日期之间的差别

假如我们想看 master 分支 2013 年 1 月 1 日与现在的区别，可以将日期加入 URL。

```
https://github.com/rails/rails/compare/master@{2013-01-01}...master
```

这样，便可以查看与指定日期之间的差别。但是如果指定日期与现在的差别过大，或者指定日期过于久远，则无法显示。
![3.png](//ww2.sinaimg.cn/large/006tNc79ly1g5d8flc50sj30ri0gjgmv.jpg)
