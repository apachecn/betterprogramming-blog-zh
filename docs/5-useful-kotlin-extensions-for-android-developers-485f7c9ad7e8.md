# Android 开发者的 5 个有用的 Kotlin 扩展

> 原文：<https://betterprogramming.pub/5-useful-kotlin-extensions-for-android-developers-485f7c9ad7e8>

## Kotlin extensions 将在构建 Android 应用程序时节省您的时间

![](img/8ced546aae3badf133fe888f425ffca3.png)

照片由[this is 工程](https://www.pexels.com/@thisisengineering/)在[像素](https://www.pexels.com/)上拍摄

# 什么是 Kotlin 扩展？

> Kotlin 提供了用新功能扩展类或接口的能力，而不必继承类或使用设计模式，如`*Decorator*`。这是通过称为*扩展*的特殊声明来完成的。

例如，您在代码中经常使用`String`类。您可能希望某些属性包含在`String class`中，但他们不会。在这种情况下，使用 Kotlin 扩展方法是可能的。

假设您想要一个具有一个属性的`String`来按字母顺序对字符串中的所有字符进行排序。让我们为此创建一个`Extension`函数。

我希望您了解扩展的确切功能以及它们的样子。

# 1.在更改的文本上编辑文本

EDITED[EDIT _ TEXT 的扩展名可能导致内存泄漏]

编辑文本更改是在移动应用程序中执行的关键任务。检查什么文本已经被改变。`EditText.onChange`文本的一个用例是在搜索栏中搜索某样东西，而不是在有 3-4 个字符时按下按钮开始搜索。当文本发生变化时，该函数将返回字符串中发生变化的文本。

# **2。显示烤面包片**

有了这个扩展函数，您可以在 Activities 或 Fragment 中的任何地方调用 toast，或者在您可以传递`Context`的任何地方调用 toast。你只需要调用`showToast(MESSAGE_YOU_WANT_TO_SHOW)`，默认情况下，吐司时长会是`Toast.LENGTH_SHORT`。

# 3.显示零食条

`Snackbar`用于在手机应用程序屏幕底部显示简短信息。它是烤面包的一种替代品。在`SnackBar`中，如果有要求，你可以有一个动作按钮。

# 4.检查互联网是否可用

当发出任何 API 请求时，比如上传文件，或者在我们的应用程序中从互联网获取任何类型的数据，我们需要确保互联网必须可用。如果互联网不可用，那么应用程序将会崩溃。

假设在一个场景中，您的 Android 项目中有多个活动和片段，您需要检查互联网在许多点上是否可用，然后您需要多次调用连接管理器，因为我在我的项目中使用了一个扩展函数。

它也会帮助你。

Internet 连接检查扩展

# 5.控制视图的可见性

在 Android 中，当我们想根据得到的数据显示或隐藏视图时。在某些情况下，我们希望隐藏任何特定视图的可见性。例如，使用`ProgressBar`我们希望只在一些数据正在从后端或本地源获取的过程中显示进度条。一旦获取了数据，我们只想让进度条不可见。

在 Android 中执行这个动作的一般惯例是`binding.loadingProgressBar = View.GONE`，它需要每次都执行。现在，我们可以简单地用`View`类创建一个扩展函数，它将满足我们的需求。

# **结论**

仅此而已！我希望你学到了一些东西。如有疑问，只管评论！我很乐意解决你的问题。

我为`PreferencesDataStore`创造了`Kotlin Extensions`。查看那篇文章，了解`DataStore`。

[](/using-jetpack-preferences-datastore-more-effectively-414e1126cff7) [## 如何使用 Jetpack 首选项数据存储

### SharedPreferences 的替换在这里

better 编程. pub](/using-jetpack-preferences-datastore-more-effectively-414e1126cff7) 

谢谢，干杯！