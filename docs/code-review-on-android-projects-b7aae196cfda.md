# Android 项目的代码审查清单

> 原文：<https://betterprogramming.pub/code-review-on-android-projects-b7aae196cfda>

## 审查 Android 代码库时要记住的几点

![](img/864b17de3402d61424dd88ea6020a2e3.png)

[拉拉·阿兹利](https://unsplash.com/@lazizli?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

代码审查有时是一个乏味的过程，但是我相信我们需要在这上面花更多的时间。也许是你学习或者分享一些知识的机会。

我列出了一些我认为在 Android 项目的代码审查过程中需要检查的要点。

# 内存泄漏

想象一下下面这个案例:*一个漂亮的 app 但是同时又很慢，屏幕之间的导航一次比一次慢。*

在代码评审过程中需要检查的几点:

*   这一段新代码有保留吗？
*   有没有 RxAndroid 相关的代码？如果是，检查 RxCalls 是否在生命周期范围(ViewModel/Fragment/Activity)的末尾被处理。
*   如果代码有协程，检查`Job`是否从`ViewModel` 作用域启动，以正确释放。
*   代码是一种使用`CountDownTimer`、AsyncTask 或任何视频/音频播放器的实现吗？如果是，代码应该释放与避免泄漏相关的内存资源。
*   是一个新的片段用`ViewBinding` 接近，代码应该在`onDestroyView`方法释放绑定。

参考:[https://developer.android.com/topic/libraries/view-binding](https://developer.android.com/topic/libraries/view-binding)

# 深度布局

有时候我们有一个`ViewGroup`小时候有另一个`ViewGroup`。

一个好的建议是总是使用 as`ViewGroup`*constraint layouts*来保持布局更加平坦*。它可以避免导致性能问题的透支。*

Android 文档中提到了一些[提示](https://developer.android.com/topic/performance/rendering/overdraw)来减少透支。

# 资源注释

Android 世界中的每个资源都有一个标识符，它是一个整数类型。如何确保我们的整数值代表一个有效的资源 id？注释在这方面对我们有帮助。

例如:

在上面的代码中，审查员可以建议:“你认为使用注释怎么样？大概是这样的:“(举个例子):

注释的其他例子:[此处](https://developer.android.com/studio/write/annotations)。

# 相似组件

在代码评审期间投入时间是非常重要的。试着像作者一样思考，反映他的选择，提出问题来理解代码。

新的代码要来了。*等等:*“这段代码做的和我们已经在这个组件中做的一样”。像这样的注释是必不可少的，因为重要的是重用我们代码库中已经存在的东西。当组件类似于以下内容时，请注意:

*   组件 X 将月亮颜色变为红色🔴；
*   组件 Z 将满月变为蓝色🔵；
*   组件 A 将月亮变成绿色🍏。

为什么没有一种成分可以改变任何月亮的颜色？它可以接收颜色作为参数。是不是工作量太大了？

# 危险代码区域

如果你有一个敏感代码——我喜欢这个表达*危险区*，它更好地描述了这种情况。危险代码是审核人看了之后给出类似于:*“这个代码没有任何意义”的反应的代码。*

在这种情况下，不要评价作者**。与他们交谈，了解代码背后的原因。这次谈话可能是一个学习新东西的机会。**

# 架构违规📐

软件架构是软件各部分之间定义的通信协议。

代码审查是识别诸如架构违规等问题的非常有效的工具。在这种情况下，添加这样的注释很有用:

*   "T10 朋友，您的 ViewModel 正在访问存储库。我们在它们之间有一个用例。请看看 MainViewModel 文件，这是一个很好的例子。
*   "*为什么在 ViewModel 中使用适配器引用？适配器是一个 RecyclerView 组件。最好将它放在一个片段中，并使它远离我们的视图模型。*

# 小细节决定大不同

*   未使用的进口产品；
*   未使用的资源，如抽屉、绳子、颜色…
*   注释代码；
*   未格式化的代码；
*   变量名，方法名，文件名，…
*   代码没有遵循样式指南。例如，Kotlin 定义了一个定义良好的[样式指南](https://kotlinlang.org/docs/coding-conventions.html)。对于任何开发人员来说，在现有代码库中快速找到任何软件组件都有很大帮助。

# 结论

静态分析工具在代码审查过程中是有帮助的，但是它们不是 100%有效的。如果你的团队追求代码质量，一个关键的开发人员评审是必不可少的。