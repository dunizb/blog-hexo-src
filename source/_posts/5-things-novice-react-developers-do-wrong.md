---
title: 新手React开发人员做错的5件事
date: 2020-05-03 23:36:28
categories:
  - 技术
tags:
  - 前端
  - React
---

请勿执行的操作以及如何解决的方法，这部分内容是针对React的新手开发人员提供的。
<!-- more -->
![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/react-context-api-4929b3703a1a7082d99b53eb1bbfc31f.png)

## 1.忘记大写React组件

考虑一下这段代码，它创建一个简单的div，其中包含父组件的标题。里面有一个子组件，其中包含带有一些文本的div。

```react
class childComponent extends React.Component {
  render() {
    return (
      <div className='childDiv'>
        <p>Child Component</p>
      </div>
    );
  }
}

class ParentComponent extends React.Component {
  render() {
    return (
      <div className='parentDiv'>
        <h1 className='parentHeader'>Parent Component</h1>
        <childComponent />
      </div>
    );
  }
}

export default ParentComponent;
```

您认为代码运行时会出现什么？

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202004/5-Things-react-error/1.jpeg)

`childComponent` 未渲染。它去哪儿了？代码编译成功，终端也没有错误。

再次查看子组件的代码。注意组件的名称，你注意到什么不同了吗?

在浏览器中打开控制台，浏览器控制台警告的大小写不正确

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202004/5-Things-react-error/2.jpeg)

事实证明，React将小写组件视为DOM标记。如果你是React的新手，你可能已经错过了React文档中的这个小细节。

如果不了解这一点，初学者常常会陷入这样的困惑：即他们的代码编译没有任何错误，到底哪里出了问题?

解决方法很简单，大写您的组件。

## 2.错误地调用收到的props

要访问由父组件传入的prop，子组件必须确保它们调用了正确的prop名称。

还可以使用另一个变量名将Props传递给子组件。考虑以下代码片段：

```javascript
class ChildComponent extends React.Component {
  render() {
    const { randomString } = this.props;

    return (
      <div className='childDiv'>
        <p>{randomString}</p>
      </div>
    );
  }
}

class ParentComponent extends React.Component {
  render() {
    const randomString = 'lorem ipsum';
    
    return (
      <div className='parentDiv'>
        <h1 className='parentHeader'>Parent Component</h1>
        <ChildComponent mainText={randomString} />
      </div>
    );
  }
}
```

尽管此代码可以编译并运行无误，但 `ChildComponent` 内不会渲染任何文本。

```html
<ChildComponent mainText={randomString} />
```

仔细看看这一行代码，在 `ParentComponent` 中声明的变量 `randomString` 作为名为 `mainText` 的prop传递给 `ChildComponent`。

然而，`ChildComponent` 试图从它收到的prop中访问 `randomString`。由于它仅接收 `mainText` 作为prop，因此将导致未定义的值分配给在 `ChildComponent` 中声明的 `randomString`。结果，其 `<p>`标记内未呈现任何内容。

注意哪些prop被传递到您的组件中，并相应地访问它们。这将在调试期间为您节省一些不必要的麻烦。

## 3.传递不正确的Props类型

如果所接收的prop不是预期的类型，那么依赖于这些接收prop的组件可能会有不同的行为。

```javascript
class ChildComponent extends React.Component {
  render() {
    const { showIntro, showBody } = this.props;

    return (
      <div className='childDiv'>
        {showIntro && <p>Hello!</p>}
        {showBody && <p>Spot the mistake!</p>}
      </div>
    );
  }
}
```

考虑这个有两个prop的 `ChildComponent`：`showIntro` 和 `showBody`。它显示“你好！和“发现错误！”只有当`showIntro` 和 `showBody` 分别设置为 `true` 时才会这样。

`ChildComponent` 希望将两个布尔值作为prop传递。如果在父组件中执行类似的操作，会发生什么情况？

```html
<ChildComponent showIntro='false' showBody='false' />
<ChildComponent showIntro={'false'} showBody={'false'} />
<ChildComponent showIntro={false} showBody={false} />
```

