---
title: Vue.js项目中管理每个页面的头部标签的方法
date: 2018-06-24 19:51:18
categories:
  - 前端框架
tags:
  - Vue.js
summary: "如何为每个页面都设置不一样的 title 呢？下面介绍两种方法。"
---

在 Vue SPA 应用中，如果想要修改 HTML 的头部标签，如页面的 `title`，我们只能去修改`index.html`模板文件，但是这个是全局的修改，如何为每个页面都设置不一样的 `title` 呢？下面介绍两种方法。

<!-- more -->

## 使用 router.meta

在路由里面配置每个路由的地址：

```js
routes: [
  {
    /* （首页）默认路由地址 */
    path: "/",
    name: "Entrance",
    component: Entrance,
    meta: {
      title: "首页入口",
    },
  },
  {
    /* 修改昵称 */
    path: "/modifyName/:nickName",
    name: "modifyName",
    component: modifyName,
    meta: {
      title: "修改昵称",
    },
  },
];
```

在每一个`meta`里面设置页面的`title`名字，最后在遍历

```js
router.beforeEach((to, from, next) => {
  /* 路由发生变化修改页面title */
  if (to.meta.title) {
    document.title = to.meta.title;
  }
  next();
});
```

这样就为每一个 VUE 的页面添加了 title。

## 使用 vue-meta 插件

vue-meta 主要用于管理 HMTL 头部标签，同时也支持 SSR。vue-meta 有以下特点：

- 在组件内设置 `metaInfo`，便可轻松实现头部标签的管理
- `metaInfo` 的数据都是响应的，如果数据变化，头部信息会自动更新
- 支持 SSR

在页面里面增加 `metaInfo` 选项

```js
export default {
  data() {
    return {
      myTitle: '标题'
    }
  },
  metaInfo: {
    title: this.title,
    titleTemplate: '%s - by - vue-meta',
    htmlAttris: {
      lang: 'zh'
    },
    script: ''
  }
  ... ...
}
```

更多 vue-meta 使用请参考 Github 官网：[https://github.com/declandewet/vue-meta](https://github.com/declandewet/vue-meta)
