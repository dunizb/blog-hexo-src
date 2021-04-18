---
title: 用值填充JavaScript数组的几种方法
date: 2020-04-17 15:19:59
categories:
  - JavaScript
---

在本文中，我们将研究如何用我们选择的内容填充新数组。

<!-- more -->

## Array.prototype.fill()

我们可以使用数组实例的 `fill` 方法为现有数组填充值。

它具有以下签名：

```javascript
Array.prototype.fill(
  value,
  (start = 0),
  (end = this.length)
);
```

`fill` 方法具有以下参数：

- `value` ——用来填充数组的值。
- `start`——可选参数，用于指示要填充数组的起始索引。默认是 0
- `end`——可选参数，结束索引，默认值为数组实例的长度。结束索引本身不包括在内

它返回一个修改后的数组，其中填充了值。

例如，我们可以按以下方式使用它：

```javascript
const arr = [1, 2, 3].fill(6, 1, 3);
```

然后 `arr` 是 `[1、6、6]`，因为我们指定要填充的值 6 是从索引 1 开始直到 2。

`fill` 始终填充到最终索引减 1。

如果我们跳过可选的参数：

```javascript
const arr = [1, 2, 3].fill(6);
```

然后我们得到 `[6，6，6]`，因为我们省略了可选参数，所以用 6 覆盖了所有项。

## 填充升序数字

通过将点扩展符与数组实例的 `keys` 方法结合使用，我们可以从 0 开始以升序数填充数组。

例如，我们可以编写以下代码：

```javascript
const arr = [...new Array(5).keys()];
```

那么 `arr` 的值是 `[0,1,2,3,4]`，因为我们创建了一个有 5 个条目的新数组，调用它的 `keys`，然后将其传播到一个新的数组中。

## 使用计算值填充

要用计算值填充数组，我们可以使用 `Array.from` 方法，然后将回调传递给第二个参数，以将值映射到我们在每个条目中想要的内容。

例如，如果要创建一个包含 5 个奇数作为其内容的数组，则可以编写以下内容：

```javascript
const arr = Array.from(new Array(5), (x, i) => i * 2 + 1);
```

然后 `arr` 的值为 `[1、3、5、7、9]`，因为我们通过在第一个参数中调用 `Array` 构造函数创建了一个新数组。然后在第二个参数中，我们传入一个函数来映射我们在第一个参数中创建的数组的索引 `i`，并返回 `i*2 + 1`。

因此，我们在数组中得到 5 个奇数。

## 用 undefined 填充

要填充 `undefined`，我们只需使用一个参数（其值为 0 或更大的整数）调用 `Array` 构造函数即可。

然后，我们将新构造的数组扩展到一个新数组中，将数组构造函数调用中创建的空值转换为 `undefined`。

例如，我们可以这样写：

```javascript
const arr = [...new Array(3)];
```

`arr` 有值 `[undefined, undefined, undefined]`，因为我们扩展了值。

## 使用 String 的 repeat() 方法

我们可以调用 `repeat` 重复一个字符串，然后调用 `split` 将字符串拆分为数组条目。

例如，如果要用 `'foo'` 填充长度为 5 的数组，则可以编写：

```javascript
const arr = "foo|"
  .repeat(5)
  .split("|")
  .filter((f) => !!f);
```

在上面的代码中，我们使用了 `|` 符号作为定界符，我们在调用 `repeat` 来重复 `'foo |'` 之后使用它来调用`split` 。

然后我们调用 `filter` 来删除 `split` 返回的数组末尾的空字符串值。

因此，`arr` 的值是 `[" foo "， " foo "， " foo "， " foo "， " foo "， " foo "]`。

## 总结

有几种方法可以用值填充数组。

我们可以使用 `array. from` 方法来创建一个新的数组。通过传入映射（map）函数，可以将这些值映射到我们想要的内容。

另外，`Array` 有一个 `fill` 静态方法来用值填充给定的数组。

`Array` 构造函数与扩展运算符组合也可以用于用值填充数组。

最后，我们可以在字符串上调用 `repeat`来重复它，然后调用 `split` 以拆分为数组项。

当我们调用 `repeat` 时，我们可能不得不调用 `filter` 来删除不需要的值。

---

原文：https://levelup.gitconnected.com/several-ways-to-fill-a-javascript-array-with-values-91f52f09669

作者：[John Au-Yeung](https://levelup.gitconnected.com/@hohanga?source=follow_footer--------------------------follow_footer-)
