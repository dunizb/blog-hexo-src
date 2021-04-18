---
title: 迎接Vue3.0 | 在Vue2与Vue3中构建相同的组件
date: 2020-04-18 17:04:44
categories:
  - 前端框架
tags:
  - Vue.js
---

4 月 16 日，Vue 开发团队终于在今天发布了 3.0-beta.1 版本，也就是测试版。通常来说，从测试版到正式版，只会修复 bug，不会引入新功能，或者删改老功能。所以，如果你对新版本非常感兴趣，或者有新项目即将上马，不妨尝试一下新版本。

<!-- more -->

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/vue-circles.jpg)

随着 Vue3 即将发布，许多人都在想“ Vue2 与 Vue3 有何不同？”

为了显示这些更改，我们将在 Vue2 和 Vue3 中构建一个简单的表单组件。

在本文结尾，你将了解 Vue2 和 Vue3 之间的主要编程差异，并逐步成为一名更好的开发人员。

## 创建我们的模板

对于大多数组件，Vue2 和 Vue3 中的代码即使不完全相同，也是非常相似的。但是，Vue3 支持**Fragments**，这意味着组件可以具有多个根节点。

在渲染列表中的组件以删除不必要的包装 div 元素时，这特别有用。但是，在这种情况下，我们将为两个版本的 Form 组件保留一个根节点。

Vue2

```html
<template>
  <div class="form-element">
    <h2>{{ title }}</h2>
    <input
      type="text"
      v-model="username"
      placeholder="Username"
    />

    <input
      type="password"
      v-model="password"
      placeholder="Password"
    />

    <button @click="login">
      Submit
    </button>
    <p>
      Values: {{ username + ' ' + password }}
    </p>
  </div>
</template>
```

唯一真正的区别是我们访问数据的方式。在 Vue3 中，我们的响应式数据都包装在响应式状态变量中——因此我们需要访问该状态变量以获取我们的值。

Vue3

```html
<template>
  <div class="form-element">
    <h2>{{ state.title }}</h2>
    <input
      type="text"
      v-model="state.username"
      placeholder="Username"
    />

    <input
      type="password"
      v-model="state.password"
      placeholder="Password"
    />

    <button @click="login">
      Submit
    </button>
    <p>
      Values: {{ state.username + ' ' + state.password }}
    </p>
  </div>
</template>
```

## 设置 Data

