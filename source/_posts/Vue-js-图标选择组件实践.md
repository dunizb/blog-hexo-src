---
title: Vue.js 图标选择组件实践
date: 2018-12-01 15:03:17
categories:
- 前端开发
tags:
- Vue.js
description: "Vue.js 图标选择组件实践，不是什么高深文章，随便看看吧"
---
![Vue.js 图标选择组件实践](//ww4.sinaimg.cn/large/006tNc79ly1g5d7yo4o9jg30z50j94e3.gif)

## 背景
最近项目中在做一个自定义菜单需求，其中有一个为菜单设置小图标的功能，就是大家常见的左侧菜单
![左侧菜单](//ww3.sinaimg.cn/large/006tNc79ly1g5d7ypuzt4j30v40lwdl5.jpg)

设置图标不难，方案就是字体图标，可供使用的图标库也有很多，比如阿里巴巴的 Iconfont，以及 Fontaswsome 等，问题在于如何优雅的提供几百个图标供用户选择，而不需要开发去一个一个的写标签，也不需要一个个的去找图标。

## 字体图标库 Fontawesome 方案

我们使用字体图标的方式，一般是一个 `<i class="iconfont icon-home"></i>` 这样的标签，平常开发中用一些图标都是用到一个写一个，展示10个图标，就要写10个标签。

在项目中本人使用的是 [Fontawesome](https://fontawesome.com/icons?d=gallery) 图标库方案，使用它是因为提供的可用图标比较丰富，基本上不需要特意去找合适的图标，直接把它的图标库下载过来，免费的有800多个。
![Fontawesome](//ww1.sinaimg.cn/large/006tNc79ly1g5d7yrclchj31pw0u04bb.jpg)

这么多图标难道要一个一个手写800多个 `i` 标签吗？三连拒绝！

Fontawesome 下载后的文件中提供一个 svg格式的精灵图，这个非常人性化，用 VSCode 打开这个SVG文件
![svg格式的精灵图](//ww4.sinaimg.cn/large/006tNc79ly1g5d7yrvyagj318y0sq7rf.jpg)

可以看到是熟悉的DOM，因为SVG本质上就是一个XML，既然是DOM，那么祭出**JS大法**吧，用浏览器打开这个SVG文件，在控制台编写如下代码获取所有的图标名称：
```javascript
const nodeArray = Array.from(document.querySelectorAll('symbol'));
const names = nodeArray.map(item => item.id)
names.toString()
```
![](//ww4.sinaimg.cn/large/006tNc79ly1g5d7yspxayj31bi0fm1kx.jpg)

## Icons组件
> 大牛可以忽略

拿到了所有图标的 name 那就好办了，一个数组循环呗。先别急着写代码，我们的目的是封装成组件复用，那么先创建一个 Icons 组件
![](//ww1.sinaimg.cn/large/006tNc79ly1g5d7yuhyaoj31e00nmtia.jpg)

提供一个筛选框，然后给一个事件即可  
```html
<template>
  <div class="ui-fas">
    <el-input v-model="name" @input.native="filterIcons" suffix-icon="el-icon-search" placeholder="请输入图标名称"></el-input>
    <ul class="fas-icon-list">
      <li v-for="(item, index) in iconList" :key="index" @click="selectedIcon(item)">
        <i class="fas" :class="['fa-' + item]" />
        <span>{{item}}</span>
      </li>
    </ul>
  </div>
</template>
<script>
import fontawesome from '@/extend/fontawesome/solid.js'
export default {
  name: 'compIcons',
  data () {
    return {
      name: '',
      iconList: fontawesome
    }
  },
  methods: {
    filterIcons () {
      if (this.name) {
        this.iconList = this.iconList.filter(item => item.includes(this.name))
      } else {
        this.iconList = fontawesome
      }
    },
    selectedIcon (name) {
      this.$emit('selected', name)
    },
    reset () {
      this.name = ''
      this.iconList = fontawesome
    }
  }
}
</script>
```
先把拿到的所有图标name放到一个 solid.js 文件中，输出为数组，在组件中引入，然后就是循环数组 `iconList`，输出`i`标签，Fontawesome 的使用方式是：`<i class="fas fas-图标name"></i>`。

筛选功能利用数组的 filter 方法，`this.$emit('selected', name)` 方式返回给父组件图标名称。

以上样式都是利用Element UI 的 Popover、Input 组件实现
```html
<el-form-item label="图标：" >
  <el-popover
    placement="left-start"
    width="540"
    trigger="click"
    @show="$refs.icons.reset()"
    popper-class="popper-class">
    <ui-icons ref="icons" @selected="selectedIcon" />
    <el-input slot="reference" placeholder="请输入内容" readonly v-model="form.menu_icon" style="cursor: pointer;">
      <template slot="prepend"><i class="fas" :class="['fa-' + form.menu_icon]"></i></template>
    </el-input>
  </el-popover>
</el-form-item>
```
组件实现了，接下来就是引用，既可以直接到导入此组件引用，也可以挂载到全局进行使用，这里说说挂载到全局使用的方式，因为我的项目中所有的公共组件都是挂载到全局的方式使用。

在组件平级新建一个 index.js 文件
![](//ww4.sinaimg.cn/large/006tNc79ly1g5d7ywffnjj315k0e2jw7.jpg)
```js
import IconsCompontent from './Icons.vue'
const Icons = {
  install(Vue) {
    Vue.component('ui-icons', IconsCompontent);
  }
}
export default Icons;
```
第4行为组件命名，此名称决定了如何使用组件，这里是`ui-icons`，那么使用的时候就是：
```html
<ui-icons />
```

接着在项目 components 根目录新建 index.js，这里是所有组件的集合
![](//ww3.sinaimg.cn/large/006tNc79ly1g5d7yz9ijrj31ig0j2aic.jpg)

最后一步是在 main.js 中注册：
```js
import CustomComponents from './components/index.js'
Object.keys(CustomComponents).forEach(key => Vue.use(CustomComponents[key]))
```
这样就可以在项目中任意`.vue`文件中以`<ui-icons />`方式使用组件了。

## 后记
点击图标后要不要关闭图标弹出层（Popover）呢？Popover 是需要鼠标点击其他地方才会隐藏的，选择一个图标后就关闭 Popover 呢，我的做法是：`document.body.click()`。
```js
selectedIcon (name) {
  this.form.menu_icon = name
  // document.body.click()
}
```

....完。