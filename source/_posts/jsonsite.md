---
title: 开源推荐|JSONsite：使用JSON文件创建SPA页面
date: 2020-12-13 23:48:25
img: https://weixin-storage.oss-cn-shanghai.aliyuncs.com/202012/jsonsite/1.gif
categories:
  - 技术
tags:
  - 开源推荐
---

发现一个开源项目，可以让你用一个 JSON 文件创建一个网站。

> [Github：https://github.com/jsonsite/jsonsite](https://github.com/jsonsite/jsonsite)

做一个 fork，添加你的 URL，然后咣当一声，一个好看的网站就用 JSON 文件做出来了。而且所有的网站都是由 Vercel 托管的，他们有惊人的正常运行时间和 CDN。

<!-- more -->

听起来不错，但是如何运作？

- JSONsite 将从提供的 URL 中获取 JSON 文件
- 然后，JSONsite 将开始解析这些数据，并将其传递给 nunjucks。
- 从 nunjucks 输出的 HTML 将被最小化
- 您的网站将在您选择的 Slug 时准备好!

所以基本上这样：

```json
{
  "title": "我的网站",
  "description": "The amazing website of John Doe",
  "image": "https://cdn.glitch.com/1788ed8a-5cc6-45e9-a3b6-18d6457af699%2Fundraw_profile_pic_ic5t.png?v=1606325421049",
  "author": "Zhang Zhang",
  "theme": {
    "navbar_color": "dark",
    "jumbotron_color": "light",
    "footer_color": "light"
  },
  "pages": [
    {
      "title": "主页",
      "id": "main",
      "content": "Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Auctor urna nunc id cursus. Maecenas ultricies mi eget mauris pharetra et ultrices. Nunc consequat interdum varius sit. Suspendisse sed nisi lacus sed. Tempor id eu nisl nunc mi ipsum faucibus vitae. Urna nec tincidunt praesent semper feugiat nibh sed pulvinar. Euismod quis viverra nibh cras pulvinar mattis nunc sed blandit. Sit amet consectetur adipiscing elit ut aliquam purus sit amet. Platea dictumst quisque sagittis purus sit amet volutpat consequat. Interdum velit laoreet id donec ultrices tincidunt arcu non. Et netus et malesuada fames. Ipsum faucibus vitae aliquet nec ullamcorper sit. Ultricies mi eget mauris pharetra et. Etiam tempor orci eu lobortis elementum nibh tellus molestie. Dolor sit amet consectetur adipiscing. Sed tempus urna et pharetra pharetra massa massa ultricies mi. Ac tincidunt vitae semper quis lectus nulla at. Odio ut sem nulla pharetra diam sit amet. Viverra adipiscing at in tellus."
    },
    {
      "title": "关于",
      "id": "about",
      "content": "Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Luctus accumsan tortor posuere ac ut consequat semper viverra. Pharetra magna ac placerat vestibulum lectus mauris. Scelerisque in dictum non consectetur a erat nam at lectus. Vel pharetra vel turpis nunc eget lorem dolor sed viverra. Duis ultricies lacus sed turpis tincidunt id aliquet risus feugiat. Gravida in fermentum et sollicitudin. Quam vulputate dignissim suspendisse in est ante in nibh mauris. Sit amet mauris commodo quis. Bibendum enim facilisis gravida neque convallis a. Quis imperdiet massa tincidunt nunc pulvinar. Leo a diam sollicitudin tempor id. Sit amet facilisis magna etiam. Pharetra sit amet aliquam id diam maecenas ultricies. Nulla at volutpat diam ut venenatis tellus. Eget lorem dolor sed viverra ipsum nunc. Lobortis scelerisque fermentum dui faucibus in. Amet cursus sit amet dictum sit amet justo donec enim. Posuere urna nec tincidunt praesent semper feugiat."
    },
    {
      "title": "联系",
      "id": "contact",
      "content": "Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Augue ut lectus arcu bibendum at varius. Hac habitasse platea dictumst vestibulum rhoncus est. Tincidunt vitae semper quis lectus nulla at volutpat diam. Eu non diam phasellus vestibulum lorem sed risus ultricies. Posuere lorem ipsum dolor sit amet consectetur. Mauris sit amet massa vitae tortor. Malesuada fames ac turpis egestas maecenas pharetra convallis posuere. Diam volutpat commodo sed egestas egestas. Orci sagittis eu volutpat odio facilisis. Dui ut ornare lectus sit amet. Nisl vel pretium lectus quam id leo in vitae turpis. Pharetra et ultrices neque ornare aenean euismod elementum nisi quis. Arcu non sodales neque sodales ut etiam sit amet. Scelerisque purus semper eget duis at. Ac turpis egestas sed tempus urna et pharetra. Platea dictumst quisque sagittis purus."
    }
  ],
  "footer": "&copy; 2020 John Doe. All Rights Reserved.",
  "javascript": "console.log('Oooh look, custom JavaScript!')",
  "css": "/* You can put custom CSS here! */"
}
```

会变成这样：

![](https://weixin-storage.oss-cn-shanghai.aliyuncs.com/202012/jsonsite/1.gif)

全部集中在一页，一个站点上。 “页面”是用 JavaScript 切换的，但是由于它们都在一页上，因此速度非常快。

从 Github 下载项目，通过运行 `yarn start` 启动项目，但是他需要加载远程的 json 文件，从这里配置：

![](https://weixin-storage.oss-cn-shanghai.aliyuncs.com/202012/jsonsite/2.png)

我在本机上通过 `http-server` node 包快速启动了一个 HTTP 服务，在启动的时候它就会加载这个 json。

---

原文：[https://blog.zhangbing.site/2020/12/13/jsonsite/](https://blog.zhangbing.site/2020/12/13/jsonsite/)
