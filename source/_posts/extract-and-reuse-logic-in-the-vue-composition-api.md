---
title: 迎接Vue3.0|Vue3 Composition API中的提取和重用逻辑
date: 2020-04-22 21:19:27
img: https://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202004/vue-composition-api-and/banner.jpg
categories:
  - 前端框架
tags:
  - Vue.js
---

Vue3 Composition API 可以在大型项目中更好地组织代码。然儿，随着使用几种不同的选项属性切换到单一的 `setup` 方法，许多开发人员面临的问题是... ...。

<!-- more -->

> 这会不会更混乱，因为一切都在一个方法中

乍一看可能很容易，但是实际上只需要花一点点时间来编写可重用的模块化代码。

让我们来看看如何做到这一点。

## 问题

Vue.js 2.x 的 Options API 是一种非常直观的分隔代码的方法

```javascript
export default {
  data() {
    return {
      articles: [],
      searchParameters: [],
    };
  },
  mounted() {
    this.articles = ArticlesAPI.loadArticles();
  },
  methods: {
    searchArticles(id) {
      return this.articles.filter(() => {
        // 一些搜索代码
      });
    },
  },
};
```

问题是，如果一个组件中有数百行代码，那么就必须在多个部分 data、methods、computed 等中为单个特性(例如搜索)添加代码。

这意味着仅一项功能的代码可能会分散分布在数百行中，并分布在几个不同的位置，从而使其难以阅读或调试。

这只是 Vue Composition API RFC 中的一个示例，展示了现在如何按功能组织代码。

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202004/vue-composition-api-and/1.png)

现在，这是使用新的 Composition API 的等效代码。

```javascript
import { ref, onMounted } from "vue";

export default {
  setup() {
    const articles = ref([]);
    const searchParameters = ref([]);

    onMounted(() => {
      this.articles = ArticlesAPI.loadArticles();
    });

    const searchArticles = (id) => {
      return articles.filter(() => {
        // 一些搜索代码
      });
    };

    return {
      articles,
      searchParameters,
      searchArticles,
    };
  },
};
```

现在，为了解决前面关于组织的问题，我们来看看一个提取逻辑的好方法。

## 提取逻辑

我们的最终目标是将每个功能提取到自己的方法中。这样一来，如果我们想调试它，所有的代码都在一个地方。

这非常简单，但是最后我们必须记住，如果我们希望能够在模板中访问数据，则仍然必须使用我们的 `setup` 方法来返回数据。

我们来创建新方法 `useSearchArticles` 并让它返回我们在 `setup` 方法中返回的所有东西。

```javascript
const useSearchArticles = () => {
  const articles = ref([]);
  const searchParameters = ref([]);

  onMounted(() => {
    this.articles = ArticlesAPI.loadArticles();
  });

  const searchArticles = (id) => {
    return articles.filter(() => {
      // 一些搜索代码
    });
  };

  return {
    articles,
    searchParameters,
    searchArticles,
  };
};
```

现在，在我们的 `setup` 方法中，我们可以通过调用我们的方法来访问属性。而且，当然，我们还必须记住从设 `setup` 法中返回它们。

```javascript
export default {
  setup() {
    const {
      articles,
      searchParameters,
      searchArticles,
    } = useSearchArticles();

    return {
      articles,
      searchParameters,
      searchArticles,
    };
  },
};
```

## 在提取的逻辑中访问组件属性

Composition API 中的另一个新变化是 `this` 引用的变化，这一变化意味着我们不能再以相同的方式使用 `prop`、`attributes` 或 `events`。

简而言之，我们将必须使用 `setup` 方法的两个参数来访问 `props`，`attribute`，`slot` 或 `emit` 方法。如果我们只使用 `setup` 方法，一个快速的虚拟组件可能是这样的。

```javascript
export default {
  setup(props, context) {
    onMounted(() => {
      console.log(props);
      context.emit("event", "payload");
    });
  },
};
```

但是现在我们要提取我们的逻辑，我们要把我们的逻辑包装器方法也接受参数。通过这种方式，我们可以从 `setup` 方法传递我们的 `props` 和 `context` 属性，逻辑代码可以访问它们。

```javascript
const checkProps = (props, context) => {
  onMounted(() => {
    console.log(props);
    context.emit("event", "payload");
  });
};
export default {
  setup(props, context) {
    checkProps(props, context);
  },
};
```

## 重用逻辑

最后，如果我们要编写一些逻辑，希望能够在多个组件中使用，则可以将逻辑提取到其自己的文件中，并将其导入到我们的组件中。

然后，我们可以像之前一样调用该方法。假设我们将我们的 `useSearchArticles` 方法移至名为 `use-search-articles-logic.js` 的文件中，如下所示

```javascript
import { ref, onMounted } from "vue";
export function useSearchArticles() {
  const articles = ref([]);
  const searchParameters = ref([]);

  onMounted(() => {
    this.articles = ArticlesAPI.loadArticles();
  });

  const searchArticles = (id) => {
    return articles.filter(() => {
      // 一些搜索代码
    });
  };

  return {
    articles,
    searchParameters,
    searchArticles,
  };
}
```

使用这个新文件，我们的原始组件将看起来像这样

```javascript
import { useSearchArticles } from "./logic/use-search-articles-logic";
export default {
  setup(props) {
    const {
      articles,
      searchParameters,
      searchArticles,
    } = useSearchArticles();

    return {
      articles,
      searchParameters,
      searchArticles,
    };
  },
};
```

## 最后

希望本文能帮助你更好地了解 Composition API 将如何改变我们的编码方式。

但是，与往常一样，项目的组织取决于开发人员设计出色的组件代码并创建可重用逻辑的意愿。

请记住，我们的目标是提高可读性，而在 Vue 中，Composition API 是实现这一点的好方法。

---

原文：https://learnvue.co/2020/03/extract-and-reuse-logic-in-the-vue-composition-api/
