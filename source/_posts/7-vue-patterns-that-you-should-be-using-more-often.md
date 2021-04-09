---
title: 你应该经常使用的7种Vue模式
date: 2021-04-09 20:05:38
  - 技术
tags:
  - Vue.js
summary: "让我们看一下那些有趣但不那么流行的功能，请记住，所有这些都是 Vue 文档的一部分"
---

说实话，阅读文档并不是我们大多数人喜欢的事情，但当使用像 Vue 这样不断发展的现代前端框架时，很多东西会随着每一个新版本的发布而改变，你可能会错过一些后来推出的新的闪亮功能。让我们看一下那些有趣但不那么流行的功能，请记住，所有这些都是 Vue 文档的一部分。

## 1.处理加载状态

在大型应用程序中，我们可能需要将应用程序划分为更小的块，只有在需要时才从服务器加载组件。为了使这一点更容易，Vue 允许你将你的组件定义为一个工厂函数，它异步解析你的组件定义。Vue 只有在需要渲染组件时才会触发工厂函数，并将缓存结果，以便将来重新渲染。2.3 版本的新功能是，异步组件工厂也可以返回一个如下格式的对象。

```js
const AsyncComponent = () => ({
  // 要加载的组件（应为Promise）
  component: import("./MyComponent.vue"),
  // 异步组件正在加载时要使用的组件
  loading: LoadingComponent,
  // 加载失败时使用的组件
  error: ErrorComponent,
  // 显示加载组件之前的延迟。默认值：200ms。
  delay: 200,
  // 如果提供并超过了超时，则会显示error组件。默认值:无穷。
  timeout: 3000,
});
```

通过这种方法，你有额外的加载和错误状态、组件获取的延迟和超时等选项。

## 2.廉价的“v-once”静态组件

在 Vue 中渲染纯 HTML 元素的速度非常快，但有时你可能有一个包含大量静态内容的组件。在这种情况下，你可以通过在根元素中添加 `v-once` 指令来确保它只被评估一次，然后进行缓存，就像这样。

```js
Vue.component("terms-of-service", {
  template: `
    <div v-once>
      <h1>Terms of Service</h1>
      ... a lot of static content ...
    </div>
  `,
});
```

## 3.递归组件

组件可以在自己的模板中递归调用自己，但是，它们只能通过 `name` 选项来调用。

如果你不小心，递归组件也可能导致无限循环：

```js
name: 'stack-overflow',
template: '<div><stack-overflow></stack-overflow></div>'
```

像上面这样的组件会导致“超过最大堆栈大小”的错误，所以要确保递归调用是有条件的即(使用 `v-if` 最终将为 `false`）

## 4.内联模板

当特殊属性 `inline-template` 存在于一个子组件上时，该组件将使用它的内部内容作为它的模板，而不是将其视为分布式内容，这允许更灵活的模板编写。

```html
<my-component inline-template>
  <div>
    <p>
      These are compiled as the component's own template.
    </p>
    <p>Not parent's transclusion content.</p>
  </div>
</my-component>
```

## 5.动态指令参数

指令参数可以是动态的。例如，在 `v-mydirective:[argument]=“value"` 中， `argument` 可以根据组件实例中的数据属性更新！这使得我们的自定义指令可以灵活地在整个应用程序中使用。

这是一条指令，其中可以根据组件实例更新动态参数：

```js
<div id="dynamicexample">
  <h3>Scroll down inside this section ↓</h3>
  <p v-pin:[direction]="200">I am pinned onto the page at 200px to the left.</p>
</div>
Vue.directive('pin', {
  bind: function (el, binding, vnode) {
    el.style.position = 'fixed'
    var s = (binding.arg == 'left' ? 'left' : 'top')
    el.style[s] = binding.value + 'px'
  }
})

new Vue({
  el: '#dynamicexample',
  data: function () {
    return {
      direction: 'left'
    }
  }
})
```

## 6.事件和键修饰符

对于 `.passive`、`.capture` 和 `.once` 事件修饰符，Vue 提供了可与 `on` 一起使用的前缀：

![](/Users/zhangbing/Desktop/7-vue-patterns/1.png)

例如：

```js
on: {
  '!click': this.doThisInCapturingMode,
  '~keyup': this.doThisOnce,
  '~!mouseover': this.doThisOnceInCapturingMode
}
```

对于所有其他的事件和键修饰符，不需要专有的前缀，因为你可以在处理程序中使用事件方法。

![](/Users/zhangbing/Desktop/7-vue-patterns/2.png)

## 7.依赖注入(Provide/Inject)

有几种方法可以让两个组件在 Vue 中进行通信，它们各有优缺点。在 2.2 版本中引入的一种新方法是使用 Provide/Inject 的依赖注入。

这对选项一起使用，允许一个祖先组件作为其所有子孙的依赖注入器，无论组件层次结构有多深，只要它们在同一个父链上。如果你熟悉 React，这与 React 的上下文功（context）能非常相似。

```js
// parent component providing 'foo'
var Provider = {
  provide: {
    foo: "bar",
  },
  // ...
};

// child component injecting 'foo'
var Child = {
  inject: ["foo"],
  created() {
    console.log(this.foo); // => "bar"
  },
  // ...
};
```

---

翻译自[medium.com](https://medium.com/js-dojo/7-vue-patterns-that-you-should-be-using-more-often-b13cde4d2ae6)，作者：Fotis Adamakis
