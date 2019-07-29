---
title: Vue.js新手教学|如何写一个Checklist组件
date: 2017-11-18 23:06:45
categories:
- 前端开发
- 前端框架
tags:
- Vue.js
- 实战
description: "本文教你如何写一个移动端的 Checklist 组件，使用 vue 单文件形式开发，适合 Vue.js 新手。同时此文非常长，最好跟着文章步骤边看边写。本文说些什么，或者你能收获什么？。"
---
![](https://raw.githubusercontent.com/dunizb/cloudimg/master/blog/article/201711/vue-checklist/banner.jpg)

**建议在电脑上阅读此文，全部源代码在文章最后**

**2017.11.30更新：本案例有了更优雅更简单的实现方案了，具体请看文末源码里的checklist2.0.vue文件，可以比较看一下两种实现方式**

本文教你如何写一个移动端的 Checklist 组件，使用 vue 单文件形式开发，适合 Vue.js 新手。同时此文非常长，最好跟着文章步骤边看边写。本文说些什么，或者你能收获什么？。
1. 一步步从0开始有节奏的编写 Checklist 组件
2. 在编码过程中进行功能分析
3. 涉及一些移动端适配的问题
4. Vue.js组件方面的一些知识

## 前置知识
阅读此文前您最好有以下知识的基础：
1. 对 Vue.js 的`.vue`单文件和 Vue.js 组件知识有基本的认识
2. CSS 的 Flexbox 布局知识。

## 什么是Checklist
什么是Checklist组件？我们先来看一下市面上已经有的UI框架的Checklist长什么样

|weui      |Mint UI|
|:---------|:---------|
|![weui](https://raw.githubusercontent.com/dunizb/cloudimg/master/blog/article/201711/vue-checklist/weui.gif)|![Mint-UI](https://raw.githubusercontent.com/dunizb/cloudimg/master/blog/article/201711/vue-checklist/mint-ui.gif)|

而这种组件的一个典型场景是移动端的购物车列表，打开你的京东、淘宝购物车看看，功能是不是很像呢？

本文写一个什么样的 Checklist 组件？这个组件来自于我司真实项目，刚开始我也是用的 Mint-UI 来做，后来业务升级需求变更 Mint-UI 就不适合了，于是我就自己撸了一个，特整理出来此文，并且我们尽量把他做的通用、灵活一点。我们的 Checklist 如下：

|我们的      |京东购物车列表|
|:---------|:---------|
|![我们的](https://raw.githubusercontent.com/dunizb/cloudimg/master/blog/article/201711/vue-checklist/checklist.gif)|<div align="center"><img width="320" src="https://raw.githubusercontent.com/dunizb/cloudimg/master/blog/article/201711/vue-checklist/jdcart.png"/></div>|

## 第零步：分析与准备
### 0.1 业务需求和功能分析
在动手撸代码之前，我们先来仔细分析一下业务需求和功能点，这个组件是展示考场和考场地址，考场地址没有就不显示，最多选三个，选了三个后其他的要禁用等，数据是根据考试科目和所在城市动态获取，当列表数据很多我们还得给它一个最大高度让列表可滚动等，从图中得出以下功能点：
1. 显示隐藏和过渡动画
2. 列表的地址一行可有可无，行高自适应
3. 选中状态和禁用状态
4. 选择了几个和最多选几个的提示文字（我们叫他**操作提示栏**吧，为了方便后面都这么叫），为了通用性，最多选几个应该做成可配置选项
5. 列表可滚动以及列表最大高度，最大高度也可以做成可配置项
6. 头部bar，标题、取消、确定应该也是可有可无，可做成可配置项
7. 选中的项的值应该是一个数组
8. 选中后点击完成按钮应该把选中的值发送给父组件
9. checkbox选框可以在左边也可以在右边

等等....

### 0.2 规划API
一个Vue组件的 API 只来自 props、events 和 slots，确定好这 3 部分的命名、规则，剩下的逻辑即使第一版没做好，后续也可以迭代完善。但是 API 如果没有设计好，后续再改对使用者成本就很大了。

根据以上功能分析可以初步得如下出一些 API，我们可以先把各个 API 先写到组件中（此部分内容属于新增，本文并没有先把 API 写到组件中，Props 的`maxHeight` 本文也没有实现，你们可以自己考虑实现以下）

**Props**

|属性名|      说明      |  类型 |是否必须|默认值|
|:------|:--------------|:------|:------|:-----|
|max  |最多选择几项|Number  |是    |0  |
|dataList |数据        |Array |是|[]  |
|maxHeight|控件最大高度|Number|否|300(px)|
|checkboxLeft |选框是否在左边 |Boolean |否|false  |

**Events**

|事件名 |      说明      |  参数/返回值          |
|:------|:--------------|:--------------|
|on-change |点击确定之后触发的事件|Object  |

**Methods**

|方法名 |      说明    |
|:------|:--------------|
|show |显示组件|
|hide |隐藏组件|

### 0.3 初始化项目
再来分析一下 DOM 结构该怎么划分，这样有利于编码时的大局观  
<div align="center"><img width="320" src="https://raw.githubusercontent.com/dunizb/cloudimg/master/blog/article/201711/vue-checklist/1.png"/></div>

画的有点丑，手头没有什么好用的图片标注工具，用的 Mac 原生标注工具

经过上面的的分析，我们就知道这个组件要做些什么了，接下来我们就开始撸代码，首先我们先把组件基本外观和架子做出来。

首先，我们新建一个checklist.vue文件：
```html
<template>
    <div class="cl-checklist">
      checklist
    </div>
</template>
<script>
</script>
<style scoped>
</style>
```
为了便于开发时测试和观察，我们还得建一个demo.vue文件，在demo.vue中引入我们的checklist.vue组件：
```html
<template>
  <div class="cl-div">
    <div class="center">checklist demo</div>
    <div>
      <input type="text" placeholder="请选择考场">
    </div>
    <checklist></checklist>
  </div>
</template>
<script>
  import checklist from '@components/checklist/checklist'
  export default {
    components: {
      checklist
    }
  }
</script>
<style scoped>
  .center{
    text-align: center;
    font-size: 18px;
  }
</style>
```

## 第一步：实现组件骨架和基础结构
### 1.1 实现基本骨架
我们先把基本模块写出来，用背景颜色区分一下，写完后再把背景颜色去掉，我日常写页面都是这样，这样可以清晰的看到模块边界在哪里。
checklist.vue
```html
<template>
  <div class="cl-checklist">
    <div class="topbar"></div>
    <div class="desc">您已选中0个，最多可选3个</div>
    <div class="list">
    </div>
  </div>
</template>
<script>
</script>
<style scoped>
  .topbar{
    height: 30px;
    background-color: #d0000e;
  }
  .desc{
    padding: 10px 15px 0 0;
    font-size: 14px;
    text-align: right;
    color: #fff;
    background-color: #0d2e44;
  }
  .list{
    height: 300px;
    background-color: #00b4ff;
  }
</style>
```
效果如下：
<div align="center"><img width="320" src="https://raw.githubusercontent.com/dunizb/cloudimg/master/blog/article/201711/vue-checklist/2.png"/></div>

### 1.2 实现topbar
<div align="center"><img width="320" src="https://raw.githubusercontent.com/dunizb/cloudimg/master/blog/article/201711/vue-checklist/3.png"/></div>

topbar有三个元素，如何选择布局方式呢？可以看出，取消、完成按钮是左右对齐，中间title是居中对齐的。我们可以选择传统的浮动布局，使用三个div，比如叫：  

```html
<div class="cancel">取消</div>
<div class="title">选择考场</div>
<div class="confirm">确定</div>
```

这样需要给div宽度，给取消、确定左右对齐，title居中对齐。**NO！NO！NO！**太麻烦了！我们使用**Flexbox**布局，后面我都将使用Flexbox布局。来看看Flexbox如何轻松解决这个布局。

HTML：
```html
<div class="topbar">
   <span class="cancel">取消</span>
   <span class="title">选择考场</span>
   <span class="confirm">完成</span>
</div>
```
CSS：
```css
.topbar{
    display: -webkit-flex;
    display: flex;
    justify-content: space-between;
    align-items: center;
    height: 45px;
    font-size: 16px;
    padding: 0 13px;
    border-bottom: 1px solid rgb(217,217,217);
}
.topbar .cancel{
    color: rgb(159,159,159);
}
.topbar .confirm{
    color: rgb(46,166,242);
}
```
我们使用`justify-content: space-between`让他们水平两端对齐，然后`align-items: center`垂直居中对齐，再给个左右`padding`即可。效果如下：
<div align="center"><img width="320" src="https://raw.githubusercontent.com/dunizb/cloudimg/master/blog/article/201711/vue-checklist/4.png"/></div>

我在项目中用的是`display:inline-block`来布局，做的没现在的好，这种事后用文章的形式来复盘和输出能够让自己更清楚的认识怎么更好的去组织代码，这也是我坚持输出的原因。

### 1.3 实现操作提示栏
这个比较简单，在这个部分直接一起给撸了吧。
HTML
```html
<template>
  <div class="cl-checklist">
    <div class="topbar">
      <span class="cancel">取消</span>
      <span class="title">选择考场</span>
      <span class="confirm">完成</span>
    </div>
    <div class="desc">您已选中0个，最多可选3个</div>
    <div class="list">
    </div>
  </div>
</template>
```
CSS
```css
.desc{
  height: 30px;
  line-height: 30px;
  padding-right: 10px;
  font-size: 14px;
  text-align: right;
  color: rgb(159,159,159);
}
```
效果如下：
<div align="center"><img width="320" src="https://raw.githubusercontent.com/dunizb/cloudimg/master/blog/article/201711/vue-checklist/5.png"/></div>

## 第二步：实现list列表结构
### 2.1 实现基本骨架
我们先来回顾以下第零部多DOM结构的分析：
<div align="center"><img width="640" src="https://raw.githubusercontent.com/dunizb/cloudimg/master/blog/article/201711/vue-checklist/6.png"/></div>

可以看到我们把DOM结构总体划分为左右结构，然后左边的又分为上下结构，左边的checkbox水平垂直居中

我们先把结构基本勾勒出来
HTML：
```html
<--  省略上面的代码 -->
<div class="list">
  <div class="line">
     <div class="l">
        <div class="title">科目二第07考点马路</div>
        <div class="address">上海市宝山区保安公路2009号</div>
     </div>
     <div class="r"></div>
  </div>
</div>
<--  省略下面的代码 -->
```
CSS：
```css
.list{
    height: 300px;
    font-size: 14px;
    padding: 10px 13px;
    background-color: #00b4ff;
  }
.list .line {
    display: -webkit-flex;
    display: flex;
    justify-content: center;
    align-items: center;
    height: 50px;
    background-color: #4caf50;
  }
.list .line .l{
    display: -webkit-flex;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: flex-start;
    width: 90%;
    background-color: #d0000e;
  }
.list .line .r{
    width: 20px;
    height: 20px;
    background-color: #0d2e44;
}
```
效果如下：
<div align="center"><img width="320" src="https://raw.githubusercontent.com/dunizb/cloudimg/master/blog/article/201711/vue-checklist/7.png"/></div>

接下来就是完善了，以及右边的checkbox圆圈。**注意**，我们不能给`.line`设死高度，这个高度应该由内容撑开，因为我们要考虑没有地址信息的时候的展示。

### 2.2 实现checkbox选框
为了方便展示选中状态，我们复制一行`.line`，设置这一行为选中状态，也就是加一个`.selected`的class，然后我们对`.selected`写选中状态的样式
```html
<div class="list">
      <div class="line">
        <div class="l">
          <div class="title">科目二第07考点马路</div>
          <div class="address">上海市宝山区保安公路2009号</div>
        </div>
        <div class="r"></div>
      </div>
      <div class="line selected">
        <div class="l">
          <div class="title">科目二第07考点马路</div>
          <div class="address">上海市宝山区保安公路2009号</div>
        </div>
        <div class="r"></div>
      </div>
</div>
```
我们继续CSS：
```css
.list .line .r{
    width: 20px;
    height: 20px;
    margin: 0 5px;
    -webkit-border-radius: 50%;
    border-radius: 50%;
    border:1px solid #9e9e9e;
    background-color: #fff;
    position: relative;
    z-index: 0;
  }
 .list .line.selected .l .title{
    color: #1799fa;
  }
  .list .line.selected .r{
    border: 1px solid #1799fa;
    background-color: #1799fa;
  }
  .list .line.selected .r::before{
    content: ' ';
    position: absolute;
    top: 4px;
    left: 4px;
    width: 12px;
    height: 12px;
    background-image: url("data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAABUAAAAPCAYAAAALWoRrAAAA90lEQVQ4ja3TMSuFURgH8GeQRGIwy6BkUbIYlHwBu0Umi8VkMVlMJoMvIcNdDAYlJuULWCSESBSLwc9we/P2eu715t6nznKe//Or0zknEF1YS3jAHRa7Aa7iy0/ddAquVUC47ARcT8APLPwX3PC73jGPCPRiGSvoqwFuJuAb5opMoFFqnmCwDbiVgK+YLecCn5XQGYYScDsBXzBTzQb2k/A5hkvBnSTzhOnsRIEBHCdDFxjBbtJ7xFQGFmigH0fJ8HOyd4/JVmAZDc2bP0yQct1ioh1YRYvn1cg0XGP8LzBDC/igAl5hrA7YCg30YE/zl5xitC6I+AYJmBaJbbKurAAAAABJRU5ErkJggg==");
    background-repeat: no-repeat;
    background-size: contain;
    background-position: center center;
    z-index: 1;
}
```
这里为了不依赖图片，我们把勾的图片编码成base64格式，同时我们先把背景去掉，效果如下：
<div align="center"><img width="320" src="https://raw.githubusercontent.com/dunizb/cloudimg/master/blog/article/201711/vue-checklist/8.png"/></div>

### 2.3 地址信息前面的小图标
这个小图标最好使用伪元素来实现
```css
.list .line .l .address{
    color: rgb(159,159,159);
    position: relative;
    padding-left: 15px;
  }
  .list .line .l .address::before{
    content: ' ';
    display: inline-block;
    position: absolute;
    width: 15px;
    height: 15px;
    top: 2px;
    left: 0;
    background-image: url("data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAABQAAAAbCAYAAAB836/YAAABu0lEQVRIiaXUzU8TQRjH8U9fpAkXIZqItHghxEgietuD/gP84R420QSDcCDxIq6KaCoJYCuaeJhdu93OlILfpEnnmWd++7zMM63d3V0RuljHQ9xFr7SP8QNfUOBPlmUzB5tsYBtLkb0eHpS/JzjEcd2hs7W1Vf1v4TkeoxMLu0EHa0VRLBdFcTIYDEC75vCsjO6mbGCnWlSCg1uKVTzK83xAqGFHqFmMX3iP7+X6HjbF67ud5/nnLvomXaxziVcY1WxDfMQLLDf8e+i3sZaIbq8hVjHC28SZtTZWIhs/TdKM8S3xsZW2eLrjOWIVMcFeO2Ik1Kc1R6xltoYI1+Y8Yl8Sxi7Funinz9s4SRzaEa/vKp4mzpx0hSHfjGzewUt8EpoA98voUuUoujgT7tdqxKEl3NN+QqDOMMuys6opRwscuI4jJrP8VYjytgxLjanX5uA/BA+qh7YuOBQadFOKLMv+Zde82Ae4uoHYlUZmTcGx8KwvymGWZVNjGhu9DzhdQOy09J0iNct75qd+VfrMkBIcYX+O4L74a5MUJHT8OGI/Nuc2zBOEd7iorS9KW5LrBH/jjZDeCK9LW5K/QatiGcsSFOsAAAAASUVORK5CYII=");
    background-repeat: no-repeat;
    background-size: contain;
    background-position: 0;
}
```
<div align="center"><img width="320" src="https://raw.githubusercontent.com/dunizb/cloudimg/master/blog/article/201711/vue-checklist/9.1.png"/></div>

### 2.4 适配移动端1像素边框
实现适配移动端1px边框，主要是根据设备的dpr来对边框进行缩放处理，CSS写法如下：
```css
.border-1px{
    position: relative;
}
.border-1px::after{
    display: block;
    position: absolute;
    left: 0;
    bottom: 0;
    width: 100%;
    border-bottom: 1px solid rgb(217,217,217);
    content: ' ';
}
@media (-webkit-min-device-pixel-ratio: 1.5), (min-device-pixel-ratio: 1.5) {
    .border-1px::after {
      -webkit-transform: scaleY(0.7);
      transform: scaleY(0.7);
    }
}
@media (-webkit-min-device-pixel-ratio: 2), (min-device-pixel-ratio: 2) {
    .border-1px::after {
      -webkit-transform: scaleY(0.5);
      transform: scaleY(0.5);
    }
}
```
然后我们在需要的地方设置`.border-1px`的样式
```html
<div class="list">
      <div class="line border-1px">
        <div class="l">
          <div class="title">科目二第07考点马路</div>
          <div class="address">上海市宝山区保安公路2009号</div>
        </div>
        <div class="r"></div>
      </div>
      <div class="line border-1px selected">
        <div class="l">
          <div class="title">科目二第07考点马路</div>
          <div class="address">上海市宝山区保安公路2009号</div>
        </div>
        <div class="r"></div>
      </div>
</div>
```
我们在每一行的`.line`元素上添加`border-1px`，效果如下：

<div align="center"><img width="320" src="https://raw.githubusercontent.com/dunizb/cloudimg/master/blog/article/201711/vue-checklist/9.png"/></div>

更多移动端1像素边框问题可以参看[《移动端1像素边框问题》](//www.qinshenxue.com/article/20151104151932.html)

### 2.5 列表滚动
实现滚动很简单，只要给父级元素也就是我们代码中的`.list`元素设置一个高度，在这里我们的数据多少不一定，所以我们最好只设置一个最大高度`max-height`即可，同时需要给最外层DIV也就是`.cl-checklist`设置`overflow:hidden`。我们先复制很多行来进行测试。
CSS：
```css
.cl-checklist{
    overflow: hidden;
}
.list{
    /*height: 300px;*/
    max-height: 300px;
    font-size: 14px;
    padding: 10px 13px;
    overflow-y: auto;
    -webkit-overflow-scrolling: touch; 
    overflow-scrolling: touch;
    /*background-color: #00A2E6;*/
}
```
注意`overflow-scrolling: touch; `属性，设置该属性是为了适配在移动端下滚动不平滑的问题，现在的效果如下：
<div align="center"><img width="320" src="https://raw.githubusercontent.com/dunizb/cloudimg/master/blog/article/201711/vue-checklist/list-overflow.gif"/></div>

## 第三步：实现选择CheckBox的交互功能
### 3.1 实现原理与v-model
这一步是整个组件的一个核心也是重点难点，这一步写的好、写的巧就会对后面的逻辑交互简化很多。我们借助HTML的原生功能特性来实现：
```HTML
<label for="xxx"><input id="xxx" type="checkbox" value=""></label>
```
这个标签组合可以实现点击`<label>`包裹起来的范围的时候触发`checkbox`，根据这个原理我们改造一下HTML代码
```html
<div class="desc">您已选中 <span>{{checkboxValue.length}}</span> 个，最多可选<span>3</span>个</div>
    <div class="list">
      <div class="line-wrapper">
        <label for="1" class="line border-1px">
          <div class="l">
            <div class="title">科目二第07考点马路</div>
            <div class="address">上海市宝山区保安公路2009号</div>
          </div>
          <div class="r"></div>
        </label>
        <input type="checkbox" id="1" v-model="checkboxValue" style="display:none" value="1">
      </div>
      <div class="line-wrapper">
        <label for="2" class="line border-1px">
          <div class="l">
            <div class="title">科目二第07考点马路</div>
            <div class="address">上海市宝山区保安公路2009号</div>
          </div>
          <div class="r"></div>
        </label>
        <input type="checkbox" id="2" v-model="checkboxValue" style="display:none" value="2">
      </div>
      <div class="line-wrapper">
        <label for="3" class="line border-1px">
          <div class="l">
            <div class="title">科目二第07考点马路</div>
            <div class="address">上海市宝山区保安公路2009号</div>
          </div>
          <div class="r"></div>
        </label>
        <input type="checkbox" id="3" v-model="checkboxValue" style="display:none" value="3">
      </div>
</div>
```
主要是把`div.line`的元素变成`<label>`元素，然后在外面再包裹一个`div.line-wrapper`，在`<label>`后面加一个checkbox标签。同时让checkbox不可见我们可以给checkbox设置`display:none`或者给外围的`div.line-wrapper`设置`overflow:hidden`都可以，这里我使用`display:none`。

Vue.js 提供了 v-model 指令，用于在表单类元素上双向绑定数据，例如在输入框上使用时，输入的内容会实时映射到绑定的数据上。单选按钮在单独使用时不需要 v-model ，直接使用 v-bind 绑定一个布尔类型的值，为真时选中，为否时不选中，如果是组合使用来实现互斥效果时就需要 v-model 配合 value 来使用。

这里给checkbox用 v-model 指令绑定了一个`checkboxValue`，这个值必须是一个数组，然后Vue.js 会帮我们自动每次变更数组：
```js
export default {
    data () {
      return {
        checkboxValue: []
      }
    }
}
```
在操作提示栏里我们给当前选择了几个设置成了`checkboxValue`的长度，这样之后我们来试试，可以发现每次选择一个都会往数组中push一次，再次点击则会从数组中移除。效果如下：
<div align="center"><img width="320" src="https://raw.githubusercontent.com/dunizb/cloudimg/master/blog/article/201711/vue-checklist/list-chexkbox.gif"/></div>

### 3.2 加个选中样式
现在是可以选中了，但是如何给选中的项加上选中的CSS呢，想来想去也是个麻烦事，不过得借助JS来实现了，不知道广大网友有没有牛逼方法。

我们在checkbox标签上绑定一个事件`selectedItem`：
```html
<div class="line-wrapper">
   <label for="1" class="line border-1px">
       <div class="l">
            <div class="title">科目二第07考点马路</div>
            <div class="address">上海市宝山区保安公路2009号</div>
       </div>
       <div class="r"></div>
    </label>
    <input type="checkbox" id="1" @click="selectedItem($event)" v-model="checkboxValue" style="display:none" value="1">
</div>
```    
```js
methods: {
     selectedItem (event) {
        const labelNode = event.target.previousElementSibling
        const classList = labelNode.classList
        classList.contains('selected') ? classList.remove('selected') : classList.add('selected')
    }
}
```
点击的时候获取Event事件对象，然后通过`previousElementSibling`找到上一个兄弟节点，给他绑定`.selected`class即可
<div align="center"><img width="320" src="https://raw.githubusercontent.com/dunizb/cloudimg/master/blog/article/201711/vue-checklist/list-chexkbox-seclected.gif"/></div>

### 3.3 最多可选择几项
#### 3.3.1 使用 props 传递数据
用户是可以设置最多选择几项的，于是 Vue.js 的 Props 功能派上用场了。在 Vue.js 组件中，使用选项 Props 来声明需要从父级接收的数据，props 的值可以是两种，一种是字符串，一种是对象，这里我们使用对象，一个`Number`对象。

先定义 props 
```js
props: {
   max: {
       type: Number,
       default: 0
    }
 }
```
我们定义了一个max属性，它的类型是`Number`类型，默认值是 0 。然后我们把`max`加到操作提示中
```html
<div class="desc">您已选中 <span>{{checkboxValue.length}}</span> 个，最多可选<span>{{max}}</span>个</div>
```
然后我们就可以给组件传递`max`属性了，现在转到demo.vue文件：
```html
<template>
  <div class="cl-div">
    <div class="center">checklist demo</div>
    <div>
      <input type="text" placeholder="请选择考场">
    </div>
    <checklist :max="2"></checklist>
  </div>
</template>
```
我们给`max`设置为2，也就是最多选择2个。

#### 3.3.2 Watch选项、$refs的使用
当我们选择了两个的时候其他的选择项就应该灰掉（禁用），那么就要监控 data 选项里的`checkboxValue`的长度了，这时候我们需要用到Vue.js的 watch 选项，watch 是一个对象，键是需要观察的表达式，值是对应回调函数。值也可以是方法名，或者包含选项的对象。Vue 实例将会在实例化时调用`$watch()`，遍历 watch 对象的每一个属性。

> **注意**，不应该使用箭头函数来定义 watcher 函数 (例如`searchQuery: newValue => this.updateAutocomplete(newValue))`。理由是箭头函数绑定了父级作用域的上下文，所以 this 将不会按照期望指向 Vue 实例，`this.updateAutocomplete` 将是 `undefined`。

我们通过监听 data 选项里的`checkboxValue`，来判断它的长度，如果它的长度刚好已经和设置的`max`属性相等了，就给其他添加`.disabled`这个class，同事给`input checkbox`添加`disabled`属性。
```js
watch: {
   checkboxValue (val) {
      const listDom = this.$refs['list']
      const lines = listDom.querySelectorAll('line-wrapper')
      if (val.length === this.max) {
        let item = null
        for (let i = 0; i < lines.length; i++) {
          item =lines[i]
          if (val.indexOf(lines[i].dataset.val) === -1) {
            item.children[0].classList.add('disabled')
            item.querySelector('input[type="checkbox"]').setAttribute('disabled', 'disabled')
          }
      }
   } else {
        let item = null
        for (let i = 0; i <lines.length; i++) {
          item =lines[i]
          if (item.children[0].classList.contains('disabled')) {
            item.children[0].classList.remove('disabled')
            item.querySelector('input[type="checkbox"]').removeAttribute('disabled')
          }
        }
      }
   }
}
```
这个需要配合Vue.js 的 `$refs`来做，在HTML中的`.list`节点上设置`ref = 'list'`，也就是为了方便选择这个DOM节点，当然你用传统的`document.querySelector`来选择`.list`节点也是没问题的。
```html
<div class="list" ref="list">
```
上面代码的第9行，判断是否当前DOM是否在选中的数组中，拿的是`checkboxValue`的数组项和一个自定义属性值比较，这个自定义属性叫`data-val`，他的值跟`input checkbox`的 value 值保持一致，这个`val`自定义属性设置在`.list-wrapper`节点上是为了方便DOM查找，减少DOM查找层数，不然就需要获取`input checkbox`的 value 值来比较。

设置`.disabled`的CSS如下：
```css
.list .line.disabled .l .title{
    color: #9e9e9e;
}
.list .line.disabled .r{
    border: 1px solid #9e9e9e;
    background-color: #9e9e9e;
}
```

## 第四步：组件显示隐藏
### 4.1 显示组件
这一步我们要做一下的组件的显示与隐藏，点击输入框从页面底部显示组件，点击取消或者蒙层从上到下隐藏组件，并且添加过渡动画。这其实使用定位和CSS3的`transform`属性即可实现。
```css
.cl-checklist{
    overflow: hidden;
    position: fixed;
    bottom: 0;
    left: 0;
    width: 100%;
    -webkit-transition: all .5s;
    transition: all .5s;
    -webkit-transform: translateY(100%);
    transform: translateY(100%);
}
.cl-checklist.show{
    -webkit-transform: translateY(0%);
    transform: translateY(0%);
}
```
我们还得为组件弄一个是否显示和隐藏的属性`isOpen`，默认为`false`不显示，用它来控制给组件动态添加显示和隐藏的`.cl-checklist.show`类。
```html
<div class="cl-checklist" :class="{'show': isOpen}">
```
那在demo.vue中如何调用这个属性呢？这时候我们就不得不考虑对外提供方法了，我们可以定义一个显示和隐藏的方法来供使用者调用。
```js
methods: {
   show () {
      this.isOpen = true
   },
   hide () {
      this.isOpen = false
   }
}
```
在demo.vue中，为输入框添加事件，然后调用组件的 show 方法
```html
<template>
  <div class="cl-div">
    <div class="center">checklist demo</div>
    <div>
      <input type="text" @focus="openChecklist" placeholder="请选择考场">
    </div>
    <checklist ref="checklist" :max="2"></checklist>
  </div>
</template>
<script>
  import checklist from '@components/checklist/checklist'
  export default {
    methods: {
      openChecklist () {
        this.$refs['checklist'].show()
      }
    },
    components: { checklist }
  }
</script>
```
现在的效果如下：
<div align="center"><img width="320" src="https://raw.githubusercontent.com/dunizb/cloudimg/master/blog/article/201711/vue-checklist/checklist-show.gif"/></div>

### 4.2 隐藏组件
做好了显示那隐藏就很简单了，点击取消隐藏组件，动画会原路返回，只需要为取消设置一下`isOpen = false`或者调用`hide`方法即可。
```html
<div class="topbar">
   <span class="cancel" @click="hide">取消</span>
   <span class="title">选择考场</span>
   <span class="confirm">完成</span>
</div>
```
现在效果如下
<div align="center"><img width="320" src="https://raw.githubusercontent.com/dunizb/cloudimg/master/blog/article/201711/vue-checklist/checklist-hide.gif"/></div>

### 4.3 添加蒙层
为了使蒙层能够覆盖整个页面，还不得不为DOM结构做一下调整
```html
<div class="cl-checklist">
    <div class="checklist" :class="{'show': isOpen}">
        ... ...
    </div>
    <!--蒙层-->
    <div class="checklist-overlay"  v-if="isOpen"></div>
</div>
```
`.checklist-overlay`的CSS如下
```css
.checklist-overlay{
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  z-index: 1000;
  background: rgba(0, 0, 0, .5);
  transition: all .5s;
}
```
对应的，DOM结构调整后，最外层的样式也要改一下
```css
.cl-checklist{
    overflow: hidden;
}
.checklist{
    position: fixed;
    bottom: 0;
    left: 0;
    z-index: 2000;
    width: 100%;
    background-color: #fff;
    -webkit-transition: all .5s;
    transition: all .5s;
    -webkit-transform: translateY(100%);
    transform: translateY(100%);
}
```
特别注意，为`.checklist`增加了白色背景和`z-index:2000`，现在的效果如下
<div align="center"><img width="320" src="https://raw.githubusercontent.com/dunizb/cloudimg/master/blog/article/201711/vue-checklist/checklist-mengceng.gif"/></div>

当然了，你也可以让点击蒙层的时候也可以隐藏组件，直接给蒙层绑定一个单击事件`@click = "hide"`即可。

### 4.4 移动端input输入框阻止弹起手机虚拟键盘
在移动端，input会默认触发手机的虚拟键盘，如何阻止手机虚拟键盘弹起呢？目前我试过有两个方案，一个是给input添加`readonly`属性,另一个就是在input事件处理方法前面添加一句`document.activeElement.blur() `。关于这个问题的详细可以阅读我的另一篇博客[《小技巧|H5禁止手机虚拟键盘弹出》](https://dunizb.com/2017/09/24/disable-the-phone-virtual-keyboard-up/)
```js
methods: {
   show () {
      document.activeElement.blur()
      this.isOpen = true
   }
}
```
## 第五步：数据渲染和向父组件传递事件
此文中子组件就是 checklist.vue ，父组件就是 demo.vue
### 5.1 数据渲染
前面我们的数据都是写死的，现在我们来动态渲染数据，也就是循环数据了。从父组件传递数据，在子组件中接收，还得使用 Props，前面我们定义了一个 `max` 属性，用来控制最多选择几项，我们再添加一个 Props 属性，取名为 `dataList`，这是一个数组类型，并且是必须的
```js
props: {
  max: {
      type: Number,
       default: 0
   },
  dataList: {
      type: Array,
      require: true
  }
}
```
在组件中传递这个 Props ，需要**注意**的就是，由于 HTML 特性不区分大小写，当使用 DOM 模板时，驼峰命名的 props 名称要转为短横线分隔符命名。不能`dataList`在组件中必须使用中划线，变成`data-list` 形式。
```html
<checklist ref="checklist" :data-list="data" :max="2"></checklist>
```
定义 `data` 数据，这里的数据应该是从后端接口中来的，这里我就模拟一下数据了
```js
data () {
   return {
        data: [{
            label: '科目二第07考点马路',
            value: '101',
            address: '上海市宝山区宝安公路2009号'
          },{
            label: '科目二第08考点沪松公路',
            value: '102',
            address: '上海市闵行区沪松公路565弄128号'
          },{
            label: '科目二第09考点七宝',
            value: '103',
            address: '上海市闵行区沪松公路200号'
          },{
            label: '科目二第09考点世纪公园世纪公园',
            value: '104',
            address: ''
          },{
            label: '科目二第09考点世纪公园',
            value: '105',
            address: '上海市浦东新区世纪大道200号'
          },{
            label: '科目二第09考点哈哈哈哈',
            value: '107'
          },{
            label: '科目二第09考点合川路地铁站',
            value: '106',
            address: '上海市合川路地铁站2号出口'
          }]
     }
}
```
最后就是渲染了，回到 checklist.vue 中，把 `v-for` 补上就行了
```html
<div class="list" ref="list">
    <div v-for="(item, index) in dataList" class="line-wrapper" :data-val="item.value">
       <label :for="index" class="line border-1px">
          <div class="l">
             <div class="title">{{item.label}}</div>
             <div class="address" v-if="item.address">{{item.address}}</div>
           </div>
           <div class="r"></div>
       </label>
       <input type="checkbox" :id="index" @click="selectedItem($event)"
             v-model="checkboxValue" style="display:none" :value="item.value">
    </div>
</div>
```
需要注意的就是**第 3 行**和**第 10 行**，`for`的值和`id`的值必须一致，这里最好是使用`v-for`的`index`，当然了，也可以用`item.label`或`item.value`，但不推荐这样做。

### 5.2 组件通信与自定义事件
最后的最后，就是该处理点击“完成”后把选中的值传递给父页面了。我们已经知道从父组件向子组件通信，通过 props 传递数据就可以了，当子组件需要向父组件传递数据时，就需要用到自定义事件。v-on 指令除了可以监听 DOM 事件外，还可以用于组件之间额自定义事件

在 Vue.js 中子组件使用 `$emit()` 来触发事件，父组件使用 `$on()` 来监听子组件的事件。父组件也可以直接在子组件的自定义标签上使用 v-on 来监听子组件触发的自定义事件。

我们给确定使用`@click`按钮绑定一个方法，叫`onConfirm`
```html
<div class="topbar">
    <span class="cancel" @click="hide">取消</span>
    <span class="title">选择考场</span>
    <span class="confirm" @click="onConfirm">完成</span>
</div>
```
我们需要传递什么给父组件？选中的值，这个值应该包含一个考场 value 值（也就是考场的id），选中的考场名称，或许还需要考场的地址，我们可以把这几个值使用`|`符号连接这几个值一起放到单选框的 value 里面
```html
<div class="list" ref="list">
    <div v-for="(item, index) in dataList" class="line-wrapper" :data-val="item.label + '|' + item.value">
        <label :for="index" class="line border-1px">
        <div class="l">
            <div class="title">{{item.label}}</div>
            <div class="address" v-if="item.address">{{item.address}}</div>
        </div>
        <div class="r"></div>
        </label>
        <input type="checkbox" :id="index" @click="selectedItem($event)"
                v-model="checkboxValue" style="display:none" :value="item.label + '|' + item.value">
    </div>
</div>
```
**注意：**第 2 行的 data-val 要跟 单选框的 value 值保持一致，因为接下来的 JS 逻辑需要用到它来和单选框的 value 比较，我们来实现 onConfirm() 方法
```js
onConfirm () {
    this.isOpen = false
    const checkboxValue = this.checkboxValue
    const res = []
    for (let i = 0; i < checkboxValue.length; i++) {
        const resObj = {}
        const item = checkboxValue[i].split('|')
        resObj.label = item[0]
        resObj.value = item[1]
        res.push(resObj)
    }
    this.$emit('on-change', res)
}
```
在方法中，首选取得`checkboxValue`的值，然后分别取出其中的value、label 和 address 三个部分放到一个对象resObj 中，再放到 res 数组中，最后把这个数组对象作为 on-change 事件的返回值参数。

在父组件的子组件标签上我们用 `@on-change` 来接收
```html
<checklist ref="checklist"
               :data-list="data"
               :max="2" @on-change="changeKaochangValue"></checklist>
```
在父组件的 data 选项中定义一个`kaochangVal`属性来接收，然后把选中的考场名称打印出来
```html
<p v-for="(item, index) in kaochangVal">{{item.label}}</p>
```
changeKaochangValue 方法
```js
changeKaochangValue (val) {
    this.kaochangVal = val
}
```
现在的效果如下：
<div align="center"><img width="320" src="https://raw.githubusercontent.com/dunizb/cloudimg/master/blog/article/201711/vue-checklist/emit.gif"/></div>

至此，这个 Checklist 组件算是完成了。

## 第六步：扩展和完善
### 设置选框在左边
考虑通用性，假如需求需要 CheckBox 框在左边呢？这个问题其实很好解决，因为我们使用 Flexbox 布局，天然支持，只需要多加一句样式即可。这个特性应该是可以用户设置的，也就是得弄一个 props 属性来支持。
```js
props : {
  checkboxLeft: {
    type: Booolean,
    default: false
  }
}
```
定义一个 checkboxLeft 属性，默认为 false 也就是 默认 checkbox 在右边，只有用户显示传递改值为 true 时 checkbox 才在左边。

前面说只需要加一个样式就可以让 checkbox 在左边了，为 `.line` 元素设置一个样式 class ,然后通过 checkboxLeft 这个 props 来动态绑定 class
```css
.list .line.checkbox-left{
    flex-direction: row-reverse;
}
```
```html
...
<label :for="index" class="line border-1px" :class="{'checkbox-left': checkboxLeft}">
...
```
熟悉 Flexbox 的同学应该知道，`flex-direction`是控制布局的方向，`row-reverse`就是倒序的意思，原来是 12 排列，row-reverse 后就变成 21 排列了。

在组件上（demo.vue）设置
```html
<checklist ref="checklist"
               :data-list="data"
               :max="2"
               :checkbox-left="true"
               @on-change="changeKaochangValue"></checklist>
```
显示设置 props 的`checkboxLeft` 为 true 即可
<div align="center"><img width="320" src="https://raw.githubusercontent.com/dunizb/cloudimg/master/blog/article/201711/vue-checklist/10.png"/></div>

### 还可以做点什么呢？
大家可以扩展一下...

******
[完整源码](https://github.com/dunizb/vue-components/tree/master/src/checklist)