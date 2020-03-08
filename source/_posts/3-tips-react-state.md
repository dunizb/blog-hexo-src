---
title: 【译】3条简单的React状态管规则
date: 2020-03-08 12:27:04
categories:
- 技术
tags:
- 前端
- React
---

React组件内部的状态是在渲染之间保持不变的封装数据。`useState()`是React钩子，负责管理功能组件内部的状态。

我喜欢`useState()`确实使状态处理变得非常容易。但是我经常遇到类似的问题：
- 我应该将组件的状态划分为小状态，还是保持复合状态？
- 如果状态管理变得复杂，我应该从组件中提取它吗？怎么做？
- 如果`useState()`用法是如此简单，那么何时需要`useReducer()`？

这篇文章介绍了3条简单的规则，可以回答上述问题并帮助您设计组件的状态。
<!-- more -->

## 1.一个关注点

高效状态管理的首要原则是：**让一个状态变量负责一个关注点。**

让一个状态变量负责一个关注点使它符合单一责任原则。让我们来看一个复合状态的例子，即一个包含多个状态值的状态。

```js
const [state, setState] = useState({
  on: true,
  count: 0
});

state.on // => true
state.count // => 0
```

状态由一个普通的JavaScript对象组成，该对象具有属性`on`和`count`。

第一个属性`state.on`包含一个布尔值，表示开关。`state.count`保存一个表示计数器的数字，例如，用户单击按钮的次数。

然后，假设您要将计数器增加1：

```js
// 复合状态更新
setUser({
  ...state,
  count: state.count + 1
});
```

您必须将整个状态保持在附近才能更新计数。这是一个需要调用的大型构造来简单地增加一个计数器：因为一个状态变量负责两个关注点：开关和计数器。

解决方案是将复合状态分成2个原子状态并计数：

```js
const [on, setOnOff] = useState(true);
const [count, setCount] = useState(0);
```

`on`状态变量仅负责存储开关状态。同样的方法，`count`变量仅负责计数器。

现在，让我们尝试更新计数器：

```js
setCount(count + 1);
// 或者使用回调
setCount(count => count + 1);
```

计数状态仅负责计数，易于推理，分别易于更新和读取。

不必担心调用多个`useState()`为每个关注点创建状态变量。

但是请注意，如果您过多使用`useState()`变量，则很有可能您的组件违反了“单一职责原则”。只需将此类组件拆分为较小的组件即可。

## 2.提取复杂的状态逻辑

> 将复杂的状态逻辑提取到自定义钩子中。

将复杂的状态操作保留在组件中是否有意义?

创建React Hook是为了将组件从复杂的状态管理和副作用中隔离出来。因此，由于组件应该只关心要呈现的元素和要附加的一些事件侦听器，所以应该将复杂的状态逻辑提取到自定义Hook中。

让我们考虑一个管理产品列表的组件。用户可以添加新的产品名称。约束是产品名称必须唯一。

第一次尝试是将产品名称列表的设置程序直接保留在组件内部：

```js
function ProductsList() {
  const [names, setNames] = useState([]);
  const [newName, setNewName] = useState('');
  const map = name => <div>{name}</div>;

  const handleChange = event => setNewName(event.target.value);
  const handleAdd = () => {
    const s = new Set([...names, newName]);
    setNames([...s]);
  };

  return (
    <div className="products">
      {names.map(map)}
      <input type="text" onChange={handleChange} />
      <button onClick={handleAdd}>Add</button>
    </div>
  );
}
```

`names`变量保存产品名称，单击Add按钮时将调用`addNewProduct()`事件处理程序。

在`addNewProduct()`中，使用一个`Set`对象来保持产品名称的唯一性。组件应该关注这个实现细节吗？不。

最好将复杂的状态设置器逻辑隔离到自定义Hook中。

新的自定义Hook`useUnique()`负责保持项目的唯一性：

```js
// useUnique.js
export function useUnique(initial) {
  const [items, setItems] = useState(initial);
  const add = newItem => {
    const uniqueItems = [...new Set([...items, newItem])];
    setItems(uniqueItems);
  };
  return [items, add];
};
```

将自定义状态管理提取到一个Hook后，`ProductsList`组件就变得轻松多了：

```js
import { useUnique } from './useUnique';

function ProductsList() {
  const [names, add] = useUnique([]);
  const [newName, setNewName] = useState('');
  const map = name => <div>{name}</div>;

  const handleChange = event => setNewName(e.target.value);
  const handleAdd = () => add(newName);

  return (
    <div className="products">
      {names.map(map)}
      <input type="text" onChange={handleChange} />
      <button onClick={handleAdd}>Add</button>
    </div>
  );
}
```

`const [names, addName] = useUnique([])`是启用自定义Hook的原因。组件不再与复杂的状态管理混杂在一起。

如果您想在列表中添加新名称，则只需调用`add('新产品名称')`。

最重要的是，将复杂的状态管理提取到自定义Hook中的好处是：
- 组件不再需要状态管理细节
- 自定义钩子可以重用
- 可以很容易地在隔离状态下测试自定义Hook

## 3.提取多个状态操作

> 将多个状态操作提取到一个reducer中。

继续使用`ProductsList`的示例，让我们添加一个`Delete`操作，该操作从列表中删除一个产品名称。

现在，您必须编码2个操作：添加和删除产品。处理这些操作，就可以创建一个 reducer 并使组件摆脱状态管理逻辑。

这种方法也符合 hook 的思想：从组件中提取复杂的状态管理。

这是添加和删除产品的 reducer 的可能实现：

```js
function uniqueReducer(state, action) {
  switch (action.type) {
    case 'add':
      return [...new Set([...state, action.name])];
    case 'delete':
      return state.filter(name => name === action.name);
    default:
      throw new Error();
  }
}
```

然后可以通过调用 React 的 `useReducer()` Hook 在产品列表中使用`uniqueReducer()`：

```js
function ProductsList() {
  const [names, dispatch] = useReducer(uniqueReducer, []);
  const [newName, setNewName] = useState('');
  const handleChange = event => setNewName(event.target.value);
  const handleAdd = () => dispatch({ type: 'add', name: newName });

  const map = name => {
    const delete = () => dispatch({ type: 'delete', name });
    return (
      <div>
        {name}
        <button onClick={delete}>Delete</button>
      </div>
    );
  }

  return (
    <div className="products">
      {names.map(map)}
      <input type="text" onChange={handleChange} />
      <button onClick={handleAdd}>Add</button>
    </div>
  );
}
```

`const [names, dispatch] = useReducer(uniqueReducer，[])`启用了uniqueReducer。`names`是保存产品名称的状态变量，`dispatch`是要使用操作对象调用的函数。

单击添加按钮后，处理程序将调用`dispatch({type：'add'，name：newName})`。调度添加操作使减速器`uniqueReducer`向状态添加新产品名称。

同样，单击“删除”按钮时，处理程序将调用`dispatch({type：'delete'，name})`。调度删除操作会将产品名称从名称状态中删除。

## 4.总结

状态变量应该负责一个关注点。

如果状态具有复杂的更新逻辑，则将该逻辑从组件中提取到自定义Hook中。

同样，如果状态需要多个操作，请使用 reducer 合并这些操作。

无论您使用什么规则，状态都应尽可能简单和分离。该组件不应被状态更新的细节所困扰：它们应该是自定义Hook或 reducer 的一部分。

严格遵循这3个简单规则将使您的状态逻辑易于理解、维护和测试。

*****

原文：https://dmitripavlutin.com/react-state-management/
