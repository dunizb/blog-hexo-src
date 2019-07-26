---
title: Vue.js常用开发知识简要入门（三）
date: 2017.02.13 10:52:00
categories:
- 前端开发
- 前端框架
tags:
- Vue.js
- 学习笔记
- 总结
description: "Vue.js学习笔记（三）"
---

## 组件的注册与使用

例如，注册一个Hello组件：
```js
var Hello = Vue.extend({        //返回预设选项的Vue构造器（子类）
   data: function() {
       return {
            msg: 'Hello，Vue.js'
       }
   },
   template: '<h1>{{msg}}</h1>'      
});
```

这样，就创建了一个`h1`标签，标签内容为Hello，Vue.js的Hello组件，我们可以使用全局注册来使用这个组建：
```js
<body>
    <Hello></Hello>
</body>
<script>
//全局注册
Vue.component('Hello',Hello);

new Vue({
     el: 'body',
     components: {
          // 'Hello': Hello,    //局部注册
          'Temp': Temp
     }
});
</script>
```

当然，我们也可以使用局部注册。

template实例模版，用户替换或插入挂载元素，元素可以使用`#`选择子，使用匹配元素的`innerHTML`作为模板
```js
<body>
    <Hello></Hello>
    <Temp></Temp>

    <template id="template-use">
        <p>{{msg}}</p>
        <p>Demos 代码演示、代码片段 - 读你 | 这世间唯有梦想和好姑娘不可辜负!</p>
    </template>
</body>
<script>
var Temp = Vue.extend({ 
   data: function() {
       return {
            msg: '组件的注册与使用'
       }
   },
   template: '#template-use'      
});

new Vue({
     el: 'body',
     components: {
          'Hello': Hello,    //局部注册
          'Temp': Temp
     }
});
</script>
```

**使用 Vue 扩展构造 Vue 实例时，实例选项中的 methods 属性会覆盖扩展选项中的 methods 属性？**

- Vue 扩展是一个提供了部分可复用的数据、方法……选项的 Vue 构造器，可以直接使用 new MyVue(options) 这样的方式来构造 Vue 实例；只要愿意，可以在一个页面上使用 Vue 构造器或者 Vue 扩展构造器创建任意个 Vue 实例；使用 Vue 扩展构造 Vue 实例时，选项级别的属性并不是直接覆盖.
- 如 Vue 扩展选项里包括 methods 属性，调用该扩展构造器时也传入了 methods，则会将两个 methods 中的内容合并成一个新对象，仅当存在键名冲突时，实例选项中的方法覆盖扩展选项中的同名方法.
- data 属性的情况类似，但更复杂，键名冲突时，当 data 属性中的数据是数值时，使用实例选项中的数据覆盖扩展选项中的数据，当 data 属性中的数据是对象时，将两个对象按相同规则继续合并，此外，同名钩子会组成数组，在生命周期中事件触发时均会被调用.
- 使用 Vue.extend 创建 Vue 扩展时，扩展选项中的 data 和 el 必须是返回相关数据的函数，即使使用指向同一个数据对象的变量作为函数的返回值，通过 Vue 扩展创建的 Vue 实例的数据也是不同的。

**注意：**
当自定义标签所对应的模板中可能包含多个顶级元素时，由于指令优先级的关系，不能使用 v-show 指令，并且在任何自定义标签后的元素中，v-else 均无效；由于 v-if 的初始化处理方式不同（根据绑定数据决定是否创建 DOM 元素，而不是设置 display 属性），在自定义标签中使用 v-if 指令是可以的，并且允许在后续标签中使用 v-else。

## 使用props传递数据

```js
<body>
   <child title="Vue.js权威指南" author="滴滴前端" price="89"></child>
</body>
<script src="http://cdnjs.cloudflare.com/ajax/libs/vue/1.0.26/vue.min.js"></script>
<script type="text/javascript">
    //注册组建
    var child = Vue.component('child',{
        props: [
            'title','author','price'
        ],
        template: `<span :title="title">{{title}}</span>
                <span :author="author">{{author}}</span>
                <span :price="price">{{price}}</span>`
    });

    //使用组建
    new Vue({
        el: 'body',
        components: { child }
    });
</script>  
```

这里使用了另一种创建组建的方式，属于Vue语法糖，背后会自动使用`Vue.extend()`.

### prop 校验

- type：类型 
- required：是否必须 
- towWay：是否双向 
- default：默认值
- validator：自定义校验函数
- coerce：转换函数
- type 为 String、Number、Boolean、Function、Object、Array等原生构造器或自定义构造器
- 仅有 type 时可不适用对象语法
- 可设置为构造器数组
- 如果设置了转换函数，先转换再判断 type

## 单文件组建规范

### 单文件组建的结构
```js
<template>
    <span>![](../img/logo.png)</span>
</template>
<script>
export default{
    props: ['componentName']
}
</script>
<style>
img.log{
    width:16px;height:16px;
}
</style>
```

### 语言块
**template块**
默认语言html，每个文件允许最多一个template块

**script块**
默认语言js，每个文件允许最多一个script块

**style块**
默认语言CSS，支持多个style块

Vue.js 的单文件组件中不一定要有 template 语言块，再者，即使确实需要 template 属性，script 语言块中输出的组件选项中也可以包含字符串形式的 template属性。为了更好地组织样式，一个单文件组件中允许有多个 style 语言块。template 语言块的默认语言是 html，还可以设置为其他预处理的模板语言如 jade。script 语言块的默认语言是 js，也允许设置使用其他语言如 CoffeeScript、TypeScript 等。

### src引用
```js
<template src="./template.html">
</template>
<script src="./script.js">
</script>
<style src="./style.css">
</style>
```

支持src引用，遵循CommonJS路径规范。

**********
本人出售《Vue.js权威指南》，二手价20元！点击下图购买

**++++++++++[20元出售此书](http://dunizb.com/obook/)++++++++++**
[![Vue.js权威指南](http://ww1.sinaimg.cn/large/006tNc79ly1g5d84xfwwvj30g20jkdjd.jpg)](http://dunizb.com/obook/)