---
title: Promise和Async/await的理解和使用
date: 2018-11-22 22:21:38
categories:
  - 技术
tags:
  - JavaScript
---

![](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/201811/promise-async-await-notepad/banner.jpg)

**以前学习写的笔记，感觉还不错，现在发出来，希望对你有帮助。文章比较长，可以结合目录进行阅读，如果文章对你有所启发和帮助，可以『一键三连』。哦，对了，我已经脱发了...😭😭**

<!-- more -->

---

## 1. 前置知识

### 1.1 区别实例对象与函数对象

实例对象：`new` 函数产生的对象, 称为实例对象, 简称为对象

函数对象： 将函数作为对象使用时, 简称为函数对象

```javascript
function Fn() {}
const fn = new Fn(); // fn为实例对象
Fn.bind({}); // Fn为函数对象
```

### 1.2 两种类型的回调函数

同步回调

- 理解：立即执行, 完全执行完了才结束, 不会放入回调队列中
- 例子: 数组遍历相关的回调函数 / Promise 的 excutor 函数

异步回调

- 理解：不会立即执行, 会放入回调队列中将来执行
- 例子：定时器回调 / ajax 回调 / Promise 的成功|失败的回调

```javascript
const arr = [1, 2, 3];
arr.forEach((item) => console.log(item)); // 同步回调, 不会放入回调队列, 而是立即执行
console.log("forEatch()之后");
setTimeout(() => {
  // 异步回调, 会放入回调队列, 所有同步执行完后才可能执行
  console.log("timout 回调");
}, 0);
console.log("setTimeout 之后");
```

### 1.3 JS 的 error 处理

错误的类型

- Error：所有错误的父类型
- ReferenceError：引用的变量不存在
  ```js
  console.log(a); // ReferenceError: a is not defined
  ```
- TypeError：数据类型不正确的错误
  ```js
  let b = null;
  console.log(b.xxx); // TypeError: Cannot read property 'xxx' of null
  ```
- RangeError：数据值不在其所允许的范围内
  ```js
  function fn() {
    fn();
  }
  fn(); // RangeError: Maximum call stack size exceeded
  ```
- SyntaxError：语法错误

  ```js
  let c = """" // SyntaxError: Unexpected string
  ```

错误处理

- 捕获错误：try ... catch
- 抛出错误：throw error

error 对象的结构

- message 属性：错误相关信息
- stack 属性：函数调用栈记录信息

## 2. Promise 是什么？

### 2.1 理解

抽象表达：Promise 是 JS 中进行异步编程的新的解决方案（旧的是谁？=> 纯回调的形式）

具体表达：

- 从语法上来说：Promise 是一个构造函数
- 从功能上来说：Promise 对象用来封装一个异步操作并可以获取其结果

### 2.2 Promise 的状态改变

Promise 的状态改变只有这 2 种：
![Promise的状态改变](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/201811/promise-async-await-notepad/1.png)

且一个 Promise 对象只能改变一次，无论变成成功还是失败，都会有一个结果数据，成功的结果数据一般称为 `value`，失败的结果数据一般称为 `reason`。

### 2.3 Promise 基本流程

![Promise基本流程](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/201811/promise-async-await-notepad/2.png)

### 2.4 Promise 的基本使用

示例，如果当前时间是偶数就代表成功，否则代表失败

```javascript
// 1. 创建一个新的Promise对象
const p = new Promise((resolve, reject) => {
  // 执行器函数，同步执行
  // 2. 执行异步操作任务
  setTimeout(() => {
    const time = Date.now(); // 如果当前时间是偶数就代表成功，否则代表失败
    // 3.1 如果成功了，调用resolve(value)
    if (time % 2 === 0) {
      resolve("成功的数据，value = " + time);
    } else {
      // 3.2 如果失败了，调用reject(reason)
      reject("失败的数据，reason = " + time);
    }
  }, 1000);
});

p.then(
  (value) => {
    // 接受得到成功的value数据，专业术语：onResolved
    console.log("成功的回调", value);
  },
  (reason) => {
    // 接受得到失败的reason数据，专业术语：onRejected
    console.log("失败的回调", reason);
  }
);
```

## 3. 为什么要用 Promise？

### 3.1 指定回调函数的方式更加灵活

旧的：回调函数必须在启动异步任务前指定

