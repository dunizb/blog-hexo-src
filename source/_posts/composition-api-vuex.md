---
title: 是否应该使用Composition API替代Vuex？
date: 2020-11-22 17:40:32
categories:
  - 技术
tags:
  - Vue.js
---

Composition API 已从 Vue 组件和实例中释放了响应数据，使其可以像任何 JavaScript 对象一样传递，这有一个明显的应用：全局响应状态。如果可以实现，那么您需要 Vuex 做什么？

在本文中，我将向您展示如何使用 Composition API 的特性替换 Vuex，并说明这是否是一个明智的想法。

<!-- more -->

## Composition API 的全局响应式状态

Vuex 存在的主要原因是在 Vue 应用程序中允许全局响应式状态，如果没有它，你只能通过组件接口即 props/events 共享本地组件状态。对于 Vue 用户来说，从组件的四到五层传递 props 和事件是一种非常熟悉的苦差事。

但是，Composition API 使您可以在组件外部（实际上，在 Vue 应用程序外部）创建独立的响应式变量。例如，您可以创建一个导出响应式变量的模块文件，并将其导入到要使用它的任何组件中。

## DIY Vuex 与 Composition API

让我们看看如何使用 Composition API 推出我们自己的 Vuex。首先，我们将创建一个文件来声明和导出全局状态变量。

让我们使用 `reactive` 方法来创建一个具有值 `count` 的状态对象，假设我们想在整个应用程序中使用这个 count 值。

```javascript
// src/global.js

import { reactive } from "vue";

const state = reactive({
  count: 0,
});

export default { state };
```

## Vuex 模式

正如 Vuex 文档所说，Vuex 既是模式又是库。为了使状态可预测，重要的是不要直接对变量进行突变。

我们可以在 DIY Vuex 中轻松地实现这一点。我们将使用 `readonly` 方法，该方法将创建状态的只读副本。我们还将提供一种方法 `increment` ，用户可以使用它来更改 count 的值（类似于 Vuex 突变）。

```javascript
// src/global.js

import { reactive, readonly } from "vue";

const state = reactive({
  count: 0,
});

const increment = function() {
  state.count++;
};

export default { state: readonly(state), increment };
```

## 使用 provide/inject 安装 store

使用我们的 DIY Vuex store 的最简单方法是 provide/inject 功能。因此，我们将在应用程序的根实例中导入这个模块，然后 `provide` 它，使其对所有子组件可用。

```javascript
// src/main.js

import { createApp } from "vue";
import global from "@/global";

const app = createApp({
  provide: {
    global
  },
  ...
}
```

现在，我们可以使用 `inject` 来访问它：

```html
// src/components/MyComponent.vue

<template></template>
<script>
  export default {
    inject: ["global"],
  };
</script>
```

现在可以使用状态值和方法：

```html
// src/components/MyComponent.vue

<template>
  <div>{{ global.state.count }}
  <button @click="global.increment">Increment</button>
</template>

<script>
export default {
  inject: ["global"]
}
</script>
```

## 那你应该抛弃 Vuex 吗？

我们已经看到了如何使用 Composition API 推出我们自己的 Vuex。在这样做的过程中，我们克服了很多关于 Vuex 的抱怨，因为我们有：

- 最小样板
- 没有像“mutations”，“commits”等深奥的命名
- 没有其他依赖项。

那么为什么不完全抛弃 Vuex 呢？

## Vuex 1 的优势：调试

虽然我们复制了全局反应状态机制，但 Vuex 有一个巨大的优势没有复制，那就是它的调试能力。

首先，使用 Vuex 跟踪所有突变，从而使您能够在 Vue Devtools 中查看哪些组件更改了状态以及如何更改了状态。

其次，是 time-travel 调试，这是 Vuex 的一个功能，你可以选择应用的任何之前的状态。

## Vuex 2 的优势：插件

Vuex 的另一个优势是插件生态系统。例如，vuex- persistent state 插件允许你在本地存储中持久化应用程序状态，Vue-Router 插件在存储状态中同步当前路由数据。

所有现有的插件确实可以复制为 Composition API 帮助程序，但是如果没有 Vuex 的标准化结构，它们将无法即插即用。

## 总结

在一个小项目中使用 Composition API 为全局响应状态创建一个简单的模块没有什么坏处，或者如果避免任何开销真的很重要的话。

但 Vuex 的调试功能仍然使其成为大规模 Vue 应用开发的必备工具，我无法想象在这种情况下我会停止使用它。

期待 Vuex 在 5 版本中的下一步发展。

---

原文：[https://vuejsdevelopers.com](https://vuejsdevelopers.com/2020/10/05/composition-api-vuex/)

作者：Anthony Gore
