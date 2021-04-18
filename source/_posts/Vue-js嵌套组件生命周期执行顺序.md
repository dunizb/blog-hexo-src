---
title: Vue.js嵌套组件生命周期执行顺序
date: 2018-12-05 16:06:55
categories:
  - 前端框架&工具
tags:
  - Vue.js
---

一次面试被问到的问题，第一次还确实有点懵逼，特此记录下来。

<!-- more -->

有组件 A，组件 B，组件 C，组件 C 是组件 B 的子组件，组件 B 又是组件 A 的子组件，那么直观的层级结构如下：

```
ComponentA
--ComponentB
----ComponentC
```

问：他们之间生命周期函数的调用顺序是什么？

下面的代码表示上述结构，全局注册了三个组件 Comp、childrenA 和 childrenB。在 childrenA 中引用 childrenB，在 Comp 中 引用 childrenA。

```javascript
Vue.component("comp", {
  template: "<div><children-a></children-a></div>",
  beforeCreate() {
    console.log("Comp beforeCreate");
  },
  created() {
    console.log("Comp created");
  },
  beforeMount() {
    console.log("Comp beforeMount");
  },
  mounted() {
    console.log("Comp mounted");
  },
});

Vue.component("childrenA", {
  template: "<div><children-b></children-b></div>",
  beforeCreate() {
    console.log("--childrenA beforeCreate");
  },
  created() {
    console.log("--childrenA created");
  },
  beforeMount() {
    console.log("--childrenA beforeMount");
  },
  mounted() {
    console.log("--childrenA mounted");
  },
});
Vue.component("childrenB", {
  template: "<div>{{text}}</div>",
  data() {
    return {
      text: "childrenB",
    };
  },
  beforeCreate() {
    console.log("----childrenB beforeCreate");
  },
  created() {
    console.log("----childrenB created");
  },
  beforeMount() {
    console.log("----childrenB beforeMount");
  },
  mounted() {
    console.log("----childrenB mounted");
  },
});
new Vue({
  el: "#app",
});
```

输出结果如下：

```
Comp beforeCreate
Comp created
Comp beforeMount
--childrenA beforeCreate
--childrenA created
--childrenA beforeMount
----childrenB beforeCreate
----childrenB created
----childrenB beforeMount
----childrenB mounted
--childrenA mounted
Comp mounted
```

可以看到，先执行父级的 beforeCreate、created 和 beforeMount，然后再去执行子组件的 beforeCreate、created 和 beforeMount，如果子组件下面没有子组件了，就执行 mounted，然后再返回父级执行 mounted。

整个过程用图表示如下：

![Vue嵌套组件生命周期执行顺序](https://i.loli.net/2019/11/06/bNsCSUq4wilyL3r.png)

完！

---

扫一扫关注我的公众号，第一时间接收最新文章，如果觉得文章对你有帮助，可以点个赞点个好看鼓励一下作者。

![扫一扫关注我的公众号，第一时间接收最新文章](https://myimgcloud.oss-cn-hangzhou.aliyuncs.com/关注名片-大礼包_横版二维码_2020-01-01-0.jpg)
