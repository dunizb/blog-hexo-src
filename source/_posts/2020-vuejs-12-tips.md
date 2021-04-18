---
title: 2020年的12个Vue.js开发技巧和窍门
date: 2020-04-14 15:20:37
img: https://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202004/vue-12-tip/1.jpeg
top: true
categories:
  - 前端框架&工具
tags:
  - Vue.js
summary: "十个很酷的提示和技巧，你可能尚未意识到这些技巧和窍门，以帮助你成为更好的 Vue 开发人员"
---

我真的很喜欢使用 Vue.js，每次使用框架时，我都会喜欢深入研究其功能和特性。通过这篇文章，我向你介绍了十个很酷的提示和技巧，你可能尚未意识到这些技巧和窍门，以帮助你成为更好的 Vue 开发人员。

<!-- more -->

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202004/vue-12-tip/1.jpeg)

## 更漂亮的插槽语法

随着 Vue 2.6 的推出，已经引入了插槽的简写方式，可用于事件（例如，`@click` 表示 `v-on:click` 事件）或冒号表示方式用于绑定（`:src`）。例如，如果你有一个表格组件，你可以使用这个功能如下：

```html
<template>
  ...
  <my-table>
    <template #row="{" item }>
      /* 一些内容，你可以在这里自由使用“item” */
    </template>
  </my-table>
  ...
</template>
```

## \$on(‘hook:’) 可以帮助你简化代码

删除事件监听器是一种常见的最佳实践，因为它有助于避免内存泄露并防止事件冲突。

如果你想在 `created` 或 `mounted` 的钩子中定义自定义事件监听器或第三方插件，并且需要在 `beforeDestroy` 钩子中删除它以避免引起任何内存泄漏，那么这是一个很好的特性。下面是一个典型的设置：

```javascript
mounted () {
    window.addEventListener('resize', this.resizeHandler);
},
beforeDestroy () {
    window.removeEventListener('resize', this.resizeHandler);
}
```

使用 `$on('hook:')`方法，你可以仅使用一种生命周期方法（而不是两种）来定义/删除事件。

```javascript
mounted () {
  window.addEventListener('resize', this.resizeHandler);
  this.$on("hook:beforeDestroy", () => {
    window.removeEventListener('resize', this.resizeHandler);
  })
}
```

## \$on 还可以侦听子组件的生命周期钩子

最后一点，生命周期钩子发出自定义事件这一事实意味着父组件可以监听其子级的生命周期钩子。

它将使用正常模式来监听事件（@event），并且可以像其他自定义事件一样进行处理。

```html
<child-comp @hook:mounted="someFunction" />
```

## 使用 immediate: true 在初始化时触发 watcher

