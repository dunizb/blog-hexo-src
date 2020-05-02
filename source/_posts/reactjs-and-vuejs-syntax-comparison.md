---
title: React.js和Vue.js的语法并列比较
date: 2020-05-02 13:02:05
categories:
  - 技术
tags:
  - 前端
  - Vue.js
  - React
---

React.js和Vue.js都是很好的框架。而且Next.js和Nuxt.js甚至将它们带入了一个新的高度，这有助于我们以更少的配置和更好的可维护性来创建应用程序。但是，如果你必须经常在框架之间切换，在深入探讨另一个框架之后，你可能会轻易忘记另一个框架中的语法。在本文中，我总结了这些框架的基本语法和方案，然后并排列出。我希望这可以帮助我们尽快掌握语法，不过限于篇幅，这篇文章只**比较React.js和Vue.js**，下一篇再谈Next.js个Nuxt.js。

<!-- more -->

![](http://weixin-storage.oss-cn-shanghai.aliyuncs.com/vuevsreact.png)

> Github：https://github.com/oahehc/react-vue-comparison

## Render

React.js

```javascript
ReactDOM.render(<App />, document.getElementById("root"));
```

Vue.js

```javascript
new Vue({
  render: (h) => h(App),
}).$mount("#root");
```

## 基本组件

### React.js

Class component

```javascript
class MyReactComponent extends React.Component {
  render() {
    return <h1>Hello world</h1>;
  }
}
```

Function component

```javascript
function MyReactComponent() {
  return <h1>Hello world</h1>;
}
```

### Vue.js

```html
<template>
  <h1>Hello World</h1>
</template>
<script>
  export default {
    name: "MyVueComponent",
  };
</script>
```

## Prop

React.js

```javascript
function MyReactComponent(props) {
  const { name, mark } = props;
  return <h1>Hello {name}{mark}</h1>;
}
MyReactComponent.propTypes = {
  name: PropTypes.string.isRequired,
  mark: PropTypes.string,
}
MyReactComponent.defaultProps = {
  mark: '!',
}
...
<MyReactComponent name="world">
```

Vue.js

```html
<template>
  <h1>Hello {{ name }}</h1>
</template>
<script>
  export default {
    name: "MyVueComponent",
    props: {
      name: {
        type: String,
        required: true,
      },
      mark: {
        type: String,
        default: "!",
      },
    },
  };
</script>
...
<MyVueComponent name="World" />
```

## 事件绑定

### React.js

Class component

```javascript
class MyReactComponent extends React.Component {
  save = () => {
    console.log("save");
  };
  render() {
    return <button onClick={this.save}>Save</button>;
  }
}
```

Function component

```javascript
function MyReactComponent() {
  const save = () => {
    console.log("save");
  };
  return <button onClick={save}>Save</button>;
}
```

### Vue.js

```html
<template>
  <button @click="save()">Save</button>
</template>
<script>
  export default {
    methods: {
      save() {
        console.log("save");
      },
    },
  };
</script>
```

## 自定义事件

React.js

```javascript
function MyItem({ item, handleDelete }) {
  return <button onClick={() => handleDelete(item)}>{item.name}</button>;
  /*
   * 应用useCallback钩子来防止在每次渲染时生成新的函数。
   *
   * const handleClick = useCallback(() => handleDelete(item), [item, handleDelete]);
   *
   * return <button onClick={handleClick}>{item.name}</button>;
  */
}
...
function App() {
  const handleDelete = () => { ... }
  return <MyItem item={...} handleDelete={handleDelete} />
}
```

Vue.js

```html
<template>
  <button @click="deleteItem()">{{item.name}}</button>
</template>
<script>
  export default {
    name: "my-item",
    props: {
      item: Object,
    },
    methods: {
      deleteItem() {
        this.$emit("delete", this.item);
      },
    },
  };
</script>
...
<template>
  <MyItem :item="item" @delete="handleDelete" />
</template>
<script>
  export default {
    components: {
      MyItem,
    },
    methods: {
      handleDelete(item) { ... }
    },
  };
</script>
```

## State

### React.js

Class component

```javascript
class MyReactComponent extends React.Component {
  state = {
    name: 'world,
  }
  render() {
    return <h1>Hello { this.state.name }</h1>;
  }
}
```

Function component

```javascript
function MyReactComponent() {
  const [name, setName] = useState("world");
  return <h1>Hello {name}</h1>;
}
```

### Vue.js

```html
<template>
  <h1>Hello {{ name }}</h1>
  <!-- 使用组件状态作为prop -->
  <my-vue-component :name="name">
</template>
<script>
  export default {
    data() {
      return { name: "world" };
    },
  };
</script>
```

## Change-State

### React.js

Class component

```javascript
class MyReactComponent extends React.Component {
  state = {
    count: 0,
  };
  increaseCount = () => {
    this.setState({ count: this.state.count + 1 });
    // 在更新之前获取当前状态，以确保我们没有使用陈旧的值
    // this.setState(currentState => ({ count: currentState.count + 1 }));
  };
  render() {
    return (
      <div>
        <span>{this.state.count}</span>
        <button onClick={this.increaseCount}>Add</button>
      </div>
    );
  }
}
```

Function component

```javascript
function MyReactComponent() {
  const [count, setCount] = useState(0);
  const increaseCount = () => {
    setCount(count + 1);
    // setCount(currentCount => currentCount + 1);
  };
  return (
    <div>
      <span>{count}</span>
      <button onClick={increaseCount}>Add</button>
    </div>
  );
}
```

### Vue.js

```html
<template>
  <div>
    <span>{{count}}</span>
    <button @click="increaseCount()">Add</button>
  </div>
</template>
<script>
  export default {
    data() {
      return { count: 0 };
    },
    methods: {
      increaseCount() {
        this.count = this.count + 1;
      },
    },
  };
</script>
```

## 双向绑定 (仅Vue.js)

### React.js

React没有双向绑定，因此我们需要自己处理数据流

```javascript
function MyReactComponent() {
  const [content, setContent] = useState("");
  return (
    <input
      type="text"
      value={content}
      onChange={(e) => setContent(e.target.value)}
    />
  );
}
```

### Vue.js

```html
<template>
  <input type="text" v-model="content" />
</template>
<script>
  export default {
    data() {
      return { content: "" };
    },
  };
</script>
```

## 计算属性

### React.js

React.js没有计算属性，但我们可以通过react hook轻松实现

```javascript
function DisplayName({ firstName, lastName }) {
  const displayName = useMemo(() => {
    return `${firstName} ${lastName}`;
  }, [firstName, lastName]);
  return <div>{displayName}</div>;
}
...
<DisplayName firstName="Hello" lastName="World" />
```

### Vue.js

```html
<template>
  <div>{{displayName}}</div>
</template>
<script>
  export default {
    name: "display-name",
    props: {
      firstName: String,
      lastName: String,
    },
    computed: {
      displayName: function () {
        return `${this.firstName} ${this.lastName}`;
      },
    },
  };
</script>
...
<DisplayName firstName="Hello" lastName="World" />
```

## Watch

### React.js

React.js没有 `watch` 属性，但是我们可以通过react hook轻松实现

```javascript
function MyReactComponent() {
  const [count, setCount] = useState(0);
  const increaseCount = () => {
    setCount((currentCount) => currentCount + 1);
  };
  useEffect(() => {
    localStorage.setItem("my_count", newCount);
  }, [count]);
  return (
    <div>
      <span>{count}</span>
      <button onClick={increaseCount}>Add</button>
    </div>
  );
}
```

### Vue.js

```html
<template>
  <div>
    <span>{{count}}</span>
    <button @click="increaseCount()">Add</button>
  </div>
</template>
<script>
  export default {
    data() {
      return { count: 0 };
    },
    methods: {
      increaseCount() {
        this.count = this.count + 1;
      },
    },
    watch: {
      count: function (newCount, oldCount) {
        localStorage.setItem("my_count", newCount);
      },
    },
  };
</script>
```

## Children-and-Slot

React.js

```javascript
function MyReactComponent({ children }) {
  return <div>{children}</div>;
}
...
<MyReactComponent>Hello World</MyReactComponent>
```

Vue.js

```html
<template>
  <div>
    <slot />
  </div>
</template>
<script>
  export default {
    name: "my-vue-component",
  };
</script>
...
<MyVueComponent>Hello World</MyVueComponent>
```

## 渲染HTML

React.js

```javascript
function MyReactComponent() {
  return <div dangerouslySetInnerHTML={{ __html: "<pre>...</pre>" }} />;
}
```

Vue.js

```html
<template>
  <div v-html="html"></div>
</template>
<script>
  export default {
    data() {
      return {
        html: "<pre>...</pre>",
      };
    },
  };
</script>
```

## 条件渲染

React.js

```javascript
function MyReactComponent() {
  const [isLoading, setLoading] = useState(true);
  return (
    <div>
      {isLoading && <span>Loading...</span>}
      {isLoading ? <div>is loading</div> : <div>is loaded</div>}
    </div>
  );
}
```

Vue.js

```html
<template>
  <div>
    <!--v-show: 总是渲染，但根据条件更改CSS-->
    <span v-show="loading">Loading...</span>
    <div>
      <div v-if="loading">is loading</div>
      <div v-else>is loaded</div>
    </div>
  </div>
</template>
<script>
  export default {
    data() {
      return { loading: true };
    },
  };
</script>
```

## 列表渲染

React.js

```javascript
function MyReactComponent({ items }) {
  return (
    <ul>
      {items.map((item) => (
        <li key={item.id}>
          {item.name}: {item.desc}
        </li>
      ))}
    </ul>
  );
}
```

Vue.js

```html
<template>
  <ul>
    <li v-for="item in items" :key="item.id">
      {{item.name}}: {{item.desc}}
    </li>
  </ul>
</template>
<script>
  export default {
    props: {
      items: Array,
    },
  };
</script>
```

## Render-Props

React.js

```javascript
function Modal({children, isOpen}) {
  const [isModalOpen, toggleModalOpen] = useState(isOpen);
  return (
    <div className={isModalOpen ? 'open' : 'close'}>
      {type children === 'function' ? children(toggleModalOpen) : children}
    </div>)
  ;
}
Modal.propTypes = {
  isOpen: PropTypes.bool,
  children: PropTypes.oneOfType([PropTypes.string, PropTypes.element]).isRequired,
}
Modal.defaultProps = {
  isOpen: false,
}
...
<Modal isOpen>
  {(toggleModalOpen) => {
    <div>
      <div>...</div>
      <button onClick={() => toggleModalOpen(false)}>Cancel</button>
    </div>
  }}
</Modal>
```

Vue.js(slot)

```html
<template>
  <div v-show="isModalOpen">
    <slot v-bind:toggleModal="toggleModalOpen" />
  </div>
</template>
<script>
  export default {
    name: "modal",
    props: {
      isOpen: {
        type: Boolean,
        default: false,
      },
    },
    data() {
      return {
        isModalOpen: this.isOpen,
      };
    },
    methods: {
      toggleModalOpen(state) {
        this.isModalOpen = state;
      },
    },
  };
</script>
...
<Modal isOpen>
  <template v-slot="slotProps">
    <div>...</div>
    <button @click="slotProps.toggleModal(false)">Close</button>
  </template>
</Modal>
```

## 生命周期

### React.js

Class component

```javascript
class MyReactComponent extends React.Component {
  static getDerivedStateFromProps(props, state) {}
  componentDidMount() {}
  shouldComponentUpdate(nextProps, nextState) {}
  getSnapshotBeforeUpdate(prevProps, prevState) {}
  componentDidUpdate(prevProps, prevState) {}
  componentWillUnmount() {}
  render() {
    return <div>Hello World</div>;
  }
}
```

Function component

```javascript
function MyReactComponent() {
  // componentDidMount
  useEffect(() => {}, []);
  // componentDidUpdate + componentDidMount
  useEffect(() => {});
  // componentWillUnmount
  useEffect(() => {
    return () => {...}
  }, []);
  // 在渲染之后但在屏幕更新之前同步运行
  useLayoutEffect(() => {}, []);
  return <div>Hello World</div>;
}
```

### Vue.js

```javascript
<template>
  <div>Hello World</div>
</template>
<script>
  export default {
    beforeCreate() {},
    created() {},
    beforeMount() {},
    mounted() {},
    beforeUpdate() {},
    updated() {},
    beforeDestroy() {},
    destroyed() {},
  };
</script>
```

## 错误处理

React.js

```javascript
class ErrorBoundary extends React.Component {
  state = { hasError: false };
  static getDerivedStateFromError(error) {
    // 更新状态，这样下一个渲染将显示回退UI。
    return { hasError: true };
  }
  componentDidCatch(error, errorInfo) {}
  render() {
    if (this.state.hasError) return <h1>Something went wrong.</h1>;
    return this.props.children;
  }
}
...
<ErrorBoundary>
  <App />
</ErrorBoundary>
```

Vue.js

```javascript
const vm = new Vue({
  data: {
    error: "",
  },
  errorCaptured: function(err, component, details) {
    error = err.toString();
  }
}
```

## Ref

### React.js

Class component

```javascript
class AutofocusInput extends React.Component {
  constructor(props) {
    super(props);
    this.ref = React.createRef();
  }
  state = {
    content: "",
  };
  componentDidMount() {
    this.ref.current.focus();
  }
  setContent = (e) => {
    this.setState({ content: e.target.value });
  };
  render() {
    return (
      <input
        ref={this.ref}
        type="text"
        value={this.state.content}
        onChange={this.setContent}
      />
    );
  }
}
```

Function component

```javascript
function AutofocusInput() {
  const [content, setContent] = useState("");
  const ref = useRef(null);
  useEffect(() => {
    if (ref && ref.current) {
      ref.current.focus();
    }
  }, []);
  return (
    <input
      ref={ref}
      type="text"
      value={content}
      onChange={(e) => setContent(e.target.value)}
    />
  );
}
```

### Vue.js

```html
<template>
  <input ref="input" type="text" v-model="content" />
</template>
<script>
  export default {
    name: "autofocus-input",
    data() {
      return { content: "" };
    },
    mounted() {
      this.$refs.input.focus();
    },
  };
</script>
```

## 性能优化

### React.js

PureComponent

```javascript
class MyReactComponent extends React.PureComponent {
  ...
}
```

shouldComponentUpdate

```javascript
class MyReactComponent extends React.Component {
  shouldComponentUpdate(nextProps) {...}
  ...
}
```

React.memo

```javascript
export default React.memo(
  MyReactComponent,
  (prevProps, nextProps) => {
    ...
  }
);
```

useMemo

```javascript
export default function MyReactComponent() {
  return React.useMemo(() => {
    return <div>...</div>;
  }, []);
}
```

useCallback

```javascript
function MyItem({ item, handleDelete }) {
  const handleClick = useCallback(() => handleDelete(item), [
    item,
    handleDelete,
  ]);
  return <button onClick={handleClick}>{item.name}</button>;
}
```

### Vue.js

v:once

```html
<span v-once>This will never change: {{msg}}</span>
```

函数式组件：我们可以将组件标记为 `functional`，这意味它无状态 (没有[响应式数据](https://cn.vuejs.org/v2/api/#选项-数据))，也没有实例 (没有 `this` 上下文)。

```html
<template functional>
  <h1>Hello {{ name }}</h1>
</template>
<script>
  export default {
    name: "MyVueComponent",
    props: {
      name: String,
    },
  };
</script>
```

keep-alive 组件

```html
<keep-alive>
  <component :is="view"></component>
</keep-alive>
```

完。
