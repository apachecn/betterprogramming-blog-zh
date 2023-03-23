# 利斯科夫替代原理

> 原文：<https://betterprogramming.pub/solid-swift-by-examples-part-three-675672c1ec20>

## Swift 实例中的坚实原则

## 坚固的雨燕

![](img/da4a77e544389e799ff3941215950c25.png)

照片由[菲尔在](https://unsplash.com/@kingslayer77?utm_source=medium&utm_medium=referral) [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上听

这是我关于*坚实*原则系列的第三部分。在以前的文章中，我们讨论了什么是坚实的原则，它们试图解决什么，以及什么是 SRP(I)和 OCP(II)。

你可以在这里看到之前的两件作品:

[](https://medium.com/@piero9212/solid-swift-by-examples-part-one-35018d53d3e6) [## 坚固的雨燕

### 完整指南第一部分

medium.com](https://medium.com/@piero9212/solid-swift-by-examples-part-one-35018d53d3e6) [](https://medium.com/@piero9212/solid-swift-by-examples-part-two-82ac3c457e4e) [## 开闭原理

### 在第一部分，我们描述了什么是坚实的原则和他们试图解决什么，解释了第一…

medium.com](https://medium.com/@piero9212/solid-swift-by-examples-part-two-82ac3c457e4e) 

在这一部分，我们将探讨利斯科夫替换。由 Barbara Liskov 在 1987 年的会议主旨中介绍，后来在 1994 年与 Jannette Wing 一起发表在一篇论文中。

他们最初的定义是:

> "使用指向基类的指针或引用的函数必须能够在不知道的情况下使用派生类的对象."

然后，随着 Robert C . Martin(Bob 叔叔)在他的《敏捷软件开发、原则、模式和实践*一书中出版了《坚实的原则》一书，并在该书的 [C#](https://docs.microsoft.com/en-us/dotnet/csharp/) 版本 [*敏捷原则、模式和实践【C#*](http://www.amazon.com/Agile-Principles-Patterns-Practices-C/dp/0131857258) 中重新出版，该定义被称为利斯科夫替代原则。*

*这就引出了罗伯特·马丁给出的定义:*

> *"子类型必须可以替换它们的基本类型."*

*就这么简单——子类应该在不破坏应用程序或功能的情况下替换它的超类，但这要求所有子类的行为都与父类相同。*

*为了实现这一点，你的子类需要验证另外两点:*前置条件*和*后置条件*变化。*

*   *不要在输入参数上实现比父类实现的更严格的验证规则。*
*   *将至少与父类相同的规则应用于所有输出参数。*

*有很多关于 Liskov 替换原则的文章，使用相同的一个`Rectangle`和一个`Square`类的例子来展示如果你的`Square`类扩展了`Rectangle`类，你将如何破坏设计原则。*

*我想用一个真实的例子来解释这个概念。*

*让我们看看下面的例子。假设我们必须从一个 API 或一个数据库中获取用户信息，并且您做出了类似这样的事情:*

*从 API 和数据库获取数据的用户服务*

*应用我们在以前的出版物(SRP 和 OCP)中学到的一些概念，我们做了这个:*

*两个特定服务，一个用于 API，另一个用于数据库*

*并且，我们有一个`UserManager`接收一个`UserService`:*

*处理所有用户逻辑的 UserManager 类*

*当我们从数据库中调用`UserManager`、`fetchUser`时:*

```
*let databaseService = DatabaseUserService()let userManager = UserManager(service: databaseService)userManager.fetchUser(with: “thebatman”, password: “afflect8”)//PRINTED RESULT **DB REQUEST HERE***
```

*从一个 API:*

```
*let apiService = ApiUserService()userManager = UserManager(service: apiService)userManager.fetchUser(with: “thebatman”, password: “afflect8”)//PRINTED RESULT **API REQUEST HERE***
```

*但是这怎么可能呢？*

*`UserManager`需要一个`UserService`，而不是一个`APIUserService`或`DatabaseUserService.`*

*这就是利斯科夫替代原理在起作用。我们用一个子类型替换一个`UserService`，比如`APIUserService.`*

*现在是时候确定我们如何能够*打破***或者我们如何*一直在打破***这个原则了。*****

# *****强化前提条件*****

*****假设我们需要更改业务逻辑，并且只有当密码长度大于`5`时，您才必须请求用户信息:*****

*****APIUserService*****

*****这个例子中断了 LSP，因为这个子类有一个先决条件，即`password`的长度必须大于`5`。*****

*****`UserManager` (client)的客户不期望`APIUserService`有不同的前提条件，因为对于**所有的**和`UserService`子类都应该是一样的。*****

*****我们可以用不同的方法来解决这个问题，但这里最简单的方法是将验证转移到我们的客户端，并在它的调用中添加一个新参数，以注入密码所需的最小字符长度:*****

*****一个拥有避免破坏原则的验证规则的用户管理器*****

# *****弱化后置条件*****

*****假设我们有这个`User`T21 和`Contact`子类:*****

*****一个包含名字和姓氏的简单用户类*****

*****我们在客户端向用户展示`UserView`:*****

*****让我们来试试:*****

*****在操场上测试我们的代码*****

*****但是如果我们在客户端修改`firstName`参数会发生什么呢？*****

*****修改输入的用户视图*****

*****在操场上再次测试*****

*****通过这种方法，我们打破了`init`上的`Contact`类的 LSP 后置条件，将`firstName`设置为“联系人”*****

# *****需要特定类型*****

*****假设我们必须获取用户的照片:*****

*****再次是获取用户照片的用户服务*****

*****我们已经将这个新功能添加到我们的`APIUserService`中:*****

*****APIUserService 实现*****

*****但是，如果这种获取用户照片的方式只通过 API 支持，而不通过数据库支持呢？*****

*****数据库用户服务实现*****

*****如果一个覆盖方法什么都不做，或者只是抛出一个异常，那么您可能违反了 LSP。*****

*****这是打破利斯科夫替代原理的一种常见方式。*****

*****让我们用构图来解决这个问题:*****

*****作文来帮助我们*****

# *****结论*****

*****请注意，如果您将`UserPhotoService`属性更改为使用`APIUserService`或`DatabaseUserService`，一切仍将相同，这就是 LSP。*****

*****编译器非常善于允许我们编写符合 Liskov 替换原则的代码，所以要好好利用它。*****

*****此外，在处理子类型时，请注意前置条件和后置条件。有时我们会想到一个亚型，但它可能是不同的东西。*****

*****我希望你喜欢这篇文章，希望它对你有所帮助。感谢阅读。请在下面的评论中留下您的反馈。*****

# *****下一章*****

*****[](https://medium.com/@piero9212/interface-segregation-principle-ec31bdb2872) [## 界面分离原理

### 这是关于坚实原则的出版物的第四部分。在以前的文章中，我们讨论了…

medium.com](https://medium.com/@piero9212/interface-segregation-principle-ec31bdb2872)***** 

# *****参考*****

*****[](https://medium.com/swift-india/solid-principles-part-3-liskov-substitution-principle-723e025d0589) [## LSP: Liskov 替代原理，又称协议设计

### 子类在代替基类使用时应该表现良好

medium.com](https://medium.com/swift-india/solid-principles-part-3-liskov-substitution-principle-723e025d0589) [](https://medium.com/@piyush.dez/solid-principles-in-swift-e9cc84ff5aa2) [## Swift 坚实的设计原则

### SOLID 代表了面向对象编程的 5 个原则:单一责任、开放/封闭、Liskov 替换…

medium.com](https://medium.com/@piyush.dez/solid-principles-in-swift-e9cc84ff5aa2) [](https://code.tutsplus.com/tutorials/solid-part-3-liskov-substitution-interface-segregation-principles--net-36710) [## 固体:第 3 部分-利斯科夫替代和界面分离原理

### 单一责任(SRP)、开放/封闭(OCP)、利斯科夫替代、界面分离和依赖性…

code.tutsplus.com](https://code.tutsplus.com/tutorials/solid-part-3-liskov-substitution-interface-segregation-principles--net-36710) [](https://stackify.com/solid-design-liskov-substitution-principle/) [## 坚实的设计原则解释:利斯科夫替代原则与代码示例

### 所有 5 个坚实的设计原则被广泛使用，如果你不知道他们的名字，你会很快认识到他们…

stackify.com](https://stackify.com/solid-design-liskov-substitution-principle/) 

[https://web . archive . org/web/20150905081111/http://www . object mentor . com/resources/articles/LSP . pdf](https://web.archive.org/web/20150905081111/http://www.objectmentor.com/resources/articles/lsp.pdf)*****