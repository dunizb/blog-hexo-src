---
title: 使用RoughViz可视化Vue.js中的草绘图表
date: 2021-01-29 22:39:30
img: https://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202101/roughViz/banner.png
categories:
  - 前端框架
tags:
  - 开源推荐
  - Vue.js
summary: "roughViz 是一个基于 D3.js 和 Rough.js 的 JavaScript 库。该库旨在帮助构建看起来像草图或手绘图的图表，如下例所示"
---

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202101/roughViz/banner.png)

## 介绍

图表是数据的图形表示，用于使数据集更易于阅读，并且易于区分各部分。虽然大多数用户习惯于看到简洁而正式的图表，但一些用户更喜欢看到手绘或素描的图表，这就是 roughViz 的用武之地。

roughViz 是一个基于 D3.js 和 Rough.js 的 JavaScript 库。该库旨在帮助构建看起来像草图或手绘图的图表，如下例所示。

<!-- more -->

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202101/roughViz/1.gif)

在本指南中，你将学习如何使用 vue-roughviz 在 Vue.js 应用程序中显示类似草图的图表，以及如何使用 vue-cli 配置 Vue 应用程序。

## 先决条件

本教程假定满足以下先决条件：

- 对 Vue.js 的基本了解
- Node.js 的本地开发环境，以及对 Node 软件包管理器（npm）的熟悉
- 文本编辑器，例如 Visual Studio Code 或 Atom

## 开始

如果尚未安装 vue-cli，请运行以下命令以安装最新版本。

```shell
npm install -g @vue/cli
# OR
yarn global add @vue/cli
```

现在，创建一个新的 vue 应用程序：

```shell
vue create my-app
```

**注意**：此过程可能需要几分钟。完成后，我们可以进入新的应用程序根目录：

```shell
cd my-app
```

上面详细描述的过程创建了一个新的 Vue.js 应用程序。为了确保一切都设置好了，运行 `npm run serve`。当你访问http://localhost:8080时，你应该会在浏览器中看到“Welcome to Your Vue.js app page”。

### 添加 vue-roughviz

`vue-roughviz` 是 RoughViz.js 的 Vue.js 包装器。这使得该库可以作为组件进行访问，从而可以在基于 Vue.js 的项目中实现无缝重用。

要将 `vue-roughviz` 包含在我们的项目中，请运行：

```shell
npm install vue-roughviz
```

### vue-roughViz 组件

vue-roughviz 提供了所有 rawViz 图表样式的组件，其中包括：

- `roughBar`——rawViz 条形图组件
- `roughBarH`——roughViz 水平条形图组件
- `roughDonut`——roughViz 甜甜圈图组件
- `roughPie`——roughViz 饼图
- `roughLine`——roughViz 折线图组件
- `roughScatter`——roughViz 分散图表组件
- `roughStackedBar`——roughViz 堆叠条形图组件

### 使用

将 `vue-roughviz` 添加到项目后，下一步是在首选的文本编辑器中打开项目文件夹。

当你打开 `src/App.vue` 文件时，初始内容应类似于下图：

![src/App.vue file](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202101/roughViz/2.png)

如果你的视图如上所述，请继续并删除其所有内容，并替换为以下代码：

```vue
<template>
  <div id="app">
    <rough-bar
      :data="{
        labels: ['North', 'South', 'East', 'West'],
        values: [10, 5, 8, 3],
      }"
      title="Regions"
      roughness="8"
      :colors="['red', 'orange', 'blue', 'skyblue']"
      stroke="black"
      stroke-width="3"
      fill-style="cross-hatch"
      fill-weight="3.5"
      class="d-inline"
    />
  </div>
</template>

<script>
  import { RoughBar } from "vue-roughviz";
  export default {
    name: "App",
    components: {
      RoughBar,
    },
  };
</script>
```

代码说明

- `import ...`——这行代码是从我们先前安装的 vue-roughviz 导入 rawBar 组件。
- `export default {}` ——此块是为了使以前导入的组件（roughBar）在我们的应用中可用。
- `<rough-bar :data="[...]" />` ——这是我们调用外部 rawBar 组件的地方，这些组件中指定的属性是必需的 prop。