**这是主要的区别——Vue2 Options API 与 Vue3 [Composition API](https://learnvue.co/)。**

Options API 将我们的代码分为不同的属性：数据，计算属性，方法等。同时，Composition API 允许我们按功能而不是属性类型对代码进行分组。

假设对于表单组件，我们只有两个数据属性：`username` 和 `password`。

Vue2 代码看起来是这样的——我们只需在 `data` 属性中放入两个值。

Vue2

```javascript
export default {
  props: {
    title: String,
  },
  data() {
    return {
      username: "",
      password: "",
    };
  },
};
```

在 Vue 3.0 中，我们必须投入更多的精力来使用一个新的 `setup()` 方法，所有的组件初始化都应该在这个方法中进行。

另外，为了使开发人员能够更好地控制响应式，我们可以直接访问 Vue 的响应式 API。

创建响应式数据涉及三个步骤：

- 从 Vue 导入 `reactive`
- 使用 `reactive` 方法声明我们的数据
- 让我们的 `setup()` 方法返回`reactive`数据，以便我们的模板可以访问它

在代码方面，它将看起来像这样。

Vue3

```javascript
import { reactive } from "vue";

export default {
  props: {
    title: String,
  },
  setup() {
    const state = reactive({
      username: "",
      password: "",
    });

    return { state };
  },
};
```

然后，在模板中，我们像 `state.username` 和 `state.password` 一样访问它们

## 在 Vue2 与 Vue3 中创建方法

Vue2 Options API 有一个单独的方法部分。在其中，我们可以定义所有方法并以所需的任何方式组织它们。

Vue2

```javascript
export default {
  props: {
    title: String,
  },
  data() {
    return {
      username: "",
      password: "",
    };
  },
  methods: {
    login() {
      // login method
    },
  },
};
```

[Vue3 Composition API](https://learnvue.co/)中的 setup 方法也可以处理方法。它的工作方式与声明数据有些类似——我们必须先声明我们的方法，然后返回它，以便组件的其他部分可以访问它。

Vue3

```javascript
export default {
  props: {
    title: String,
  },
  setup() {
    const state = reactive({
      username: "",
      password: "",
    });

    const login = () => {
      // login method
    };
    return {
      login,
      state,
    };
  },
};
```

## 生命周期钩子函数

在 Vue2 中，我们可以直接从组件选项访问[生命周期钩子](https://learnvue.co/2019/12/a-beginners-guide-to-vuejs-lifecycle-hooks/)函数。对于我们的示例，我们将等待 mounted 事件。

Vue2

```javascript
export default {
  props: {
    title: String,
  },
  data() {
    return {
      username: "",
      password: "",
    };
  },
  mounted() {
    console.log("component mounted");
  },
  methods: {
    login() {
      // login method
    },
  },
};
```

现在有了 Vue3 Composition API，几乎所有内容都在 `setup()` 方法内部，这包括 mounted 的生命周期钩子。

但是，默认情况下不包括生命周期挂钩，因此我们必须导入 `onMounted` 方法，作为 Vue3 中调用的方法，这看起来与早期导入 reactive 相同。

然后，在我们的 `setup()` 方法中，可以通过将 `onMounted` 方法传递给函数来使用它。

Vue3

```javascript
import { reactive, onMounted } from "vue";

export default {
  props: {
    title: String,
  },
  setup() {
    // ..

    onMounted(() => {
      console.log("component mounted");
    });

    // ...
  },
};
```

## 计算属性

让我们添加一个计算属性，将我们的用户名转换为小写字母。为了在 Vue2 中完成此操作，我们将一个计算字段添加到我们的 options 对象中。

Vue2

```javascript
export default {
  // ..
  computed: {
    lowerCaseUsername() {
      return this.username.toLowerCase();
    },
  },
};
```

[Vue3 的设计](https://learnvue.co/2019/12/how-vue3-is-designed-for-both-hobby-devs-and-large-projects/)允许开发人员导入他们使用的内容，而在项目中没有使用的不需要导入。本质上，他们不希望开发人员必须包含他们从未使用过的东西，这在 Vue2 中已经成为一个日益严重的问题。

因此，要在 Vue3 中使用计算属性，我们首先必须将 computed 导入到组件中。

然后，类似于我们之前创建 reactive 数据的方式，我们可以使一条 reactive 数据成为这样的计算值：

Vue3

```javascript
import { reactive, onMounted, computed } from "vue";

export default {
  props: {
    title: String,
  },
  setup() {
    const state = reactive({
      username: "",
      password: "",
      lowerCaseUsername: computed(() =>
        state.username.toLowerCase()
      ),
    });

    // ...
  },
};
```

## 访问属性（Props）

访问 Props 带来了 Vue2 和 Vue3 之间的一个重要区别——**这意味着完全不同的东西**。

在 Vue2 中，这几乎总是引用组件，而不是特定的属性，虽然这使事情表面上很容易，但它使类型支持成为一种痛苦。

以往，我们都可以轻松访问 Props——让我们添加一个简单的示例，例如在 mounted 的钩子上打印出标题 prop：

Vue2

```javascript
mounted () {
  console.log('title: ' + this.title)
}
```

但是在 Vue3 中，我们不再使用它来访问 Props、发出事件和获取属性。

相反，`setup()` 方法采用两个参数：

- `props`——对组件 prop 的不可变访问
- `context`——Vue3 公开的选定属性（emit、slots、 attrs）

使用 props 参数，上面的代码将如下所示。

Vue3

```javascript
setup (props) {
  // ...

  onMounted(() => {
    console.log('title: ' + props.title)
  })

  // ...
}
```

## 发送事件（Emitting Events）

类似地，在 Vue2 中发出事件非常简单，但是 Vue3 为你提供了对如何访问属性/方法的更多控制。

例如，在我们的例子中，我们想在按下“Submit”按钮时向父组件发出登录事件。

Vue2 代码只需要调用 `this.$emit`并传入我们的有效参数对象即可。

Vue2

```javascript
login () {
  this.$emit('login', {
    username: this.username,
    password: this.password
  })
 }
```

然而，在 Vue3 中，我们现在知道这不再意味着同样的事情，所以我们必须做得不同。

幸运的是，上下文对象(context)公开了 `emit`，这使我们拥有与此相同的东西。

我们要做的就是将 context 添加为 `setup()` 方法的第二个参数，我们将解构上下文对象，以使我们的代码更简洁。

然后，我们只需要调用 emit 发送事件即可。然后，像以前一样，emit 方法采用两个参数：

- 事件名称
- 与事件一起传递的有效参数对象

Vue3

```javascript
setup (props, { emit }) {
  // ...

  const login = () => {
    emit('login', {
      username: state.username,
      password: state.password
    })
  }

  // ...
}
```

## 最终的 Vue2 与 Vue3 代码！

如你所见，Vue2 和 Vue3 中的所有概念都是相同的，但是我们访问属性的某些方式已经有所变化。

总的来说，我认为 Vue3 将帮助开发人员编写更有组织的代码——特别是在大型代码库中。这主要是因为 Composition API 允许你按特定功能将代码分组在一起，甚至可以将功能提取到自己的文件中，然后根据需要将其导入组件中。

Vue2 中用于表单组件的完整代码：

```html
<template>
  <div class="form-element">
    <h2>{{ title }}</h2>
    <input
      type="text"
      v-model="username"
      placeholder="Username"
    />

    <input
      type="password"
      v-model="password"
      placeholder="Password"
    />

    <button @click="login">
      Submit
    </button>
    <p>
      Values: {{ username + ' ' + password }}
    </p>
  </div>
</template>
<script>
  export default {
    props: {
      title: String,
    },
    data() {
      return {
        username: "",
        password: "",
      };
    },
    mounted() {
      console.log("title: " + this.title);
    },
    computed: {
      lowerCaseUsername() {
        return this.username.toLowerCase();
      },
    },
    methods: {
      login() {
        this.$emit("login", {
          username: this.username,
          password: this.password,
        });
      },
    },
  };
</script>
```

这是在 Vue3 中的完整代码：

```html
<template>
  <div class="form-element">
    <h2>{{ state.title }}</h2>
    <input
      type="text"
      v-model="state.username"
      placeholder="Username"
    />

    <input
      type="password"
      v-model="state.password"
      placeholder="Password"
    />

    <button @click="login">
      Submit
    </button>
    <p>
      Values: {{ state.username + ' ' + state.password }}
    </p>
  </div>
</template>
<script>
  import { reactive, onMounted, computed } from "vue";

  export default {
    props: {
      title: String,
    },
    setup(props, { emit }) {
      const state = reactive({
        username: "",
        password: "",
        lowerCaseUsername: computed(() =>
          state.username.toLowerCase()
        ),
      });

      onMounted(() => {
        console.log("title: " + props.title);
      });

      const login = () => {
        emit("login", {
          username: state.username,
          password: state.password,
        });
      };

      return {
        login,
        state,
      };
    },
  };
</script>
```

我希望本教程有助于重点介绍 Vue 代码在 Vue3 中看起来与众不同的某些方式。如有其他疑问，请留下你的意见！

---

原文：https://learnvue.co/2020/02/building-the-same-component-in-vue2-vs-vue3/

作者：[Matt Maribojoc](https://learnvue.co/author/matthewmaribojoc/)
