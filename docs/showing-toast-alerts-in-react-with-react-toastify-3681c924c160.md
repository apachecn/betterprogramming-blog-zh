# 在 React 中显示吐司提醒，带有“react-toastify”

> 原文：<https://betterprogramming.pub/showing-toast-alerts-in-react-with-react-toastify-3681c924c160>

## 构建响应迅速的 web 应用

![](img/f12635850f87f652f00c4a4c29f216d6.png)

照片由 [Eiliv-Sonas Aceron](https://unsplash.com/@shootdelicious?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

# 介绍

在任何 web 应用程序中，我们经常需要通知用户某个操作是成功还是失败。这一功能通常是通过在屏幕顶部显示用户警报或通知来实现的，几秒钟后这些警报或通知就会消失。这些警告被称为“祝酒词”,在开发人员设置的超时后会自动消失。

Android/Material 设计框架引入了这一概念，但在过去几年中，它已经发展成为 web 和移动领域的标准。

因此，在这里，我将介绍一种使用 [react-toastify](https://www.npmjs.com/package/react-toastify) 库在 React 项目中实现它们的简单方法。

# 创建项目

由于我是 TypeScript 的忠实粉丝(这一选择背后的原因超出了本文的范围)，我将使用 TypeScript 模板生成一个 React 项目。然而，相同的原则和模式可以在任何使用 JavaScript 或 TypeScript 的 React 项目中原样使用。因此，我在终端上执行的第一个命令如下:

```
npx create-react-app react-toasts --template typescript
```

然后我们安装 react-toastsify，我们将使用这个库来实现 toast。

```
yarn add react-toastify
```

# 展示祝酒词

既然项目已经初始化并且安装了依赖项，我们从位于`App.tsx`文件顶部的 react-toastify 库中导入所需的元素。然后我们添加一个按钮，并将`onClick`属性设置为一个显示吐司的函数(`notifyDefault`)。

您的项目的`App.tsx`文件现在应该是这样的:

App.tsx 文件

请注意，这将显示默认的 toast，因为我们没有使用任何参数来设置 toast 的样式。

# 设定吐司的样式并设置超时

react-toastify 允许我们格式化许多东西。最重要的包括警报显示的位置，警报解除后的超时，主题的外观(亮、暗、彩色)等等。

这可以通过在`ToastContainer`元素中设置相应属性的值或者将这些参数作为`**toast**` 函数的第二个参数来实现。在第一种情况下，所有警报都将继承`ToastContainer`的属性，而在第二种情况下，每个警报都可以独立定制。您可以在 react-toastify 库的[文档中了解更多关于格式化选项的信息。](https://fkhadra.github.io/react-toastify/introduction/)

在下面的例子中，我们将研究为每个警报设置不同的`toast`参数的另一种方法。

如您所见，我们添加了另外三个按钮，分别触发三个新功能。我们对这三个函数分别调用 toast 函数(第 8–10 行)。我们传递一个包含格式化参数的对象，比如关闭超时(autoclose)、位置、进度条可见性等。

你可以在 GitHub 上克隆[最终项目](https://github.com/petrosDemetrakopoulos/react-toasts)来试试。

# 就这样

我希望你会觉得有趣，并且在你未来的项目中有用！你可以在这个 [GitHub 库](https://github.com/petrosDemetrakopoulos/react-toasts)上找到完整的项目。

**如果这篇文章有帮助，请点击拍手👏按钮几下，以示你对作者的支持👇**