```javascript
// 成功的回调函数
function successCallback(result) {
  console.log("处理成功:" + result);
}

function failureCallback(error) {
  console.log("处理失败:" + error);
}

// 使用纯回调函数
createAudioFileSync(
  audioSettings,
  successCallback,
  failureCallback
);
```

Promise：启动异步任务 => 返回 Promise 对象 => 给 Promise 对象绑定回调函数，甚至可以在异步任务结束后指定多个

```javascript
// 使用 Promise
const promise = createAudioFileSync(audioSettings);
setTimeout(() => {
  promise.then(successCallback, failureCallback);
}, 3000);
```

### 3.2 支持链式调用，可以解决回调地狱问题

什么是回调地狱？回调函数嵌套调用，外部回调函数异步执行的结果是嵌套的回掉执行条件，代码是水平向右扩展

```javascript
// 回调地狱
doSomething(function(result) {
  doSomethingElse(result, function(newResult) {
    doThirdThing(newResult, function(finalResult) {
      console.log('Got the final result: ' + finalResult)
    }, failureCallback)
  }, failureCallback)
},
```

回调地狱的缺点：不便阅读，不便于异常处理

解决方案：Promise 链式调用，代码水平向下扩展

```javascript
doSomething()
  .then(function(result) {
    return doSomethingElse(result);
  })
  .then(function(newResult) {
    return doThirdThing(newResult);
  })
  .then(function(finalResult) {
    console.log("Got the final result: " + finalResult);
  })
  .catch(failureCallback);
```

终极解决方案：**async/await**，用同步的写法处理异步的操作

```javascript
async function request() {
  try {
    const result = await doSomething();
    const newResult = await doSomethingElse(result);
    const finalResult = await doThirdThing(newResult);
    console.log("Got the final result: " + finalResult);
  } catch (error) {
    failureCallback(error);
  }
}
```

## 4. Promise 的 API 说明

### 4.1 API 说明

**Promise 构造函数**。

`Promise (excutor) {}`，excutor 会在 Promise 内部立即同步回调,异步操作在执行器中执行

- excutor 函数：执行器 `(resolve, reject) => {}`
- resolve 函数：内部定义成功时我们调用的函数 `value => {}`
- reject 函数：内部定义失败时我们调用的函数 `reason => {}`

**Promise.prototype.then 方法**

`(onResolved, onRejected) => {}`，指定用于得到成功 value 的成功回调和用于得到失败 reason 的失败回调返回一个新的 promise 对象

- onResolved 函数：成功的回调函数 `(value) => {}`
- onRejected 函数：失败的回调函数 `(reason) => {}`

**Promise.prototype.catch 方法**

`(onRejected) => {}`，onRejected 函数：失败的回调函数 `(reason) => {}`，then() 的语法糖, 相当于： `then(undefined, onRejected)`

**Promise.resolve 方法**

`(value) => {}`，value：成功的数据或 promise 对象，返回一个成功/失败的 promise 对象

**Promise.reject 方法**

`(reason) => {}`，reason：失败的原因，返回一个失败的 promise 对象

**Promise.all 方法**

`(promises) => {}`，promises：包含 n 个 promise 的数组，返回一个新的 promise, 只有所有的 promise 都成功才成功, 只要有一个失败了就直接失败

**Promise.race 方法**

`(promises) => {}`，promises: 包含 n 个 promise 的数组，返回一个新的 promise, 第一个完成的 promise 的结果状态就是最终的结果状态

```javascript
// 产生一个成功值为 1 的 Promise 对象
const p1 = new Promise((resolve, reject) => {
  resolve(1);
});

// 产生一个成功值为 2 的 Promise 对象
const p2 = Promise.resolve(2);
// 产生一个失败值为 3 的 Promise 对象
const p3 = Promise.reject(3);

p1.then((value) => console.log(value));
p2.then((value) => console.log(value));
p3.catch((reason) => console.error(reason));

// const pAll = Promise.all([p1, p2])
const pAll = Promise.all([p1, p2, p3]);
pAll.then(
  (values) => {
    console.log("all onResolved()", values); // all onResolved() [ 1, 2 ]
  },
  (reason) => {
    console.log("all onRejected()", reason); // all onRejected() 3
  }
);

const race = Promise.race([p1, p2, p3]);
race.then(
  (value) => {
    console.log("all onResolved()", value);
  },
  (reason) => {
    console.log("all onRejected()", reason);
  }
);
```

### 4.2 Promise 的几个关键问题

#### 4.2.1 如何改变 Promise 的状态

**resolve(value)**，如果当前是 pendding 就会变为 resolved

