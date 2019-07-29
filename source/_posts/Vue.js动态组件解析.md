---
title: Vue.js动态组件解析
date: 2017-06-19 22:44:28
categories:
- 前端开发
- 前端框架
tags:
- Vue.js
- 学习笔记
description: "什么是动态组件绑定？简单的说，就是几个组件放在一个挂载点下，然后根据父组件的某个变量来决定显示哪个，或者都不显示。"
---

![Vue.js](//ww1.sinaimg.cn/large/006tNc79ly1g5d8f879yxj30p00b0q2x.jpg)
此前的Vue.js系列文章：

 - [Vue.js常用开发知识简要入门（一）](http://dunizb.com/2016/12/18/Vue.js常用开发知识简要入门（一）)
 - [Vue.js常用开发知识简要入门（二）](//www.jianshu.com/p/ce9fc4c8a7ce)
 - [Vue.js常用开发知识简要入门（三）](http://dunizb.com/2017/02/13/Vue.js常用开发知识简要入门（三）)
 - [Vue.js开发常见问题解析](http://dunizb.com/2017/06/19/Vue.js开发常见问题解析/)

什么是动态组件绑定？简单的说，就是几个组件放在一个挂载点下，然后根据父组件的某个变量来决定显示哪个，或者都不显示。

## is属性

在挂载点使用component标签，然后使用`v-bind:is="组件名"`，会自动去找匹配的组件名，如果没有，则不显示；改变挂载的组件，只需要修改`is`指令的值即可。
HTML：
```html
<div id="example">
  <input type="radio" id="one" value="fast" v-model="currentView" />
  <label for="one">fast</label>
  <br />
  <input type="radio" id="two" value="bus" v-model="currentView" />
  <label for="two">bus</label>
  <br />
  <input type="radio" id="three" value="business" v-model="currentView" />
  <label for="three">business</label>

  <component :is="currentView">
    <!-- 组件在 vm.currentView 变化时改变 -->
  </component>
</div>
```
JS：
```js
//创建根实例
new Vue({
  el: "#example",
  data: {
    currentView: 'bus'
  },
  components: {
    fast: {template: '<div>滴滴快车</div>'},
    bus: {template: '<div>滴滴巴士</div>'},
    business: {template: '<div>滴滴专车</div>'}
  }
});
```
通过`is`属性绑定的`vm.currentView`变量值，控制展示的组件，[在线查看效果](http://jsrun.net/aVYKp)

## keep-alive

简单来说，被切换掉（非当前显示）的组件，是直接被移除了。如果把切换出去的组件保留在内存中，则可以保留它的状态或避免重新渲染。为此，可以添加一个keep-alive指令参数。
```js
<keep-alive>
   <!-- 非活动组件将被缓存 -->
    <component :is="currentView"></component>
</keep-alive>
```
Vue.js为其组件设计了一个`keep-alive`特性，如果这个特性存在，那么在组件被重复创建时，会通过缓存机制快速创建组件，以提升视图更新性能。

## activate钩子

简单来说，他是延迟加载。 例如，在发起ajax请求时，会需要等待一些时间，假如我们需要在ajax请求完成后，再进行加载，那么就需要用到`activate`钩子了。

具体用法来说，activate是和template、data等属性平级的一个属性，形式是一个函数，函数里默认有一个参数，而这个参数是一个函数，执行这个函数时，才会切换组件。
```js
Vue.component('activate-example', {
    activate(done) {
        let _this = this;
        loadDataAsync(function(data) {
            _this.someData = data;
            done();
        });
    }
});
```
`activate`钩子只作用于动态组件切换或静态组件初始化渲染的过程中，不作用于使用实例方法手工插入的过程中。

**********
**++++++++++[本人出售《Vue.js权威指南》，二手价20元！](http://dunizb.com/obook/)++++++++++**
[![Vue.js权威指南](//ww2.sinaimg.cn/large/006tNc79ly1g5d8f98fhbj30g20jkdjd.jpg)](http://dunizb.com/obook/)