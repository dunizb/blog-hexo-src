---
title: 小技巧|使用Vue.js的Mixins复用你的代码
date: 2019-01-02 16:08:09
categories:
- 前端开发
tags:
- Vue.js
- 小技巧
description: "Vue中的混入 mixins 是一种提供分发 Vue 组件中可复用功能的非常灵活的方式。听说在3.0版本中可能会用Hooks的形式实现，但这并不妨碍它的强大。"
---
![云南大理崇圣寺](//ww4.sinaimg.cn/large/006tNc79ly1g5d7vfccx3j31400u0u10.jpg)

Vue中的混入 [mixins](https://cn.vuejs.org/v2/guide/mixins.html) 是一种提供分发 Vue 组件中可复用功能的非常灵活的方式。听说在3.0版本中可能会用Hooks的形式实现，但这并不妨碍它的强大。基础部分的可以看这里。


这里主要来讨论 mixins 如何优化我们的数据列表代码。

如果我们有大量的表格页面，仔细一扒拉你发现非常多的东西都是可以复用的例如分页，表格高度，加载方法， laoding 声明等一大堆的东西。下面我们来整理出来一个简单通用混入 `list.js`

list.js
```js
const list = {
  data () {
    return {
      loading: false,
      pageParam: {
        pageNum: 1, // 页码
        pageSize: 20, // 页长
        total: 0 // 总记录数数
      },
      pageSizes: [10, 20, 30, 50], // 页长数
      pageLayout: 'total, sizes, prev, pager, next, jumper', // 分页布局
      pageCount: 5, // 页码按钮的数量，当总页数超过该值时会折叠(大于等于 5 且小于等于 21 的奇数)
      list: []
    }
  },
  methods: {
    // 分页回掉事件
    handleSizeChange (val) {
      this.pageParam.pageSize = val
      this.getList()
    },
    handleCurrentChange (val) {
      this.pageParam.pageNum = val
      this.getList()
    },
    /**
     * 表格数据请求成功的回调 处理完公共的部分（分页，loading取消）之后把控制权交给页面
     * @param {*} apiResult
     * @returns {*} promise
     */
    listSuccessCb (apiResult = {}) {
      return new Promise((resolve, reject) => {
        let tempList = [] // 临时list
        try {
          this.loading = false
          tempList = apiResult.data
          this.pageParam.total = apiResult.page.total
          // 直接抛出
          resolve(tempList)
        } catch (error) {
          reject(error)
        }
      })
    },
    /**
     * 处理异常情况
     * ==> 简单处理 仅仅是对表格处理为空以及取消loading
     */
    listExceptionCb (error) {
      this.loading = false
      console.error(error)
    }
  },
  created () {
    // 这个生命周期是在使用组件的生命周期之前
    this.$nextTick().then(() => {
      // todo
    })
  }
}
export default list
```

下面我们直接在组件中使用这个`mixins`
```js
import mixin from '@/mixins/list' // 引入
import {getList} from '@/api/demo'
export default {
  name: 'mixins-demo',
  mixins: [mixin], // 使用mixins
  data () {
    return {
    }
  },
  methods: {
    // 加载列表
    getList () {
      const params = { ...this.searchForm, ...this.pageParam }
      fetchUserList(params).then(res => {
        if (res.code === 0) {
          this.listSuccessCb(res).then((list) => {
            this.list = list
          }).catch((err) => {
            console.log(err)
          })
        }
      })
    },
  },
  created() {
    this.load()
  }
}
```
使用了 mixins 之后一个简单的有 loadoing, 分页,数据的表格大概就只需要上面这些代码。

在`list.js`中我们可以直接调用组件的方法，比如在分页回调事件中调用组件的 `getList()`方法，在组件中直接调用 `list.js`中的代码，如直接访问 `this.pageParam`。


当组件和 mixins 对象含有同名选项时，这些选项将以恰当的方式混合。比如，数据对象在内部会进行**浅合并** (一层属性深度)，在和组件的数据发生冲突时以组件数据优先。

你学会了吗？还不快试试。。。

