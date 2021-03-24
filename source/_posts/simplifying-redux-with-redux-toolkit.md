---
title: 使用Redux Toolkit简化Redux
date: 2021-03-24 15:54:44
img: http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202103/redux-toolkit/banner.jpeg
categories:
  - 翻译
tags:
  - React
summary: "了解Redux Toolkit，这是用于高效Redux开发的经过验证的工具集。在本文中，你将看到为什么Redux Toolkit值得React社区更多的关注"
---

> 翻译自[blog.bitsrc.io](https://blog.bitsrc.io/simplifying-redux-with-redux-toolkit-6236c28cdfcb)，作者：Madushika Perera

**了解 Redux Toolkit，这是用于高效 Redux 开发的经过验证的工具集。在本文中，你将看到为什么 Redux Toolkit 值得 React 社区更多的关注。**

<!-- more -->

React 和 Redux 被认为是大规模 React 应用中管理状态的最佳组合。然而，随着时间的推移，Redux 的受欢迎程度下降，原因是：

- 配置 Redux Store 并不简单。
- 我们需要几个软件包来使 Redux 与 React 一起工作。
- Redux 需要太多样板代码。

带着这些问题，Redux 的创建者**Dan Abramov**发表了名为[《你可能不需要 Redux》](https://medium.com/@dan_abramov/you-might-not-need-redux-be46360cf367)的文章，建议人们只在需要的时候使用 Redux，而在开发不那么复杂的应用时，要遵循其他方法。

## Redux Toolkit 解决的问题

Redux Toolkit(之前称为 Redux Starter Kit)提供了一些选项来配置全局 store，并通过尽可能地抽象 Redux API 来更精简地创建动作和 reducers。

## 它包括什么？

Redux Toolkit 附带了一些有用的软件包，例如 Immer，Redux-Thunk 和 Reselect。它使 React 开发人员的工作变得更加轻松，允许他们直接更改状态（不处理不可变性），并应用 Thunk 之类的中间件（处理异步操作）。它还使用了 Redux 的一个简单的“选择器”库 Reselect 来简化 reducer 函数。

![ReduxToolkit依赖项](http://myimgcloud.oss-cn-hangzhou.aliyuncs.com/202103/redux-toolkit/1.png)

## Redux Toolkit API 的主要功能？

以下是 Redux Took Kit 使用的 API 函数，它是现有 Redux API 函数的抽象。这些函数并没有改变 Redux 的流程，只是以更易读和管理的方式简化了它们。

- **configureStore**：像从 Redux 中创建原始的 createStore 一样创建一个 Redux store 实例，但接受一个命名的选项对象并自动设置 Redux DevTools 扩展。
- **createAction**：接受一个 Action 类型字符串，并返回一个使用该类型的 Action 创建函数。
- **createReducer**：接受初始状态值和 Action 类型的查找表到 reducer 函数，并创建一个处理所有 Action 类型的 reducer。
- **createSlice**：接受一个初始状态和一个带有 reducer 名称和函数的查找表，并自动生成 action creator 函数、action 类型字符串和一个 reducer 函数。

您可以使用上述 API 简化 Redux 中的样板代码，尤其是使用**createAction**和**createReducer**方法。然而，这可以使用 createSlice 进一步简化，它可以自动生成 action creator 和 reducer 函数。

### createSlice 有什么特别之处？

它是一个生成存储片的助手函数。它接受片的名称、初始状态和 reducer 函数来返回 reducer、action 类型和 action creators。

首先，让我们看看在传统的 React-Redux 应用程序中 reducers 和 actions 的样子。

**Actions**

```react
import {GET_USERS,CREATE_USER,DELETE_USER} from "../constant/constants";
export const GetUsers = (data) => (dispatch) => {
 dispatch({
  type: GET_USERS,
  payload: data,
 });
};
export const CreateUser = (data) => (dispatch) => {
 dispatch({
  type: CREATE_USER,
  payload: data,
 });
};
export const DeleteUser = (data) => (dispatch) => {
 dispatch({
  type: DELETE_USER,
  payload: data,
 });
};
```

**Reducers**

```react
import {GET_USERS,CREATE_USER,DELETE_USER} from "../constant/constants";
const initialState = {
 errorMessage: "",
 loading: false,
 users:[]
};
const UserReducer = (state = initialState, { payload }) => {
switch (type) {
 case GET_USERS:
  return { ...state, users: payload, loading: false };
case CREATE_USER:
  return { ...state, users: [payload,...state.users],
 loading: false };
case DELETE_USER:
  return { ...state,
  users: state.users.filter((user) => user.id !== payload.id),
, loading: false };
default:
  return state;
 }
};
export default UserReducer;
```

现在，让我们看看如何使用**createSlice**简化并实现相同的功能。

```react
import { createSlice } from '@reduxjs/toolkit';
export const initialState = {
  users: [],
  loading: false,
  error: false,
};
const userSlice = createSlice({
  name: 'user',
  initialState,
  reducers: {
    getUser: (state, action) => {
      state.users = action.payload;
      state.loading = true;
      state.error = false;
    },
    createUser: (state, action) => {
      state.users.unshift(action.payload);
      state.loading = false;
    },
    deleteUser: (state, action) => {
      state.users.filter((user) => user.id !== action.payload.id);
      state.loading = false;
    },
  },
});
export const { createUser, deleteUser, getUser } = userSlice.actions;
export default userSlice.reducer;
```

正如你所看到的，现在所有的动作和 reducer 都在一个简单的地方，而在传统的 redux 应用中，你需要在 reducer 中管理每一个 action 和它对应的 action，当使用 createSlice 时，你不需要使用开关来识别 action。

当涉及到突变状态时，一个典型的 Redux 流程会抛出错误，你将需要特殊的 JavaScript 策略，如 spread operator 和 Object assign 来克服它们。由于 Redux Toolkit 使用 Immer，因此您不必担心会改变状态。由于 slice 创建了**actions**和**reducers**，你可以导出它们，并在你的组件和 Store 中使用它们来配置 Redux，而无需为**actions**和**reducers**建立单独的文件和目录，如下所示。

```react
import { configureStore } from "@reduxjs/toolkit";
import userSlice from "./features/user/userSlice";
export default configureStore({
 reducer: {
  user: userSlice,
 },
});
```

这个存储可以通过使用**useSelector**和**useDispatch**的 redux api 直接从组件中使用。请注意，您不必使用任何常量来标识操作或使用任何类型。

## 处理异步 Redux 流

为了处理异步动作，Redux Toolkit 提供了一个特殊的 API 方法，称为**createAsyncThunk**，它接受一个字符串标识符和一个 payload 创建者回调，执行实际的异步逻辑，并返回一个 Promise，该 Promise 将根据你返回的 Promise 处理相关动作的调度，以及你的 reducers 中可以处理的 action 类型。

```react
import axios from "axios";
import { createAsyncThunk } from "@reduxjs/toolkit";
export const GetPosts = createAsyncThunk(
"post/getPosts", async () => await axios.get(`${BASE_URL}/posts`)
);
export const CreatePost = createAsyncThunk(
"post/createPost",async (post) => await axios.post(`${BASE_URL}/post`, post)
);
```

与传统的数据流不同，由**createAsyncThunk**处理的 action 将由分片内的**extraReducers**部分处理。

```react
import { createSlice } from "@reduxjs/toolkit";
import { GetPosts, CreatePost } from "../../services";
export const initialState = {
  posts: [],
  loading: false,
  error: null,
};
export const postSlice = createSlice({
name: "post",
initialState: initialState,
extraReducers: {
   [GetPosts.fulfilled]: (state, action) => {
     state.posts = action.payload.data;
   },
   [GetPosts.rejected]: (state, action) => {
    state.posts = [];
   },
   [CreatePost.fulfilled]: (state, action) => {
   state.posts.unshift(action.payload.data);
   },
 },
});
export default postSlice.reducer;
```

请注意，在**extraReducers**内部，您可以处理已解决（**fulfilled**）和已拒绝（**rejected**）状态。

通过这些代码片段，您可以看到此工具包在 Redux 中简化代码的效果如何。我创建了一个[利用 Redux Toolkit 的 REST 示例](https://github.com/LMPerera/redux-toolkit)供您参考。

## 最后的想法

根据我的经验，当开始使用 Redux 时，Redux Toolkit 是一个很好的选择。它简化了代码，并通过减少模板代码来帮助管理 Redux 状态。

最后，就像 Redux 一样，Redux Toolkit 并非仅为 React 构建。我们可以将其与其他任何框架（例如 Angular）一起使用。

您可以通过参考[Redux Toolkit 的文档](https://redux-toolkit.js.org/)找到更多信息。

谢谢您的阅读！
