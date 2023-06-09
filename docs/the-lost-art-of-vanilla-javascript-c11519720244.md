# 遗失的香草 JavaScript 艺术

> 原文：<https://betterprogramming.pub/the-lost-art-of-vanilla-javascript-c11519720244>

## 当编程变成将库粘合在一起时

![](img/37822894d4bac9914c6c8494e7222eda.png)

照片由[乔纳·佩特里奇](https://unsplash.com/@jonah_jpg?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

几个月前，我为一家科技公司做了一个关于 Web 组件的演讲，大多数与会者都没有使用过 Web 组件。这是一个非常有趣和受欢迎的演讲，以听众持续半个多小时的一轮提问结束。

在接近尾声时，有人问我这是否意味着我们要回到他所谓的“类似 jQuery 的意大利面条代码”的时代，因为我已经展示了原始的 DOM 操作代码。我可以看到其他一些人点头表示同意，这种反应真的让我思考。

显然，不属于某个框架或库的一部分，而只是我们称之为“香草 JavaScript”的代码被认为是糟糕的“意大利面条代码”这甚至不应该是一个惊喜。

整整一代开发人员开始使用框架和初学者工具包，这些框架和工具包带有一大堆现成的依赖项。只需运行`npm install`并根据您的需要调整代码模板。

虽然这可能是一个很好的起点，但我们确实需要认识到编程不仅仅是将第三方库粘在一起。

# 我不反对依赖

让我先说明一下，我并不提倡使用依赖关系。事实上，我对多种前端框架颇有经验。

然而，我鼓励你在考虑使用哪一个之前，问问自己下一个项目是否真的需要这些依赖。

这是一个非常重要的问题，要问自己。我觉得这个问题问得不够多，如果有人问的话。

# “不要重新发明轮子”的谬论

你可能已经听过很多次了。作为一名开发人员，你不应该“重新发明轮子”，相反，你应该使用一个已经在做你试图构建的事情的库。

例如，当您需要一个数据库 ORM 时，这是一个很好的建议，因为当已经有好的 ORM 可用时，从头开始编写它是没有意义的。

**问题是:当我们决定自己编码什么以及何时使用第三方依赖时，我们在哪里划线？**

2016 年， [left-pad](https://github.com/left-pad/left-pad) 包的开发者因为一个法律问题决定从 NPM 移除他所有的包，他[这样做几乎破坏了互联网](https://www.theregister.co.uk/2016/03/23/npm_left_pad_chaos/)。这说明了对第三方软件的依赖已经变得多么疯狂。

左填充的唯一工作是用零或空格填充字符串的左边。这个包只包含几行代码，初级开发人员也可以用几行代码编写它。事实上，它已经被弃用，取而代之的是标准`String.prototype.padStart()`，然而数以千计的项目依赖于它。

更糟糕的是。包 [isarray](https://github.com/juliangruber/isarray) 只有四行代码，它检查给定的参数是否是一个数组。当然，这是为不支持本机`Array.isArray()`方法的老浏览器准备的，但是这可以由任何开发人员用一行代码编写。

编码自己并不是“重新发明轮子”在您的项目中，确实没有必要将它作为一个依赖项。

# 我们不知道我们在用什么

尽管如此，人们仍然认为最好使用已经可用的第三方模块——即使它们非常小——因为这些模块已经过“战斗测试”,是“成熟的解决方案”

问题是:你确定吗？

你*知道*依赖是一个经过充分测试和证明的解决方案吗，或者你只是*假设*它是？

你真的知道你的`node_modules`文件夹里的所有东西吗？

我不知道，可能也没人知道。老实说，您不必这样做，但至少您应该考虑将什么作为依赖项安装，以及自己编写什么代码。

当每个现代浏览器中都已经有了`fetch`时，你真的需要一个臃肿的库来进行简单的 HTTP 调用吗？或者是一个大型的数据解析库，你可以用几行代码自己编写，并且*只*你*真正*需要的？

似乎整整一代开发人员都太不安全了，以至于不能编写和使用他们自己的代码，相反，他们求助于将库粘合在一起，他们*认为*经过了良好的测试并且没有错误。

但这是一种虚假的安全感。

# 我们怎么会变得如此缺乏安全感？

为什么我们作为开发人员如此害怕相信我们自己的代码，而宁愿使用我们认为更好更安全的其他人的代码？

毕竟这些在 Github 上发布自己开源代码的人也是开发者，和我们一样。它们也不是完美的，它们也可能写出有缺陷的代码。然而，我们选择相信他们，而不是自己。

我理解你不想写一个像 RxJS 这样的库或者自己反应，比如。这些大型库已经存在多年，有许多贡献者，并且经过了良好的测试。我不建议重新实现这些[，除非你想从中学习](https://medium.com/better-programming/why-coding-your-own-makes-you-a-better-developer-5c53439c5e4a?source=friends_link&sk=86f893a87f23d1e87ac699e68e87b896)。

但是对于较小的功能，您可以轻松地自己编写代码并完全适应自己的需求，我建议您自己编写。至少你可以在需要的时候轻松修改和修复。

我猜开发人员编写自己的代码的不安全感来自于多年来使用的库和框架，这些库和框架对我们隐藏了原生 JavaScript 平台。这已经从 jQuery 开始，随着 Angular 和 React 等框架的出现变得更糟。

这些抽象本质上被认为是复杂的。正因为如此，开发人员倾向于使用这些作为黑盒。

再说一次，我不是反框架的，我确实看到了它们带来的价值。但是现在，似乎有整整一代开发人员只知道如何使用框架编程，而对底层平台知之甚少甚至一无所知。他们不知道如何使用原始 DOM，因为他们几乎没有接触过它。

我已经参与了几个常青树项目，其中第一个问题是*我们应该使用哪个*框架，而不是*如果*我们应该使用一个的话。默认的假设是需要一个框架，主要的论点是框架将提供我们需要的一切，我们不应该重新发明轮子。

具有讽刺意味的是，这些人中的许多人没有足够的本地平台知识来判断所选择的框架是否是一个好的选择。

可以理解的是，这些框架的复杂性令人生畏，尤其是对于初学者。他们会倾向于把它们当作黑匣子来使用，而不了解它们内部是如何工作的。

框架也不鼓励这样做，因为它们的工作是通过隐藏底层细节和 API 使编程更容易开始。

这就产生了对框架的依赖，而这反过来又无助于给开发人员自己编码的信心。

# 使用依赖关系就是外包你的业务

当你在应用中使用依赖时，你基本上是把这部分外包给了其他开发者，所以你最好确定那个人做得很好。

如果你有一家公司，你把客户服务外包给其他公司，如果他们搞砸了，你就有大麻烦了。如果他们对什么是好的客户服务有不同的看法呢？

您可能不会让这种情况发生，您应该以同样的方式对待应用程序中的依赖关系。这并不意味着您需要阅读所有依赖项的源代码，但至少您应该对它是如何工作的有一个坚实的概念。

# 了解基础

但是要做到这一点，您首先应该对普通 JavaScript 编程有一个坚实的理解，因为这是所有编程的基础。

前端框架肯定会带来价值，但也会带来复杂性和开销。我经历过学习和实现几个框架的痛苦，我可以告诉你，你需要确保努力是值得的。

没有理由不使用原生平台，你会惊讶地看到[如今它所提供的](https://itnext.io/using-the-dom-like-a-pro-163a6c552eba?source=friends_link&sk=3e723a0f3c548d9b4b7c11d354a26659)。

不要人云亦云，说你不应该重新发明轮子。首先学习基础，然后*再*决定你是否真的需要那个库或框架。

你会成为更好的开发者。