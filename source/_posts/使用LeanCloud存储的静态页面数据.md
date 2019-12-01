---
title: 使用LeanCloud存储的静态页面数据
date: 2019-12-01 21:44:26
categories:
- 技术
tags:
- 前端
- 云开发
---

Serverless 云开发是现在的大热门和趋势，各大云服务厂商都已经支持 FaSS(函数即服务) 云开发方式，微信小程序云开发是典型的例子。
<!-- more -->

## 背景
我的[博客](https://www.dunizb.com/)有个[“我的小铺”](https://store.dunizb.com/)频道，是我个人书籍出售展示页面，其实是一个静态页面，托管在 [coding.net](https://coding.net/) 每次更新页面都要打开源码编辑代码然后 push 到服务器中，此前一直是这么做的，这个过程也很简单没什么问题。但是由于 coding.net 已经卖身腾讯云了，个人版服务很不稳定，直到前段时间push代码后coding.net一直部署出错，修改代码后真实环境数据无改变，不得不得重新关闭Page服务再打开。

于是决定把数据存在某个地方，不用每次都push代码，以后修改只需要后台数据库添加一行数据新页面就更新即可。

## 方案
最先想到的是找云服务，比如云数据库之类的，于是一通趴拉和寻找，试过阿里云、APICloud、腾讯云等等，都不是我想要的，要么一时半会儿不会用😅，要么不提供HTTP API，要么免费一个月后面要收费，我就一丁点儿数据犯不着，最后发现了 [LeanCloud](https://www.leancloud.cn/) 最符合我的要求。

![LeanCloud.png](https://i.loli.net/2019/12/01/ljV4IgqL6GfKpOy.png)

LeanCloud 的数据存储服务个人用户可免费使用一定容量，不需要提供域名，而且提供RESTful API用于Web页面调用，简单方便。

## 使用 LeanCloud
### 注册和创建表
LeanCloud 注册后首先要实名验证，这个很简单，只需要提供身份证号码即可、完善相关开发者信息后创建应用

![image.png](https://i.loli.net/2019/12/01/tvdr6cBLVlX9FRe.png)

填写应用名称，选择开发版

![image.png](https://i.loli.net/2019/12/01/aQ63EKRxBGYWOgU.png)

上述操作都无误后会有如下界面，存储 - 结构化数据，创建Class其实创建一个数据表，如果你懂关系型数据库如MySQL的话你应该很明白。

![image.png](https://i.loli.net/2019/12/01/EnOgkF1Cueq8xQN.png)

创建Class后，就可以为表添加字段了，点击添加列添加你想要的字段

![image.png](https://i.loli.net/2019/12/01/j9BZVziIR48PJm2.png)

之后就可以添加行，为你的的列字段输入值

![image.png](https://i.loli.net/2019/12/01/PJCUgH9dwc3aRMm.png)

### 在页面中调用
LeanCloud 提供了 JavaScript SDK 和用于 Web页面的 CDN 链接（[官方文档](https://leancloud.cn/docs/start.html))
```
<script src="//cdn.jsdelivr.net/npm/leancloud-storage@4.0.0/dist/av-min.js"></script>
```

由于我的页面很简单，所以也不用什么Ajax请求库了，直接使用 Fetch API，只是为了渲染页面使用了Vue.js CDN。全部JS代码如下
```javascript
const baseUrl = 'https://oobzicxg.lc-cn-n1-shared.com/1.1/'
const header = {
    'x-avoscloud-application-id': '你的应用AppID',
    'x-avoscloud-session-token': '你的应用AppID',
    'x-avoscloud-application-key': '你的应用AppKey'
}

new Vue({
    el: '#app',
    data: {
        books: [],
        softwares: []
    },
    created() {
        const _this = this
        const apiUrl = baseUrl + 'classes/BlogStore?limit=50&&order=-updatedAt&&'
        fetch(apiUrl, {
            headers: header
        })
        .then((response) => response.json())
        .then((res) => {
            this.books = res.results.filter((item) => item.type === 'book')
            this.softwares = res.results.filter((item) =>  item.type === 'software')
        }).catch((err) => {
            console.log('err', err)
        });
    }
})
```
然后页面就是 `v-for` 循环了。

应用 AppID 和应用 AppKey 在设置 - 应用 Keys 中可以查看

![image.png](https://i.loli.net/2019/12/01/YcbxmpyfRF1l3we.png)

这样就完成了，只需要在后台向Class中修改数据页面一刷新就可以看到变化了，不需要去动代码了。

*************
关注公众号，第一时间接收最新文章。如果对你有一点点帮助，可以点喜欢点赞点收藏，还可以小额打赏作者，以鼓励作者写出更多更好的文章。
<img src="https://i.loli.net/2019/11/06/SdgA4QFiTzMeHyI.jpg" />
