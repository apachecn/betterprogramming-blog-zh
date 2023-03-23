# 使用 Jetpack Compose 构建带有滚动条的 LazyColumn

> 原文：<https://betterprogramming.pub/lazycolumn-with-a-scrollbar-b1a9ee3df4a0>

## 当你想要一个可怕的滚动体验

![](img/79d96f36b84275e597e07cb691ed5c6c.png)

[泰勒·威尔科克斯](https://unsplash.com/@taypaigey?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/scroll?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

# 镇上的一个新图书馆

有一个名为 [LazyColumns](https://github.com/BILLyTheLiTTle/LazyColumns) 的新库，它提供了一个`LazyColumn`的变体。假设他们正在服用类固醇。

要将它添加到您的项目中，您应该将以下内容添加到您的项目中`build.gradle`或`settings.gradle`，这取决于您的项目使用的 Gradle 版本。

然后在你的应用程序模块的`build.gradle`中添加下面的依赖项。

# 如何使用

将库依赖项添加到项目中后，您只需调用下面的`Composable`函数，如下所示:

使用的参数与常规的`LazyColumn`实现中使用的参数相同，除了`settings`参数影响滚动条的外观&感觉。

# 代码示例

您可以在下面的示例中看到上述`Composable`函数的真实用例:

正如我上面提到的，参数真的很简单(就像一个常规的`LazyColumn`)。在这里我们渲染了`data`列表，并给`LazyColumn`一个`500.dp`高度。

# 结果呢

上面的代码很好，但是当上面的代码被执行时，人们应该期待什么呢？

# 需要更多代码？

如果你想看看幕后发生了什么，为这个项目做点贡献，或者在你可以自由克隆的单个项目中看到以上所有内容，这里有到官方 LazyColumns GitHub 页面的链接。