**reject(reason)**，如果当前是 pendding 就会变为 rejected

**抛出异常**，如果当前是 pendding 就会变为 rejected

```javascript
const p = new Promise((resolve, reject) => {
  // resolve(1) // Promise 变为 resolved 成功状态
  // reject(2) // Promise 变为 rejected 失败状态
  // Promise 变为 rejected 失败状态，reason为抛出的 error
  throw new Error("我抛出的异常");
  // 变为 rejected 失败状态，reason为抛出的 3
  // throw 3
});

p.then(
  (value) => {},
  (reason) => {
    console.log("reason :", reason);
  }
);
```

#### 4.2.2 当一个 promise 指定多个成功/失败回调函数, 都会调用吗？

当 promise 改变为对应状态时都会调用

```javascript
const p = new Promise((resolve, reject) => {
  // 变为 rejected 失败状态，reason为抛出的 3
  throw 3;
});

p.then(
  (value) => {},
  (reason) => {
    console.log("reason :", reason);
  }
);

p.then(
  (value) => {},
  (reason) => {
    console.log("reason2 :", reason);
  }
);

// 结果：
// reason : 3
// reason2 : 3
```

#### 4.2.3 改变 promise 状态和指定回调函数谁先谁后?

都有可能, 正常情况下是先指定回调再改变状态, 但也可以先改状态再指定回调。

如何先改状态再指定回调?

- 在执行器中直接调用 resolve()/reject()
- 延迟更长时间才调用 then()

什么时候才能得到数据?

- 如果先指定的回调, 那当状态发生改变时, 回调函数就会调用, 得到数据
- 如果先改变的状态, 那当指定回调时, 回调函数就会调用, 得到数据

```javascript
// 常规：先指定回调函数,后改变状态
new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve(1); // 后改变状态（同时指定数据），异步执行回调函数
  }, 1000);
}).then(
  // 先指定回调函数，保存当前指定的回调函数
  (value) => {},
  (reason) => {
    console.log("reason :", reason);
  }
);

// 先改状态,后指定回调函数
new Promise((resolve, reject) => {
  resolve(1); // 先改变状态（同时指定数据）
}).then(
  // 后指定回调函数，异步执行回调函数
  (value) => {
    console.log("value2：", value);
  },
  (reason) => {
    console.log("reason2 :", reason);
  }
);

const p = new Promise((resolve, reject) => {
  resolve(1); // 先改变状态（同时指定数据）
});

setTimeout(() => {
  p.then(
    (value) => {
      console.log("value3：", value);
    },
    (reason) => {
      console.log("reason3 :", reason);
    }
  );
}, 1500);
```

#### 4.2.4 promise.then()返回的新 promise 的结果状态由什么决定?

简单表达：由 then()指定的回调函数执行的结果决定

详细表达：

- 如果抛出异常, 新 promise 变为 rejected, reason 为抛出的异常
- 如果返回的是非 promise 的任意值, 新 promise 变为 resolved, value 为返回的值
- 如果返回的是另一个新 promise, 此 promise 的结果就会成为新 promise 的结果

```javascript
new Promise((resolve, reject) => {
  resolve(1);
})
  .then(
    (value) => {
      console.log("onResolved1()", value); // 1
      // return 1.1 或
      return Promise.resolve(1.1);
      // return Promise.reject(1.1)
      // throw 1.1
    },
    (reason) => {
      console.log("onRejected1()", reason);
    }
  )
  .then(
    (value) => {
      console.log("onResolved2()", value);
    }, // 1.1
    (reason) => {
      console.log("onRejected2()", reason);
    } // 1.1
  );
```

#### 4.2.5 promise 如何串连多个操作任务

promise 的 `then()` 返回一个新的 promise, 可以开成 `then()` 的链式调用，通过 then 的链式调用串连多个同步/异步任务。

#### 4.2.6 promise 异常传透

当使用 promise 的 then 链式调用时, 可以在最后指定失败的回调，前面任何操作出了异常, 都会传到最后失败的回调中处理。

下面的示例代码演示了异常传透

```javascript
new Promise((resolve, reject) => {
  // resolve(1)
  reject(1);
})
  .then((value) => {
    console.log("onResolved1()", value);
    return 2;
  })
  .then((value) => {
    console.log("onResolved2()", value);
    return 3;
  })
  .then((value) => {
    console.log("onResolved3()", value);
  })
  .catch((reason) => {
    console.log("onRejected()", reason); // onRejected() 1
  });
```