### vue-roughviz props

唯一需要的 prop 是 `data`，它是用来构造图表的数据，这可以是字符串或对象。

如果选择一个对象，则该对象必须包含 `labels` 和 `values` 键。如果改用字符串，则字符串必须是 csv 或 tsv 文件的 URL。在这个文件中，还必须将 `labels` 和 `values` 指定为表示每个列的单独属性。

其他有用的 prop 包括：

- `title`——指定图表标题
- `roughness`——图表的粗细度等级
- `stroke`——bar stroke 的颜色
- `stroke-width`
- `fill-weight`——指定内部路径颜色的粗细。
- `fill-style`——条形填充样式，可以是以下一种：
  - `dashed`
  - `solid`
  - `zigzag-line`
  - `cross-hatch`
  - `hachure`
  - `zigzag`

### 运行

要预览我们的应用，运行 `npm run serve`。如果你正确地遵循了上述步骤，访问http://localhost:8080应该允许你查看浏览器中显示的图表。

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202101/roughViz/3.gif)

## 从外部 API 加载数据

让我们做一个小实验，在我们的图表中显示过去 10 天比特币的价格历史。在这个实验中，我们将使用 Coingecko API。

**为什么选择 Coingecko？**与其他加密货币 API 不同，Coingecko 是免费的，不需要 API 密钥就可以开始，这是我们实验的理想选择。

继续，用下面的代码替换 `src/App.vue`

```vue
<template>
  <div id="app">
    <div class="d-inline-block">
      <rough-bar
        v-if="chartValue.length > 0"
        :data="{
          labels: chartLabel,
          values: chartValue,
        }"
        title="BTC - 10 Days"
        roughness="3"
        color="#ccc"
        stroke="black"
        stroke-width="1"
        fill-style="zig-zag"
        fill-weight="2"
      />
    </div>
  </div>
</template>

<script>
  import { RoughBar } from "vue-roughviz";
  export default {
    name: "App",
    components: {
      RoughBar,
    },
    data() {
      return {
        chartLabel: [],
        chartValue: [],
      };
    },
    methods: {
      async loadData() {
        await fetch(
          "https://api.coingecko.com/api/v3/coins/bitcoin/market_chart?vs_currency=usd&days=10&interval=daily"
        )
          .then((res) => res.json())
          .then((rawData) => {
            console.table(rawData.prices);
            rawData.prices.map((data) => {
              let date = new Date(data[0]).toDateString();
              let rPrice = data[1];
              console.log(
                `Price of 1btc on ${date} is ${rPrice}`
              );
              this.chartLabel.push(date);
              this.chartValue.push(rPrice);
            });
          })
          .catch((err) =>
            console.error("Fetch error -> ", err)
          );
      },
    },
    beforeMount() {
      this.loadData();
    },
  };
</script>
```

我们创建了一个异步方法 `loadData()`，它从 coingecko API 获取比特币价格历史记录，并循环遍历返回的数据。我们将日期与价格分开，使用返回的日期作为图表标签，价格作为图表值。而 `beforeMount()` 也就是在我们的应用被挂载到视图之前，我们调用了前面创建的 `loadData()` 函数。

运行我们的应用程序应该，你应该看到我们的图表的新变化如下:

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202101/roughViz/4.gif)

## 结束

本文解释了图表及其用途，介绍了使用 Vue-CLI 创建 Vue 应用程序的过程，并展示了如何使用 `vue-roughviz` 在 Vue.js 应用程序中显示类似草图的图表。

## 在线预览及源码

请戳这个链接：[https://coding.zhangbing.site/view.html?url=./list/vue-roughviz.html](https://coding.zhangbing.site/view.html?url=./list/vue-roughviz.html)

---

原文地址：[https://blog.zhangbing.site/2021/01/29/visualizing-sketched-charts-in-vue-js-with-roughviz/](https://blog.zhangbing.site/2021/01/29/visualizing-sketched-charts-in-vue-js-with-roughviz/)
