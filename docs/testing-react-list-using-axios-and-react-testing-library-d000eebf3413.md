# 使用 axios 和 React 测试库测试 React 列表

> 原文：<https://betterprogramming.pub/testing-react-list-using-axios-and-react-testing-library-d000eebf3413>

## 看看我们如何测试一个异步列表

![](img/b1124e79e13510aec46b7dbda0382850.png)

杰西卡·刘易斯在 [Unsplash](https://unsplash.com/s/photos/list?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

典型的 web 应用程序肯定会有一个要呈现的列表。它可以是笔记本电脑、汽车、电影、财产、用户等的列表。今天，我们将看看如何测试一个异步列表。

我用 [Create React App](https://github.com/facebook/create-react-app) 启动了一个回购，下面是我用来做这项工作的三个额外的包:

```
"@testing-library/react": "^8.0.4""axios": "^0.19.0""jest-dom": "^3.5.0"
```

首先，我们有这个组件:

它只是调用一个 API，获取数据，并将用户的名字呈现给页面。当它正在抓取时，我们显示“正在加载…”

我们必须设置 [Jest](https://jestjs.io/) 来嘲弄 [axios](https://github.com/axios/axios) 。只需在 src 目录中创建一个文件夹`__mocks__`，我们用下面的代码将文件命名为`axios.js`。

接下来，我们必须用 Jest 设置`afterEach`清理，这样我们就不必在每个测试文件中重复这个步骤。

```
afterEach(cleanup)
```

在[文档](https://testing-library.com/docs/react-testing-library/setup#cleanup)中阅读更多关于`afterEach`清理的信息。

为此，在应用程序目录的第一层创建一个名为`jest.config.js`的文件。

现在我们处于测试阶段。创建一个名为`Users.spec.js`(或`Users.test.js`)的文件。

测试涵盖了我们的两个需求。在获取数据时检查加载器是否存在，并检查呈现的列表是否正确。

如果我们有更多的需求，例如，我们需要一个排序列表，用户的点击行为，等等。，最好把上面的测试拆分成多个测试块。

然而，我们需要确保我们使用 [mockClear](https://jestjs.io/docs/en/mock-function-api.html#mockfnmockclear) 重置了模拟，这样 API 就不会被调用一次以上。

为了在测试中看到行动或继续玩设置，这里的[回购](https://github.com/eldyvoon/react-testing-library-axios-react-hooks/)。

测试愉快！