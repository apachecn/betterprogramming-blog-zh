# 想提高代码可读性？—这里有 5 条规则

> 原文：<https://betterprogramming.pub/5-rules-to-improve-code-readability-83eda50ca780>

## 代码可读性是你的应用程序的一个特性(即使你的用户看不到)

![](img/c94661664df194b2ff54e32e765fd940.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上由 [Jesus Kiteque](https://unsplash.com/@jesuskiteque?utm_source=medium&utm_medium=referral) 拍摄的照片。

我有五条规则来提高代码的可读性。我在各种项目、团队和组织中都学到了它们。我希望你能从这篇文章中挑选一些东西来提高你的代码可读性。

## TL；速度三角形定位法(dead reckoning)

对于那些没有阅读所有内容就想寻找快速提示的人，请阅读 TL；以下是灾难恢复版本:

1.  重复使用将被多次使用的东西。
2.  可读性和可维护性。
3.  让模块、类或组件尽可能小。
4.  为你的代码制定规则和指导方针。
5.  像在一个团队中一样编码——即使是一个人的团队。

## 1.重复使用将被多次使用的东西

大部分开发者都知道 D.R.Y .是什么意思(不重复)。D.R.Y 可以帮助您防止代码重复。

为什么要反复写一个函数？你应该写一次，然后在多个地方重复使用。如果您需要更改代码，您只需查看一个地方，而不是在多个地方复制粘贴一个 bug 修复。

但是请注意，您将会在 D.R.Y .中引入复杂性，因为最终，事情将会越来越多地被重用。

当您开始更改代码时，重用部分代码时编写测试的重要性将非常明显。

## 2.可读性和可维护性优于通用解决方案

可重用性、可读性和可维护性同时是彼此的朋友和敌人。当你开始将 D.R.Y .应用于你的代码时，你引入了复杂性。当你引入复杂性时，可读性会下降。

因此，在构建特性时，不要从通用的解决方案开始。从简单开始！第一次不可能完美。

您可以通过迭代重用部分应用程序，但仍然保持可读性和可维护性。

在有很多开发团队的组织中工作时，你的团队会分为内部人和外部人(比如自由职业者或顾问)。所以在这种情况下，人们会更频繁地从一个组织转换到另一个组织。

在这些情况下，可读性和可维护性是成功的关键。由一个很容易离开团队的人来实现通用的解决方案并不是一个明智的选择。

有时，您需要一个通用的解决方案，但是这些解决方案仍然必须是可读的和可维护的。

## 3.让模块、类或组件尽可能小

在为应用程序构建新特性时，您可能会仔细规划它们。

最好的解决方案可以分成小的模块、类或组件。你想知道为什么吗？

小段代码更容易测试和维护。

想象一下，建造一座高楼也是通过移动较小的组件来完成的，而不是一次性建造整个建筑，然后试图将其移动到位置。好吧，也有例外。

大多数现代的库和框架被分成更小的构建块，而不是一个文件。JavaScript 库和框架如 Angular、React 和 Vue 应用了组件的概念。我不认为他们这样做是偶然的。

## 4.自动化代码的规则和指导方针

编写可读和可维护的代码的一部分是它的架构。另一部分是代码风格。

许多人对使用制表符或空格进行缩进的讨论都很熟悉。不，我不打算继续那个讨论。无论你在团队中使用什么，确保每个人都清楚。

自动化这些代码风格规则和指南是最好的解决方案。很多 ide 都集成了这一点，或者可以通过插件安装。

跨多种语言和代码编辑器的最简单的一个是 [editorconfig](https://editorconfig.org/) 。通过添加一个`.editorconfig`，这些规则将被应用。

您可以在这些文件中为您的项目设置许多设置。可以对特定语言进行全局设置。例如:

*   缩进样式:制表符或空格
*   报价类型:单一或双重
*   最大长度
*   字符集
*   还有更多…

这是我的一个项目中的配置:

```
# Editor configuration, see [https://editorconfig.org](https://editorconfig.org)
root = true[*]
charset = utf-8
indent_style = space
indent_size = 2
insert_final_newline = true
trim_trailing_whitespace = true[*.ts]
quote_type = single[*.md]
max_line_length = off
trim_trailing_whitespace = false
```

还有很多特定于特定语言的工具。我喜欢用更漂亮的 JavaScript，但是你可能喜欢用不同的东西。但是，只要参与项目的每个人都按照相同的规则和指导方针工作，您使用哪一个并不重要。

## 5.像在一个团队中一样编码——即使是一个人的团队

最后但同样重要的是，像团队一样写作！

我可以想象，对于从未在团队中编写过代码的人来说，很难理解那是什么感觉。

但是如果你自己编写一个项目，编写只有你自己理解的代码是非常诱人的(例如，编写不清楚的变量名，使用 2-3 个字符的变量名，等等。).

试着像在团队中一样编写代码。想象一下，你的代码非常清晰，别人可以很容易地理解你的代码。

你可以通过 Twitter 上的朋友或开发人员社区中的人来检查你的代码的可读性，从而很容易地测试这一点。我可以保证你会得到你从未想过的反馈。

不要对负面反馈感到恐慌！只需关注那些能让别人读懂你的代码的反馈。

你应该知道可读代码和不可读代码之间只有一线之隔。这是基于一个人的看法。如果有人告诉你你的代码不可读，不要难过！感谢反馈。

# 谢谢！

![](img/788dd57432ef08bda37f970c71ad90b8.png)

读完这个故事后，我希望你学到了一些新的东西，或者受到启发去创造一些新的东西！🤗

如果我给你留下了问题或一些要说的话作为回应，向下滚动并给我键入一条消息。如果你想保密，请在 Twitter @DevByRayRay 上给我发一条 [DM。我的 DM 永远是开放的😁](https://twitter.com/@devbyrayray)

## [通过电子邮件获取我的文章点击这里](https://byrayray.medium.com/subscribe) | [购买 5 美元的中级会员](https://byrayray.medium.com/subscribe)

# 阅读更多

![RayRay](img/992af170033696163d6cc0269218aedd.png)

[雷雷](https://byrayray.medium.com/?source=post_page-----83eda50ca780--------------------------------)

## 荒诞的故事

[View list](https://byrayray.medium.com/list/angular-stories-24674407532a?source=post_page-----83eda50ca780--------------------------------)6 stories![](img/b94f2b7d2929c90566cd2dd6f657a751.png)![](img/02b73423a62d73b113af9fdf9629c79f.png)![Stacked books](img/b02a2f57c0093e04ab1d11d3a55f35ea.png)![RayRay](img/992af170033696163d6cc0269218aedd.png)

[雷雷](https://byrayray.medium.com/?source=post_page-----83eda50ca780--------------------------------)

## 最新的 JavaScript 和 TypeScript 故事

[View list](https://byrayray.medium.com/list/latest-javascript-typescript-stories-0358ad941491?source=post_page-----83eda50ca780--------------------------------)14 stories![](img/c93ca03b33796c40dcc47873de2697c2.png)![](img/86f37efa11855f6f0f0f62984c37f696.png)![](img/ddbaa6d0bea676316247e82043d60b63.png)