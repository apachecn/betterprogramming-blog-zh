# 在 React 中应用依赖倒置原则

> 原文：<https://betterprogramming.pub/apply-the-dependency-inversion-principle-in-react-c20a0afc3d64>

## 你的 React 组件知道的太多了。让我们解决这个问题

![](img/39f8d5df74fcfaa7b055686695557666.png)

照片由 [Pexels](https://www.pexels.com/photo/photo-of-keyboard-near-phone-3184453/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) 的[faxels](https://www.pexels.com/@fauxels?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)拍摄。

*依赖倒置*原则是著名的坚实原则之一。也是最重要的一条。

今天，我们将看到如何解决一个非常常见的错误，新手反应开发人员使用这一原则。

我会尽量保持简单。我们开始吧！

# 这个原理告诉我们什么？

就面向对象编程而言，这一原则背后的主要思想是始终有一个抽象的高级代码接口，而不是实现细节。

坚持住！我知道你在想什么:“我是一个简单的前端开发人员。你为什么用这些复杂的术语来烦我？”

让我为你简单地陈述它。对于 React 应用程序，这一原则意味着:

> "任何组件或功能都不应该关心一件特定的事情是如何完成的."

还不清楚？好了，让我们用一些代码来弄脏我们的手吧！

# 实际例子

我们举一个很常见的用例。我们将从我们的组件发出一个 API 调用，从远程数据源获取一些数据。实现可能如下所示:

用户. js

看这个组件。它依赖于从组件内部获取的一些远程数据。

我们的组件的主要职责是呈现数据。它不应该关心数据是如何获取的，也不应该关心数据来自哪里。

这个组件知道的太多了——这是个问题。

# 为什么？

好吧，让我们假设你有十个其他组件，它们都获取自己的数据。

现在你的经理走过来，告诉你使用`axios`而不是`fetch`

你有麻烦了！现在您必须进入每个文件并重构逻辑以使用`axios`。

但是生活并不是那么简单！几天后，您的经理又来了，告诉您实现缓存。

你必须再次做同样的事情。

因此，它增加了在你的软件中引入错误的机会。此外，代码变得不可维护，浪费了宝贵的时间。

# 那我们该怎么办？

让我们引入一个数据获取钩子，将我们的逻辑抽象到组件之外，因为这正是这个原则告诉我们的。依赖抽象，记得吗？

useFetch.ts

现在在我们的`Users`组件中使用这个钩子:

User.tsx

注意这个解决方案的一个伟大之处:你的`useFetch`钩子不关心谁在调用它。它只是将一个`URL`作为输入并返回数据。

现在，所有其他组件都可以利用我们刚刚编写的钩子。我们的`Users`组件不再依赖于数据如何返回或者哪个库正在被使用的具体细节！

# 更高级的用法

现在，让我们用基本的缓存功能来满足您的经理:

useFetch.ts

您现在只需要在一个地方更改代码。太好了！假设您需要将 API 错误显示为吐司。你现在能做到吗？如果是这样，那么你明白我的意思了。

# 如何检测

*   在大多数情况下，如果您违反了单一责任原则，那么您也可能违反了依赖倒置原则。
*   对于任何组件，查看顶部的导入部分。如果你正在导入一些不负责显示某些东西的库(比如吐司或者模态)，那么你可能违反了这个原则。

# 本系列的前几篇文章

1.  [单一责任原则](/how-to-apply-solid-principles-to-clean-your-code-in-react-cdfd5e0a9cea)
2.  [开启关闭原理](/applying-the-open-closed-principle-to-write-clean-react-components-4e4514963e40)
3.  [利斯科夫替代原理](/applying-the-liskov-substitution-principle-in-react-3a0614a42a08)
4.  [界面偏析原理](/how-to-apply-interface-segregation-principle-in-reactjs-fadf77113c5d)

今天到此为止。我希望你喜欢这篇文章以及这个系列。

**有话要说？通过**[**LinkedIn**](https://www.linkedin.com/in/56faisal/)联系我

[](/top-7-libraries-for-blazingly-fast-reactjs-applications-c0069e87c8b7) [## 快速反应应用的 7 大库

### 摇滚明星开发人员的必备工具

better 编程. pub](/top-7-libraries-for-blazingly-fast-reactjs-applications-c0069e87c8b7)