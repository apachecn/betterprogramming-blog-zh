# 如何构建离线优先的 React 应用程序

> 原文：<https://betterprogramming.pub/offline-first-react-app-b79ab1a17649>

## 用户不会总是有很好的连接，从一开始就做好准备

![](img/86b3c635d86033108c73a3fba0222758.png)

迈克尔·泽兹奇在 [Unsplash](https://unsplash.com/s/photos/patterns-tech?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

我为一个离线优先的 React 应用程序做了一个概念验证，并决定与开发者社区分享它。

假设我们正在开发一个 ReactJS 应用程序，即使出现网络问题，我们也不能承受 CRUD 操作的任何暂停。让我们看看如何实现这一点。我将使用 Redux 进行商店管理。

让我们从安装必要的库开始:

```
npm install redux-offline --save
```

这个[库](https://github.com/redux-offline/redux-offline)对于为 web 和 React-native 构建离线优先的应用程序来说非常方便。

然后安装`localforage`，因为我们将使用`[indexedDB](https://javascript.info/indexeddb)`而不是`[localStorage](https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage)`。

```
npm install localforage --save
```

我们将在本例中使用的其他库有:

```
- [redux](https://redux.js.org/)
- [redux-thunk](https://github.com/reduxjs/redux-thunk)
- [redux-logger](https://github.com/LogRocket/redux-logger)
- [react-redux](https://react-redux.js.org/)
```

通过单击这些库的名称来了解它们。

Redux store 会是这样的:

现在，我们将编写一个 React 组件来与 Redux 存储交互:

在`redux/actions/app.js`中:

在`redux/reducers/app.js`:

就是这样！

现在，当我们在 React 组件中创建的文本字段中插入一个项目名称后单击“Add”按钮时，它通过使用我们在操作中编写的`useDispatch`钩子向我们的本地 API ( `/api/sample`)发出一个`POST HTTP`请求来调度一个操作。Reducer 将采用初始状态，并基于该动作返回期望的结果——我们新添加的项目。

钩子`useSelector`将在我们组件中获得更新后的值。

记住:`useSelector`是`connect`的`mapStateToProps`的替代。您传递给它一个函数，该函数接受 Redux 存储状态并返回您需要的状态，然后`useDispatch`替换`connect`的`mapDispatchToProps`。它所做的只是返回你的商店的`dispatch`方法，这样你就可以手动分派动作。

如果互联网以某种方式断开或有网络问题，那么神奇开始了！

它将试图攻击我们的 API ( `/api/sample`)，当然，由于缺乏网络连接，它不能这样做。相反，它将开始与我们已经在商店中配置的`indexedDB`交互，并在特定的时间间隔(我们也可以设置时间间隔)后继续尝试访问 API。通过`POST HTTP`请求发布的所有数据将保存在`indexedDB`中。一旦网络恢复，它会将数据从`indexedDB`移动到我们的主`DB`，并将我们的主`DB`与`indexedDB`同步。

最终用户永远不会有互联网断开的印象，可以继续正常的 CRUD 操作。

是不是很酷？

一个高效的离线优先应用程序应该考虑以下几个方面:

*   当从服务器得到不正确的答复(例如，超时)时，尝试重新发送请求。
*   仅当我们检测到互联网连接或我们的 API 启动时发送请求。
*   动作排队。
*   在重新启动应用程序之间保持一个动作队列。

Redux offline 是一个完整的解决方案，它实现了上述功能，还允许您配置:

*   重新发送请求时服务器请求之间的时间间隔。
*   回滚前失败的请求数。
*   用于处理这些请求的库(例如，`axios`)。
*   队列实现。
*   改变配置。

然而，我们需要记住，如果用户无法以某种方式打开我们的页面，他将无法下载所有的逻辑，因此应用程序将不会加载。在 ReactNative 中就不是这样了，在 react native 中，我们可以在应用程序打开后立即访问应用程序逻辑。

我相信您现在已经完全准备好动手开发一个离线优先的 React 应用程序了！

回购完全可行的例子:[https://github.com/mansern/offline-first-demo](https://github.com/mansern/offline-first-demo)

你今天学到新东西了吗？评论和反馈总是让作者开心！

## 关于作者

*我在各种前沿技术领域拥有超过七年的专业软件开发经验，目前在硅谷的一家公司担任首席软件工程师，使用 ReactJs 和 Apollo-GraphQL 进行全栈开发。我喜欢写关于技术的文章，并与大家分享我的专业经验。*