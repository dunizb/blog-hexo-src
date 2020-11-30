---
title: 使用GPU.js改善JavaScript性能
date: 2020-11-30 23:06:06
img: https://weixin-storage.oss-cn-shanghai.aliyuncs.com/202012/performance-with-gpu-js/banner.png
categories:
  - 技术
tags:
  - JavaScript
  - 翻译
  - 开源推荐
---

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202012/performance-with-gpu-js/banner.png)

你是否曾经尝试过运行复杂的计算，却发现它需要花费很长时间，并且拖慢了你的进程？

有很多方法可以解决这个问题，例如使用 web worker 或后台线程。GPU 减轻了 CPU 的处理负荷，给了 CPU 更多的空间来处理其他进程。同时，web worker 仍然运行在 CPU 上，但是运行在不同的线程上。

在该初学者指南中，我们将演示如何使用[GPU.js](https://gpu.rocks/#/)执行复杂的数学计算并提高 JavaScript 应用的性能。

## 什么是 GPU.js？

GPU.js 是一个针对 Web 和 Node.js 构建的 JavaScript 加速库，用于在图形处理单元（GPGPU）上进行通用编程，它使你可以将复杂且耗时的计算移交给 GPU 而不是 CPU，以实现更快的计算和操作。还有一个备用选项：在系统上没有 GPU 的情况下，这些功能仍将在常规 JavaScript 引擎上运行。

当你要执行复杂的计算时，实质上是将这种负担转移给系统的 GPU 而不是 CPU，从而增加了处理速度和时间。

高性能计算是使用 GPU.js 的主要优势之一。如果你想在浏览器中进行并行计算，而不了解 WebGL，那么 GPU.js 是一个适合你的库。

## 为什么要使用 GPU.js

为什么要使用 GPU 执行复杂的计算的原因不胜枚举，有太多的原因无法在一篇文章中探讨。以下是使用 GPU 的一些最值得注意的好处。

- GPU 可用于执行大规模并行 GPGPU 计算。这是需要异步完成的计算类型
- 当系统中没有 GPU 时，它会优雅地退回到 JavaScript
- GPU 当前在浏览器和 Node.js 上运行，非常适合通过大量计算来加速网站
- GPU.js 是在考虑 JavaScript 的情况下构建的，因此这些功能均使用合法的 JavaScript 语法

如果你认为你的处理器可以胜任，你不需要 GPU.js，看看下面这个 GPU 和 CPU 运行计算的结果。

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202012/performance-with-gpu-js/1.png)

如你所见，GPU 比 CPU 快 22.97 倍。

## GPU.js 的工作方式

考虑到这种速度水平，JavaScript 生态系统仿佛得到了一个可以乘坐的火箭。GPU 可以帮助网站更快地加载，特别是必须在首页上执行复杂计算的网站。你不再需要担心使用后台线程和加载器，因为 GPU 运行计算的速度是普通 CPU 的 22.97 倍。

`gpu.createKernel` 方法创建了一个从 JavaScript 函数移植过来的 GPU 加速内核。

与 GPU 并行运行内核函数会导致更快的计算速度——快 1-15 倍，这取决于你的硬件。

## GPU.js 入门

为了展示如何使用 GPU.js 更快地计算复杂的计算，让我们快速启动一个实际的演示。

安装

```shell
sudo apt install mesa-common-dev libxi-dev  // using Linux
```

npm

```shell
npm install gpu.js --save
// OR
yarn add gpu.js
```

在你的 Node 项目中要导入 GPU.js。

```javascript
import { GPU } from ('gpu.js')

// OR
const { GPU } = require('gpu.js')

const gpu = new GPU();
```

### 乘法演示

在下面的示例中，计算是在 GPU 上并行完成的。

首先，生成大量数据。

```javascript
const getArrayValues = () => {

  // 在此处创建2D arrary
  const values = [[], []]

  // 将值插入第一个数组
  for (let y = 0; y < 600; y++){
    values[0].push([])
    values[1].push([])

    // 将值插入第二个数组
    for (let x = 0; x < 600; x++){
      values\[0\][y].push(Math.random())
      values\[1\][y].push(Math.random())
    }
  }

  // 返回填充数组
  return values
}
```

创建内核（运行在 GPU 上的函数的另一个词）。

```javascript
const gpu = new GPU();

// 使用 `createKernel()` 方法将数组相乘
const multiplyLargeValues = gpu
  .createKernel(function(a, b) {
    let sum = 0;
    for (let i = 0; i < 600; i++) {
      sum +=
        aaaaaaaaaaaaaaaa\[this.thread.yyyyyyyyyyyyyyyy\][
          i
        ] *
        bbbbbbbbbbbbbbbb\[iiiiiiiiiiiiiiii\][this.thread.x];
    }
    return sum;
  })
  .setOutput([600, 600]);
```

使用矩阵作为参数调用内核。

```javascript
const largeArray = getArrayValues();
const out = multiplyLargeValues(
  largeArray[0],
  largeArray[1]
);
```

输出

```javascript
console.log(out\[y\][x]) // 将元素记录在数组的第x行和第y列
console.log(out\[10\][12]) // 记录输出数组第10行和第12列的元素
```

## 运行 GPU 基准测试

你可以按照[GitHub](https://github.com/gpujs/benchmark)上指定的步骤运行基准测试。

```shell
npm install @gpujs/benchmark

const benchmark = require('@gpujs/benchmark')

const benchmarks = benchmark.benchmark(options);
```

`options` 对象包含可以传递给基准的各种配置。

前往 GPU.js 官方网站查看完整的计算基准，这将帮助你了解使用 GPU.js 进行复杂计算可以获得多少速度。

## 结束

在本教程中，我们详细探讨了 GPU.js，分析了它的工作原理，并演示了如何进行并行计算。我们还演示了如何在你的 Node.js 应用中设置 GPU.js。

---

原文：[https://blog.zhangbing.site](https://blog.zhangbing.site/2020/11/30/improving-javascript-performance-with-gpu-js/)  
来源：[https://blog.logrocket.com](https://blog.logrocket.com/improving-javascript-performance-with-gpu-js/)