代码会执行 `.catch` 中的代码，但实际上代码的执行不是执行到第 3 行就直接跳转到 catch 里面了，而是从第一个 then 调用向下一个个的执行（逐级传递），但是由于我们 then 里面没有处理异常。在 then 里面没写处理异常实际上相当于默认添加了 `reason => { throw reason }` 或者 `reason => Promise.reject(reason)`：

```javascript
new Promise((resolve, reject) => {
  reject(1);
}).then(
  (value) => {
    console.log("onResolved1()", value);
  },
  // reason => { throw reason }
  // 或者
  (reason) => Promise.reject(reason)
);
```

Promise 的异常传透示意图

![Promise的异常传透](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/201811/promise-async-await-notepad/3.png)

#### 4.2.7 中断 promise 链

当使用 promise 的 then 链式调用时, 在中间中断, 不再调用后面的回调函数。

办法: 在回调函数中返回一个 `pendding` 状态的 promise 对象

```javascript
new Promise((resolve, reject) => {
  resolve(1);
})
  .then((value) => {
    console.log("onResolved1()", value);
    return new Promise(() => {}); // 返回一个 pending 的 Promise，中断 promise 链
  })
  .then(
    // 这个 then 不会执行力
    (value) => {
      console.log("onResolved2()", value);
    }
  );
```

## 5. async 与 await

Async/await 实际上只是一种基于 promises 的糖衣语法糖，Async/await 和 promises 一样，都是非堵塞式的，Async/await 让异步代码更具同步代码风格，这也是其优势所在。

- `async function` 用来定义一个返回 `AsyncFunction` 对象的异步函数。异步函数是指通过事件循环异步执行的函数，它会通过一个隐式的 `Promise` 返回其结果，。如果你在代码中使用了异步函数，就会发现它的语法和结构会更像是标准的同步函数。[MDN async_function](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/async_function)
- `await` 操作符用于等待一个`Promise` 对象。它只能在异步函数 `async function` 中使用。[MDN await](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/await)

### 5.1 async 函数

`async` 函数的返回值为 `Promise` 对象，`async` 函数返回的 `Promise` 的结果由函数执行的结果决定

```javascript
async function fn1() {
  return 1;
}

const result = fn1();
console.log(result); // Promise { 1 }
```

在控制台可以看见如下信息

既然是 Promise 对象，那么我们用 then 来调用，并抛出错误，执行 `onRejected()` 且 reason 为错误信息为“我是错误”

```javascript
async function fn1() {
  // return 1
  // return Promise.resolve(1)
  // return Promise.reject(2)
  throw "我是错误";
}

fn1().then(
  (value) => {
    console.log("onResolved()", value);
  },
  (reason) => {
    console.log("onRejected()", reason);
  } // onRejected() 我是错误
);
```

### 5.2 await 表达式

`await` 右侧的表达式一般为 `promise` 对象, 但也可以是其它的值:

- 如果表达式是 `promise` 对象, `await` 返回的是 `promise` 成功的值
- 如果表达式是其它值, 直接将此值作为 `await` 的返回值

```javascript
function fn2() {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve(1000);
    }, 1000);
  });
}

function fn4() {
  return 6;
}

async function fn3() {
  // const value = await fn2() // await 右侧表达式为Promise，得到的结果就是Promise成功的value
  // const value = await '还可以这样'
  const value = await fn4();
  console.log("value", value);
}

fn3(); // value 6
```

`await` 必须写在 `async` 函数中, 但 `async` 函数中可以没有 `await`，如果 `await` 的 `Promise` 失败了, 就会抛出异常, 需要通过 `try...catch` 捕获处理

```javascript
function fn2() {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      // resolve(1000)
      reject(1000);
    }, 1000);
  });
}

async function fn3() {
  try {
    const value = await fn2();
  } catch (error) {
    console.log("得到失败的结果", error);
  }
}

fn3(); // 得到失败的结果 1000
```

### 5.3 Async/await 比 Promise 更优越的表现

**简洁干净**，使用 async/await 能省去写多少行代码

**错误处理**，async/wait 能用相同的结构和好用的经典 try/catch 处理同步和异步错误，错误堆栈能指出包含错误的函数。

**调试**，async/await 的一个极大优势是它更容易调试，使用 async/ await 则无需过多箭头函数，并且能像正常的同步调用一样直接跨过 await 调用。

---
