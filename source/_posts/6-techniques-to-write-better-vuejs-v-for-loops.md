---
title: 在Vue.js编写更好的v-for循环的6种技巧
date: 2020-04-12 14:09:46
categories:
  - 技术
tags:
  - 前端
  - Vue.js
---

# 编写更好的VueJS v-for循环的6种技巧

在VueJS中，v-for循环是每个项目都会使用的东西，它允许您在模板代码中编写for循环。

在最基本的用法中，它们的用法如下。

```vue
<ul>
  <li v-for='product in products'>
    {{ product.name }}
  </li>
</ul>
```

但是，在本文中，我将介绍六种方法来使你的 `v-for` 代码更加精确，可预测和强大。

让我们开始吧。

## 1.始终在v-for循环中使用key

首先，我们将讨论大多数Vue开发人员已经知道的常见最佳做法——在 `v-for` 循环中使用 `:key`。通过设置一个惟一的键属性，它可以确保组件以您期望的方式工作。

果我们不使用key，Vue将尝试使DOM尽可能高效，这可能意味着 `v-for` 元素可能会出现乱序或其他不可预测的行为。如果我们对每个元素都有唯一的键引用，那么我们就可以更好地准确地预测DOM将如何操作。

```vue
<ul>
  <li 
    v-for='product in products'
    :key='product._id'  
  >
    {{ product.name }}
  </li>
</ul>
```

## 2.在一个范围内循环

尽管大多数情况下，`v-for` 用于遍历数组或对象，但在某些情况下，我们肯定只希望循环执行一定次数。

例如，假设我们正在为在线商店创建一个分页系统，而我们只希望每页显示10个产品。使用一个变量来跟踪当前的页码，我们可以像这样处理分页。

```vue
<ul>
  <li v-for='index in 10' :key='index'>
    {{ products[page * 10 + index] }}
  </li>
</ul>
```

## 3.不要在循环中使用v-if

一个超级常见的错误是使用 `v-if` 来过滤 `v-for` 循环的数据。尽管这看起来很直观，但它会导致一个巨大的性能问题——VueJS优先考虑 `v-for` 而不是 `v-if` 指令。

这意味着您的组件将循环遍历每个元素，然后检查 `v-if` 条件以确定是否应渲染。因此，实际上，无论条件是什么，您都将遍历数组的每个项目。

不要这样：

```vue
// BAD CODE!
<ul>
  <li 
    v-for='product in products' 
    :key='product._id' 
    v-if='product.price < 50'
  >
    {{ product.name }}
  </li>
</ul>
```

## 4.使用计算属性或方法代替

为避免上述问题，我们应该在遍历模板中的数据之前对其进行过滤。有两种非常相似的方法：

- 使用计算属性
- 使用过滤方法

让我们快速地介绍一下这两种方法。

首先，我们只需要设置一个计算属性，为了获得与之前的v-if相同的功能，代码应如下所示。

```vue
<ul>
  <li v-for='products in productsUnderFifty' :key='product._id' >
    {{ product.name }}
  </li>
</ul>

// ...
<script>
  export default {
    data () {
      return {
        products: []
      }
    },
    computed: {
      productsUnderFifty: function () {
        return this.products.filter(product => product.price < 50)
      }
    }
  }
</script>
```

下面的代码几乎相同，但是使用方法改变了我们访问模板中的值的方式，如果我们希望能够将变量传递给筛选器，那么方法是最好的选择。

```vue
<ul>
  <li v-for='products in productsUnderPrice(50)' :key='product._id' >
    {{ product.name }}
  </li>
</ul>

// ...

<script>
  export default {
    data () {
      return {
        products: []
      }
    },
    methods: {
      productsUnderPrice (price) {
        return this.products.filter(product => product.price < price)
      }
    }
  }
</script>
```

## 5.在循环中访问项目的索引

除了遍历数组和访问每个元素之外，我们还可以跟踪每个项的索引。

为此，我们必须在项目后添加一个索引值，它非常简单，可用于分页，显示列表索引，显示排名等。

```vue
<ul>
  <li v-for='(products, index) in products' :key='product._id' >
    Product #{{ index }}: {{ product.name }}
  </li>
</ul>
```

## 6.遍历一个对象

到目前为止，我们只真正看过使用 `v-for` 遍历数组，但是我们可以轻松地遍历对象的键值对。

与访问元素的索引类似，我们必须向循环中添加另一个值。如果我们用一个参数遍历一个对象，我们将遍历所有的项。

如果我们添加另一个参数，我们将获得items 和 key，如果添加第三个，我们还可以访问 `v-for` 循环的索引。

假设我们要遍历产品中的每个媒体资源。

```vue
<ul>
  <li v-for='(products, index) in products' :key='product._id' >
    <span v-for='(item, key, index) in product' :key='key'>
      {{ item }}
    </span>
  </li>
</ul>
```

## 总结

希望这篇简短的文章能教您一些有关使用Vue的 `v-for` 指令的最佳做法。

你还有什么其他建议吗？

****

原文：https://learnvue.co/2020/02/6-techniques-to-write-better-vuejs-v-for-loops/?utm_source=rss&utm_medium=rss&utm_campaign=6-techniques-to-write-better-vuejs-v-for-loops

翻译：《前端外文精选》微信公众号
