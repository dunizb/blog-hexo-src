---
title: Rust与Python：为什么Rust可以取代Python
date: 2021-02-23 22:48:13
img: http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202102/rust-vs-python/banner.png
categories:
  - 翻译
tags:
  - Rust
  - Python
summary: "比较 Rust 和 Python，讨论每种情况下的适用用例，回顾使用 Rust 与 Python 的优缺点，并说明为什么 Rust 可能会取代 Python。"
---

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202102/rust-vs-python/banner.png)

> 本文翻译自：[blog.logrocket.com](https://blog.logrocket.com/rust-vs-python-why-rust-could-replace-python/)，作者：David Adeneye Abiodun

**在本指南中，我们将比较 Rust 和 Python 编程语言。我们将讨论每种情况下的适用用例，回顾使用 Rust 与 Python 的优缺点，并说明为什么 Rust 可能会取代 Python。**

<!-- more -->

我将介绍以下内容：

- 什么是 Rust？
- 什么是 Python？
- 何时使用 Rust
- 何时使用 Python
- 为什么 Rust 可以取代 Python

## 什么是 Rust？

[Rust](https://www.rust-lang.org/)是一种多范式语言，使开发人员能够构建可靠且高效的软件。Rust 注重安全和性能，类似于 C 和 C++，速度快，内存效率高，没有垃圾收集。它可以与其他语言集成，也可以在嵌入式系统上运行。

Rust 拥有[优秀的文档](https://doc.rust-lang.org/book/)、友好的编译器和有用的错误信息，以及先进的工具，包括集成的包管理器、构建工具、智能多编辑器支持、自动完成和类型检查、自动格式化等。

Rust 是由 Mozilla Research 的 Graydon Hoare 在 2010 年推出的。虽然与 Python 相比，Rust 是一门年轻的语言，但它的社区却在稳步发展。事实上，在 Stack Overflow 的 "2020 开发者调查 "中，86%的受访者将 Rust 评为 2020 年他们最喜欢的编程语言。

乍一看，Rust 被静态化和强类型化可能看起来很极端。正如你所看到的，从长远来看，这有助于防止意外的代码行为。

## 什么是 Python？

[Python](https://www.python.org/)是一种编程语言，旨在帮助开发人员更高效地工作，更有效地集成系统。和 Rust 一样，Python 也是多范式的，并且被设计成可扩展的。如果速度是最重要的，你可以使用低级别的 API 调用，比如 [CPython](https://github.com/python/cpython)。

Python 的历史可以追溯到 1991 年 Guido van Rossum 推出的 Python，它以代码的可读性、消除分号和大括号而闻名。

除了它的可扩展性，Python 是一种解释型语言，这使得它比大多数编译型语言慢。正如你所预料的那样，Python 的成熟度很高，它有一个庞大的库的生态系统和一个庞大的专业社区。

## 何时使用 Rust

Rust 被应用于系统开发、操作系统、企业系统、微控制器应用、嵌入式系统、文件系统、浏览器组件、虚拟现实的仿真引擎等。

当性能很重要的时候，Rust 是一种常用的语言，因为它能很好地处理大量数据。它可以处理 CPU 密集型的操作，如执行算法，这就是为什么 Rust 比 Python 更适合系统开发的原因。

Rust 保证了内存的安全性，让你可以控制线程行为和线程之间的资源分配方式。这使你能够构建复杂的系统，这使 Rust 比 Python 更有优势。

总而言之，你应在以下情况下使用 Rust：

- 你的项目需要高性能
- 你正在构建复杂的系统
- 你重视内存安全而不是简单性

## 何时使用 Python

Python 可以用于许多应用领域，从 Web 开发，到数据科学和分析，到 AI 和机器学习，再到软件开发。

Python 被广泛用于机器学习，数据科学和 AI，因为它是：

- 简单易写
- 灵活的
- 包含大量面向数据的软件包和库
- 由出色的工具和库生态系统支持

在以下情况下，你应该使用 Python：

- 你需要一种灵活的语言来支持 Web 开发，数据科学和分析以及机器学习和 AI
- 你重视可读性和简单性
- 你需要一种对初学者友好的语言
- 与性能相比，你更喜欢语法简单和开发速度

## 为什么 Rust 可以取代 Python

考虑到 Rust 的迅速普及和广泛的用例，它似乎几乎不可避免地会在不久的将来超越 Python，以下是一些原因。

### 性能

Rust 超越 Python 的一个主要原因是性能。因为 Rust 是直接编译成机器代码的，所以在你的代码和计算机之间没有虚拟机或解释器。

与 Python 相比，另一个关键优势是 Rust 的线程和内存管理。虽然 Rust 不像 Python 那样有垃圾回收功能，但 Rust 中的编译器会强制检查无效的内存引用泄漏和其他危险或不规则行为。

编译语言通常比解释语言要快。但是，使 Rust 处于不同水平的是，它几乎与 C 和 C ++一样快，但是没有开销。

让我们看一个用 Python 编写的 O（log n）程序的示例，并使用迭代方法计算完成任务所需的时间：

```python
import random
import datetime
def binary_searcher(search_key, arr):
  low = 0
  high = len(arr)-1
  while low <= high:
    mid = int(low + (high-low)//2)
    if search_key == arr[mid]:
      return True
    if search_key < arr[mid]:
      high = mid-1
      elif search_key > arr[mid]:
        low = mid+1
return False
```

输出：

```python
> python -m binny.py
It took 8.6μs to search
```

现在，让我们来看一下使用迭代方法用 Rust 编写的定时 O（log n）程序：

```rust
>use rand::thread_rng;
use std::time::Instant;
use floating_duration::TimeFormat;

fn binary_searcher(search_key: i32, vec: &mut Vec<i32>) -> bool {
  let mut low: usize = 0;
  let mut high: usize = vec.len()-1;
  let mut _mid: usize = 0;
  while low <= high {
    _mid = low + (high-low)/2;
    if search_key == vec[_mid] {
      return true;
    }
    if search_key < vec[_mid] {
      high = _mid - 1;
    } else if search_key > vec[_mid] {
      low = _mid + 1;
    }
  }
  return false;
}

fn main() {
  let mut _rng = thread_rng();
  let mut int_vec = Vec::new();
  let max_num = 1000000;

  for num in 1..max_num {
    int_vec.push(num as i32);
  }
  let start = Instant::now();
  let _result = binary_searcher(384723, &mut int_vec);
  println!("It took: {} to search", TimeFormat(start.elapsed()));
}
```

输出

```rust
> cargo run
Finished dev [unoptimized + debuginfo] target(s) in 0.04s
Running target\debug\algo_rusty.exe
It took: 4.6μs to search
```

在没有任何优化技术的情况下，Rust 和 Python 在同一台机器上执行类似的操作分别需要 4.6 微秒和 8.6 微秒。这意味着 Python 花费的时间几乎是 Rust 的两倍。

### 内存管理

Python 和大多数现代编程语言一样，被设计成内存安全的。然而 Rust 在内存安全方面却让 Python 望尘莫及，即使没有垃圾回收。

Rust 采用了一种独特的方式来确保内存安全，其中涉及所有权系统和借用检查器(borrow checker)。Rust 的借用检查器确保引用和指针不会超过它们所指向的数据。

### 错误检查与诊断

Python 和其他语言一样，提供了错误检查和日志机制。但是在让开发者知道出了什么问题的时候，Rust 和 Python 之间有一些对比。

举一个 Python 变量错误的典型例子：

```python
apple = 15
print('The available apples are:', apple)
```

Python 输出

```
Traceback (most recent call last):
    File "binny.py", line 2, in <module>
      print('The available apples are:', aple)
    NameError: name 'aple' is not defined
```

Rust 中的类似示例：

```rust
fn main() {
  let apple = 15;
  println!("The available apples are:", apple);
}
```

Rust 输出

```
println!("The available apples are:", aple);
   ^^^^ help: a local variable with a similar name exists: `apple`
```

在这里，Rust 推荐了可能的变量，这些变量可能是你想输入的。Python 只会抛出错误，而不会给出如何修复的建议。

举个例子：

```rust
fn main() {
  let grass = 13;

  grass += 1;
}
```

此代码引发错误，因为默认情况下 Rust 中的变量是不可变的。除非它具有关键字 `ou’'t`，否则无法更改。

错误：

```
let grass = 13;
      |         -----
      |         |
      |         first assignment to `grass`
      |         help: make this binding mutable: `mut grass`
```

修正错误：

```rust
fn main() {
  let mut _grass: i32 = 13;

  _grass += 1;
}
```

如你所见，现在它不会引发任何错误。除此之外，Rust 不允许不同的数据类型相互操作，除非将它们转换为相同的类型。

因此，维护 Rust 代码库通常很容易。除非指定，否则 Rust 不允许更改。 Python 确实允许这种性质的更改。

与大多数编译语言相比，Rust 因其速度快、内存安全有保证、超强的可靠性、一致性和用户友好性而备受青睐。在编程中，我们已经到了速度开始变得毫不费力的地步。

随着技术的发展，它变得越来越快，试图在更短的时间内做更多的事情，而不需要那么多的权衡。Rust 帮助实现了这一点，同时又不妨碍开发者的工作。当技术试图推动可以实现的边界时，它也会考虑系统的安全性和可靠性，这是 Rust 背后的主要思想。

### 并行运算

除了速度外，Python 在并行计算方面也有局限性。

Python 使用全局解释器锁(GIL)，它鼓励只有一个线程同时执行，以提高单线程的性能。这个过程是一个阻碍，因为它意味着你不能使用多个 CPU 核进行密集计算。

### 社区

如前所述，Stack Overflow 的“ 2020 开发人员调查”中有 86％的受访者将 Rust 称为 2020 年最喜欢的编程语言。

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202102/rust-vs-python/1.jpeg)

同样，“ 2020 HackerRank 开发人员技能报告”的受访者将 Rust 列为他们计划下一步学习的十大编程语言：

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202102/rust-vs-python/2.png)

相比之下，2019 年的调查将 Rust 排在列表的底部，这表明 Rust 开发人员社区正在迅速增长。

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202102/rust-vs-python/3.png)

正如这些数据所示，Rust 正在成为主流开发者社区的一部分。许多大公司都在使用 Rust，一些开发者甚至用它来构建其他编程语言使用的库。著名的 Rust 用户包括 Mozilla、Dropbox、Atlassian、npm 和 Cloudflare 等等。

Amazon Web Service 还对 Lambda，EC2 和 S3 中的性能敏感组件采用了 Rust。在 2019 年，AWS 宣布赞助 Rust 项目，此后为 Rust 提供了 AWS 开发工具包。

公司正越来越多地用更高效的编程语言（如 Rust）取代速度较慢的编程语言。没有其他语言能像 Rust 一样在简单和速度之间做出平衡。

## 总结

Rust 已经发展成为一种常用的编程语言，其采用率也因此而增加。虽然 Python 在机器学习/数据科学社区中占有稳固的地位，但 Rust 很可能在未来被用作 Python 库的更有效后端。

Rust 具有取代 Python 的巨大潜力。在目前的趋势下，作为应用、性能和速度方面的首选编程语言，Rust 不仅仅是一种编程语言，更是一种思维方式。
