# 使用 ACTION_PROCESS_TEXT 自定义文本选择

> 原文：<https://betterprogramming.pub/custom-text-selection-with-action-process-text-9c1cd9b24027>

## 使用意图过滤器的跨应用程序通信

![](img/991383f679f8d0292542be5aeedd631c.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上由 [Romain Vignes](https://unsplash.com/@rvignes?utm_source=medium&utm_medium=referral) 拍摄的照片。

# 什么是意图过滤器？

要理解`ACTION_PROCESS_TEXT`的基础，你应该知道什么是意图过滤器，以及它是如何工作的。所以让我们从头开始。

## 什么是意图？

意图是从另一个 Android 组件触发动作的基本通信块。通常，该功能缩小到三个基本用例:开始一项活动、一项服务或一次广播。

Android 有两种意图。

显式意图:这种类型的意图用于触发应用程序中的另一个组件，因为我们知道目标类名。

```
**val send** = Intent(**this**@MainActivity, SecondActivity::**class**.java)
startActivity(**send**)
```

隐含意图:这种类型的意图帮助我们导航我们不知道的组件。例如，要在外部浏览器中打开一个 URL，我们不知道浏览器应用程序的组件名。

```
**val send** = Intent()
**send**.action = **Intent**.ACTION_VIEW
**send**.data = **Uri**.parse(url)
startActivity(**send**)
```

所以这里我们要用`ACTION`。它只是一个字符串，指定要执行的一般操作。当你用`ACTION`触发一个意图时，支持`ACTIONS`的应用将会显示出来(一个典型的用例是共享意图)。

## 意图过滤器

到目前为止，我们已经从发送方的角度看到了一切。接收器呢？接收器应用程序如何支持`ACTIONS`？答案是意图过滤器。

在清单中声明一个 Android 组件时，我们可以使用一个`intent-filter`标签将该组件注册到一个或多个意图过滤器。据[安卓开发者](https://developer.android.com/guide/components/intents-filters):

> 每个意图过滤器基于意图的动作、数据和类别指定它接受的意图类型。只有当意图可以通过您的意图过滤器之一时，系统才会向您的应用程序组件传递隐式意图。”

看一看:

意图-清单文件中的过滤器用法

# 为什么需要 ACTION_PROCESS_TEXT？

从 Android 6.0 Marshmallow 开始，引入了一个新的浮动文本选择工具栏，使复制、粘贴和剪切等选项更贴近所选文本。

有通用选项固然很好，但是显示工具栏的必要应用也同样重要。例如，当用户选择一些文本时，显示翻译器作为选项之一。

显示未内置到系统中的应用程序不是由操作系统决定的。互动应该是双方的。这就是意图过滤器和`ACTION_PROCESS_TEXT`的用武之地。

# 什么是 ACTION_PROCESS_TEXT？

`ACTION_PROCESS_TEXT`只不过是在 API 级中引入的一个`ACTION`字符串。在清单文件中的 Android 组件上使用这个动作和`intent-filter`，将向系统发出信号，表明这个应用程序可以处理选择的文本。

因此，操作系统将在浮动文本选择工具栏中显示相应的应用程序。一旦用户选择了一个应用程序，一个隐含的意图就会被激发，其中`ACTION_PROCESS_TEXT`作为`ACTION`，所选的文本在附加部分，这样开发者就可以在各自的 Android 组件中检索它们。

理论说够了。让我们做一些编码。

# 意图过滤器设置

第一步应该是对您希望在浮动工具栏中显示的 Android 组件实现意图过滤器。这很简单。我们需要包含`intent-filter`标签，并将`ACTION`设置为`android.intent.action.PROCESS_TEXT`。

我们还可以设置 mime 类型，将您的应用程序限制在特定的类别中(例如，仅在选择文本时显示)。看一看:

文本处理意图过滤器

此时，如果您运行应用程序并在设备中的任意位置选择文本，您的应用程序将显示在浮动工具栏中。

# 获取选定的文本

现在我们已经在文本选择工具栏中显示了应用程序，下一步是处理输入数据。这很简单。我们只需要验证传入意图`ACTION`是否为`android.intent.action.PROCESS_TEXT`:

```
**if** (intent.**action** != null &&
        intent.**action**.equals(Intent.**ACTION_PROCESS_TEXT**) ){

}
```

如果是`true`，那么我们需要读取输入数据。默认情况下，读取输入数据的键是`Intent.EXTRA_PROCESS_TEXT`。这将返回`CharSequence`，我们需要将它转换成一个字符串，然后使用。看一看:

文本处理读取输入

# 更新结果

在某些情况下，我们需要发送回文本(例如，如果您的应用程序就像那些时髦的字体之一，那么您需要将输入数据转换成其各自的样式，并在源文件中替换它)。

为此，我们首先需要验证源是否可以观察到更新后的结果。为此，我们在 intent extras 中获得了另一个参数— `EXTRA_PROCESS_TEXT_READONLY`。看一看:

```
isReadOnly = intent.getBooleanExtra(
                 **Intent**.EXTRA_PROCESS_TEXT_READONLY, **false**)
```

如果这是`true`，那么源不能更新文本。如果是假的，那么我们可以通过`setResult`发送回处理过的文本。看一看:

发回已处理的文本

# 结论

如果开发者以正确的方式使用这样的功能(像在[维基百科](https://play.google.com/store/apps/details?id=org.wikipedia)和[谷歌翻译](https://play.google.com/store/apps/details?id=com.google.android.apps.translate))，这将提高 Android 的质量。另一方面，误用它会导致 Android 体验不佳。

不是每个应用程序都需要使用这种类型的跨应用程序通信，所以我建议您只有在 Android 的操作系统定制为最终用户增加价值的情况下才利用它。

目前就这些。希望你学到了有用的东西。感谢阅读！