在prop中使用了不同的引号和大括号。但是，它们的行为将不同。看看这个：

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202004/5-Things-react-error/3.jpeg)

前两个 `ChildComponent` 都渲染了两个 `<p>` 标记，而最后一个 `ChildComponent` 没有渲染。

作为prop传递的 `'false'` 和 `{'false'}` 会导致无意中为 `showIntro` 和 `showBody` 分配了一个值为 `false` 的字符串，而不是布尔值 `false`。

对于前两个 `ChildComponent`，将 `showIntro` 和 `showBody` 都计算为 `true`。

这是由于 `&&` 运算符的隐式强制类型转换。当 `&&` 运算符检查 `showIntro` 或 `showBody`（均为字符串）时，两个字符串都将强制为 `true`。

最后一个 `ChildComponent` 接收到布尔值 `false`，因此它没有正确渲染任何内容。

```javascript
console.log(`showIntro type: ${typeof showIntro}`);
console.log(`showIntro evaluated to: ${showIntro && true}`);
console.log(`showBody type: ${typeof showBody}`);
console.log(`showBody evaluated to: ${showBody && true}`);
```

为了确认这一点，我们运行 `console.log()` 来检查每个 `ChildComponent` 中prop的运行结果。

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202004/5-Things-react-error/4.png)

正如这里所演示的，初学者在将prop传递给其他组件时能够区分使用引号和花括号之间的区别是非常重要的。

您可以使用引号来传递字符串文字。

```javascript
<MyComponent data='Hello World!'/> // passing in a String
```

花括号用于传递JavaScript表达式。

```javascript
<MyComponent data={2468} /> // passing in a Number
<MyComponent data={true} /> // passing in a Boolean
```

以下是Reac文档中的一些注意事项：

> 将JavaScript表达式嵌入属性中时，请勿在大括号周围加上引号。您应该使用引号（用于字符串值）或大括号（用于表达式），但不要在同一属性中都使用引号。

## 4.在render()内部调用setState()

下图无限循环错误消息

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202004/5-Things-react-error/5.jpeg)

尽管您的组件中没有 `componentWillUpdate()` 或 `componentWillUpdate()`，您仍可能遇到此错误。当您在 `render()` 函数中调用 `setState()` 时也会发生此错误。

为什么会这样？每次调用 `setState()` 时，React将通过调用 `render()` 重新渲染。您的 `render()` 函数内部是什么？ `setState()`。你看到结果了吗？一个无限循环。

只需将 `setState()` 调用移到 `render()` 函数之外即可。

如果在组件挂载后必须初始化状态（也许是从API端点提取数据），请在 `componentDidMoun()` 中进行。

如果可以在组件挂载之前初始化状态，也可以使用构造函数来完成。

## 5.setState()的异步性

在调试时，通常使用 `console.log()` 打印值。但是，当代码异步运行时，这不能很好地工作。

```javascript
handleCounterIncrement = () => {
  const { counter } = this.state;
  console.log(`Before update: ${counter}`);
  this.setState({ counter: counter + 1 });
  console.log(`After update: ${counter}`);
};
```

你以前试过这样做吗？坏消息——`setState()` 调用是异步的。不能保证给定的代码将按顺序执行。它可能导致如下输出：

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202004/5-Things-react-error/6.jpeg)

在执行 `setState()` 之前执行了两个 `console.log()` 调用。因此，它两次打印前一个状态的值。

如果希望在调用 `setState()` 之前和之后检查状态的值，请在 `setState()` 中将回调作为第二个参数传递。

```javascript
handleCounterIncrement = () => {
  const { counter } = this.state;
  console.log(`before update: ${counter}`);
  this.setState({ counter: counter + 1 }, () => {
  	console.log(`after update: ${this.state.counter}`);
  });
};
```

回调将在 `setState()` 完成后执行，从而为 `console.log()` 提供同步行为。

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/202004/5-Things-react-error/7.jpeg)


****

原文：https://medium.com/better-programming/5-things-novice-react-developers-do-wrong-9d97bd6dae95

作者：[Jeremy Aw](https://medium.com/@jeremyinelysium?source=post_page-----9d97bd6dae95----------------------)