[Vue Watchers](https://learnvue.co/2019/12/a-simple-vue-watcher-tutorial-for-beginners/) 是添加高级功能（例如，API 调用）的好方法，该功能可以在观察值发生变化时运行。

默认情况下，观察者不会在初始化时运行。根据你的功能，这可能意味着某些数据不会完全初始化。

```javascript
watch: {
  title: (newTitle, oldTitle) => {
    console.log(
      "Title changed from " + oldTitle + " to " + newTitle
    );
  };
}
```

如果你需要 wather 在实例初始化后立即运行，那么你所要做的就是将 wather 转换为具有 `handler(newVal, oldVal)` 函数以及即时 `immediate: true` 的对象。

```javascript
watch: {
    title: {
      immediate: true,
      handler(newTitle, oldTitle) {
        console.log("Title changed from " + oldTitle + " to " + newTitle)
      }
    }
}
```

## 你应该始终验证你的 Prop

验证 Props 是 Vue 中的基本做法之一。

你可能已经知道可以将 props 验证为原始类型，例如字符串，数字甚至对象。你也可以使用自定义验证器——例如，如果你想验证一个字符串列表：

```javascript
props: {
  status: {
    type: String,
    required: true,
    validator: function (value) {
      return [
        'syncing',
        'synced',
        'version-conflict',
        'error'
      ].indexOf(value) !== -1
    }
  }
}
```

## 动态指令参数

Vue 2.6 的最酷功能之一是可以将指令参数动态传递给组件。假设你有一个按钮组件，并且在某些情况下想监听单击事件，而在其他情况下想监听双击事件。这就是这些指令派上用场的地方：

```html
<template>
  ...
  <aButton @[someEvent]="handleSomeEvent()" />...
</template>
<script>
  ...
  data(){
    return{
      ...
      someEvent: someCondition ? "click" : "dbclick"
    }
  },
  methods: {
    handleSomeEvent(){
      // handle some event
    }
  }
</script>
```

而且，这实际上也很整洁-你可以将相同的模式应用于动态 HTML 属性，props 等。

## 重用相同路由的组件

开发人员经常遇到的情况是，多个路由解析为同一个 Vue 组件。问题是，Vue 出于性能原因，默认情况下共享组件将不会重新渲染，如果你尝试在使用相同组件的路由之间进行切换，则不会发生任何变化。

```javascript
const routes = [
  {
    path: "/a",
    component: MyComponent,
  },
  {
    path: "/b",
    component: MyComponent,
  },
];
```

如果你仍然希望重新渲染这些组件，则可以通过在 `router-view` 组件中提供 `:key` 属性来实现。

```html
<template>
  <router-view :key="$route.path"></router-view>
</template>
```

## 把所有 Props 传到子组件很容易

这是一个非常酷的功能，可让你将所有 props 从父组件传递到子组件。如果你有另一个组件的包装组件，这将特别方便。所以，与其把所有的 props 一个一个传下去，你可以利用这个，把所有的 props 一次传下去：

```html
<template>
  <childComponent v-bind="$props" />
</template>
```

代替：

```html
<template>
  <childComponent
    :prop1="prop1"
    :prop2="prop2"
    :prop="prop3"
    :prop4="prop4"
    ...
  />
</template>
```

## 把所有事件监听传到子组件很容易

如果子组件不在父组件的根目录下，则可以将所有事件侦听器从父组件传递到子组件，如下所示：

```html
<template>
	<div>
    ...
		<childComponentv-on="$listeners" />...
  <div>
</template>
```

如果子组件位于其父组件的根目录，则默认情况下它将获得这些组件，因此不需要使用这个小技巧。

## \$createElement

默认情况下，每个 Vue 实例都可以访问 `$createElement` 方法来创建和返回虚拟节点。例如，可以利用它在可以通过 v-html 指令传递的方法中使用标记。在函数组件中，可以将此方法作为渲染函数中的第一个参数进行访问。

## 使用 JSX

由于 Vue CLI 3 默认支持使用 JSX，因此现在（如果愿意）你可以使用 JSX 编写代码（例如，可以方便地编写函数组件）。如果尚未使用 Vue CLI 3，则可以使用 `babel-plugin-transform-vue-jsx` 获得 JSX 支持。

## 自定义 v-model

默认情况下，`v-model` 是 `@input` 事件侦听器和 `:value` 属性上的语法糖。但是，你可以在你的 Vue 组件中指定一个模型属性来定义使用什么事件和 value 属性——非常棒！

```javascript
export default: {
  model: {
    event: 'change',
    prop: 'checked'
  }
}
```

## 总结

这绝不是 VueJS 技巧的完整列表，这些只是我个人认为最有用的一些，其中一些技巧使我花了很长时间才在 Vue 中进行实践，因此我认为我可以与大家分享这些知识。

希望他们像我一样对你有帮助！

**你最喜欢的 VueJS 技巧和窍门是什么？我也很想向你学习！**

---

参考链接：

- [medium.com/better-prog…](https://medium.com/better-programming/10-tips-and-tricks-to-make-you-a-better-vue-js-developer-afc74acaf388)
- [learnvue.co/2020/01/7-s…](https://learnvue.co/2020/01/7-simple-vuejs-tips-you-can-use-to-become-a-better-developer/)
