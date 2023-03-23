# 教程:使用样式化组件和样式化系统的 React Native 中的黑暗模式

> 原文：<https://betterprogramming.pub/tutorial-dark-mode-in-react-native-with-styled-components-and-a-styled-system-1463ae2b606b>

![](img/1de1bbf81f22129c124c8b37387cec09.png)

Elliott Engelmann 在 [Unsplash](https://unsplash.com/s/photos/dark?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

到目前为止，我相信你已经注意到了席卷应用程序世界的最新趋势:我们知道并喜爱的许多公司，包括 Slack、Instagram、Medium 等等，现在都为用户提供了切换到深色主题的能力。苹果和谷歌也在各自的操作系统中加入了相同的功能。

因此，如果你曾经想在你的 React 原生应用中实现颜色模式，本指南将向你展示一种实现它的技术。

# 正在设置

我们需要做的第一件事是配置我们的主题。在我们的主题对象中，我们将在颜色对象中嵌套一个模式对象。这里需要注意的是，`dark`对象的形状和它上面定义的颜色是一样的。

我们还需要将我们的模式导出为一个字符串数组，这样我们就可以在它们之间循环。你可能注意到我们也没有一个`light`模式；这是因为我们会自动将顶层颜色分配给一个`default`模式。因为我们没有可以直接引用的默认模式对象，所以我们需要在开始时使用`unshift`手动添加它。现在你的主题对象应该看起来像这样:

# 配置上下文

接下来，如果你还没有，你会想从`styled-components`开始在`ThemeProvider`中包装你的应用。

既然我们的应用程序可以访问我们的主题，我们需要设置我们自己的上下文提供者来处理在主题之间切换的逻辑并记住用户的偏好:

这里没有什么太花哨的东西。我们只是创建了一个上下文提供者，它有两个道具， `mode` 和`toggleMode`。然后我们赋予`mode`一个默认值。

我们还导出了一个名为`useAppContext`的定制钩子，这纯粹是为了方便，这样我们就可以访问我们的 AppProvider 的上下文，而不必每次都引用`React.useContext(AppContext)`。

# 切换模式

正如我们在上面看到的，我们的 AppProvider 组件接受两个属性，`mode`和`toggleMode`——我们将在下面的 App.tsx 文件中定义它们。

现在，我们可以循环遍历我们之前创建的模式数组，并将该值设置为一个状态。

接下来我们要做的是根据当前的活动模式动态更新我们的主题对象。我们可以使用 Lodash 的一些辅助函数来实现这一点:

这项技术的伟大之处在于，你可以添加尽可能多的颜色模式，而不是管理两个独立的亮/暗主题对象，当你点击`toggleMode`时，它会在它们之间循环。

示例:

我们就要完成设置切换配色方案的逻辑了。我们仍然需要做的是将用户的首选项设置为异步存储，这样下次用户打开应用程序时，它会记住他们首选的颜色模式:

在我们的`toggleMode`函数所在的同一个 App.tsx 文件中，我们添加了两个`useEffect`钩子。

第一个在 mount 上运行(注意空的依赖数组),负责从 AsyncStorage 获取用户的首选项并将其设置为 state。

另一个用于在每次更新时将新模式设置为 AsyncStorage。就`colorModeStorage`实用程序而言，它只是一个包含一些 setter/getter 函数的对象:

现在我们都设置好了，我们需要做的最后一件事是给我们的应用程序添加一个按钮/开关，我们将使用它来循环模式。但是，如何做到这一点取决于您，超出了本教程的范围。

为了确保您的所有组件在切换颜色模式时反应良好，请确保始终使用您的主题中的颜色。因为我们使用的是[风格系统](https://styled-system.com/)，你应该像这样引用你主题的颜色:

```
<Box bg="primary" />
```

就是这样。您可以在下面看到完整的 App.tsx 文件:

顺便说一句，如果你在你的应用程序中使用`react-navigation`(我相信你们大多数人都是这样)，你可能已经注意到默认情况下你可能使用的任何组件——比如`Tabs`等。—不要自动切换颜色模式。

谢天谢地，`react-navigation`支持主题化——你只需要告诉你的导航器使用哪个主题。可以这样做:

一切都准备好了。

# 资源

非常感谢 Brent Jackson，他为他的色彩模式指南[撰写了](https://styled-system.com/guides/color-modes)[风格系统](https://styled-system.com/)。

这篇文章改编自他的 React 本地应用指南。