---
title: Vue.js项目API、Router配置拆分实践
date: 2017-06-20 20:35:48
categories:
  - 前端框架
tags:
  - Vue.js
  - 实战
---

## 前后端分离开发方式前端拥有更高的控制权

<!-- more -->

随着前端框架技术的飞速发展，Router 这个概念也被迅速普及到前端项目中，在早期前后的没有分离的时期下，并没有明确的路由概念，前端页面跳转大多是通过后端进行请求转发的，比如在 Spring MVC 项目中，进行一个页面跳转如下（画红线部分）：
![code-0620-1.png](//ww3.sinaimg.cn/large/006tNc79ly1g5d7yf3yfej30l30ecjsv.jpg)

前端需要一个超链接，链接的 href=/manager，这样这个超链接被转发到 scs/waitFollowed 路径指定的页面。

前后的分离后，前端页面跳转的方式发生了变化，不再需要后端处理了，数据交换方式也改变了，由此前端需要定义 Router 配置文件，需要定义 API 配置文件。在项目的权限配置管理中，完全不需要后端什么事了，可以说，权限配置表可以单独拿出来由前端维护了。
![TIM截图20170620113459.png](//ww4.sinaimg.cn/large/006tNc79ly1g5d7ygz8brj30yg0dbwoa.jpg)

比如这个 url 字段，在前后端不分离的情况下，严重依赖于后端，url 就是后端接口地址，如果需要更改就需要后端修改代码修改接口地址，而现在，前端可以自由控制 url 的值是什么了。

在接口层面，前端也会有自己的配置文件，可以对后端提供的接口进行重命名，组合等。比如
![code-0620-2.png](//ww4.sinaimg.cn/large/006tNc79ly1g5d7yhixgvj30p30gaac9.jpg)

前端都统一使用`模块名+接口名`的方式管理，管后端提供的接口叫啥已经不重要，在视觉上和维护上都比较方便。在页面上使用，查询起来也很直观：
![code-0620-3.png](//ww2.sinaimg.cn/large/006tNc79ly1g5d7yie0djj30nz07jglk.jpg)

看到`DISTRBUTE().Leads.dataGrid`这个接口，就知道这是`DISTRBUTE`模块下`Leasd`功能下的列表查询接口

## Vue.js 中的 API、Router 配置

在 Vue.js 项目下，一开始我们只使用一个`api.config.js`配置文件，所有的接口都定义在这里面，router 也一样，都配置在一个`router.config.js`中，下面是我们项目中 API 配置文件
![GIF.gif](//ww2.sinaimg.cn/large/006tNc79ly1g5d7yiyghrg30cm0ggb08.gif)

可以看到，很多的业务模块，很多的接口，已经达到了 570 多行，随着业务进一步推进，接口快速膨胀，文件越来越大。

这时候迫切需要拆分，把不同的业务模块单独拆分为一个个 API 配置文件。同样，我们来看看拆分前的 Router 配置文件：
![GIF2.gif](//ww3.sinaimg.cn/large/006tNc79ly1g5d7yjtqylg30cl0ewhdt.gif)

这样 router 一多最大的缺点就是会导致 router 命名冲突。

## 拆分！拆分！拆分！

首先考虑 API 配置文件怎么拆分，对于接口，我们肯定有多套环境，多套环境那么 API 的 URL 也不一样，拆分成多个文件后多个文件需要共用同一个获取`apiBase`的方法，所以这个`apiBase`就要写在公共的地方，在这里原来的`api.config.js`就变成了公共配置，`apiBase`就放在此文件内。

```js
export function apiBase() {
  let hostname = window.location.hostname,
    API_BASE_URL = "http://test2api.dunizb.com"; //默认环境
  if (hostname === "crm.dunizb.cn") {
    //正式环境
    API_BASE_URL = "http://api.dunizb.cn";
  } else if (hostname === "admin.dunizb.com") {
    //公网测试环境
    API_BASE_URL = "http://testapi.dunizb.com";
  } else if (hostname === "manager.dunizb.com") {
    //内网测试环境
    API_BASE_URL = "http://test2api.dunizb.com";
  }
  return API_BASE_URL;
}
```

然后在每个子 API 配置文件中引入即可：

```js
import { apiBase } from "../api.config";
```

具体功能 API 不需要更改，直接拷贝相应模块 API 到子模块 API 配置文件即可。
![GIF3.gif](//ww1.sinaimg.cn/large/006tNc79ly1g5d7ykobv3g30ni0ewdmb.gif)

Router 的拆分稍微复杂一点，拆分后的文件目录与 API 的目录相同：
![code-0620-4.png](//ww1.sinaimg.cn/large/006tNc79ly1g5d7ym3w2ej30ba0d90t7.jpg)

拆分思路也完全一样，但要保证只有一个`router.start`即：

```js
return router.start(App, "#app");
```

虽然你在子 router 配置文件中也写上页面是能正常工作的，但是 Vue.js 会在控制台报一个错误：
![code-0620-5.png](//ww1.sinaimg.cn/large/006tNc79ly1g5d7ymky4wj30i608pjr8.jpg)

这个错误的意思就是 router 已经启动，无需启动多次。所以，子 router 文件中不能存在 `return router.start(App, '#app');` 这样的代码。

拆分后`router.config.js`内容如下：

```js
/**
 * 路由总文件
 * Created by Bing on 2017/6/19 0019.
 */
import App from './App';
import authority from './routers/authority';
import publics from './routers/public';
import study from './routers/study';
... ...
export default function(router){
  authority(router);//基础与权限模块
  publics(router);//公共模块
  study(router);//教学相关
  ... ...
  return router.start(App, '#app');
}
```

而子 router 配置文件的写法就是这样（以 study 模块为例）：

```js
/**
 * 教学排课
 * 教研
 * Created by Bing on 2017/6/19 0019.
 */
import courseIndex from "components/studyCourse/index/index";
import waitCourse from "components/studyCourse/waitCourse/waitCourse";
import alreadyCourse from "components/studyCourse/alreadyCourse/alreadyCourse";
import gearCourse from "components/studyCourse/waitCourse/gearCourse";
import courseWare from "components/teachingResearch/courseware/courseware.vue";
import courseWareLibrary from "components/teachingResearch/courseware/library.vue";
export default function(router) {
  router.map({
    "/study/index": { component: courseIndex },
    "/study/waitCourse": { component: waitCourse }, //待排课程
    "/study/waitCourse/gearCourse": {
      component: gearCourse,
    }, //待排
    "/study/course": { component: alreadyCourse }, //已排课程
    "/tr/courseware": { component: courseWare }, //课件管理
    "/tr/courseWare/library": {
      component: courseWareLibrary,
    }, //自主上传课件库
  });
}
```

拆分后，每个模块管理它自己领域的 router、api，router.config.js 和 api.config.js 就大大瘦身了，也降低了命名冲突的问题和将来混乱的问题。

---

此前的 Vue.js 系列文章：

- [Vue.js 常用开发知识简要入门（一）](http://dunizb.com/2016/12/18/Vue.js常用开发知识简要入门（一）)
- [Vue.js 常用开发知识简要入门（二）](//www.jianshu.com/p/ce9fc4c8a7ce)
- [Vue.js 常用开发知识简要入门（三）](http://dunizb.com/2017/02/13/Vue.js常用开发知识简要入门（三）)
- [Vue.js 开发常见问题解析](http://dunizb.com/2017/06/19/Vue.js开发常见问题解析/)
- [Vue.js 动态组件解析](http://dunizb.com/2017/06/19/Vue.js动态组件解析/)

---

本人出售《Vue.js 权威指南》，二手价 20 元！点击下图购买

**++++++++++[20 元出售此书](http://dunizb.com/obook/)++++++++++**
[![Vue.js权威指南](//ww3.sinaimg.cn/large/006tNc79ly1g5d7yn253zj30g20jkdjd.jpg)](http://dunizb.com/obook/)
