# 轻松构建 URL 请求

> 原文：<https://betterprogramming.pub/building-urlrequests-with-ease-f0136cdd56c3>

## 在 iOS 中对 URLRequests 使用构建器模式

![](img/d0cf8b14a97c59f2bbfca550fb13cdb8.png)

[来源](https://undraw.co/search)

在本文中，我将向您展示一种简单的方法来简化开发人员在应用程序开发过程中必须执行的最长、最枯燥的任务之一。

如今，大多数应用程序都需要某种类型的网络。在 **iOS 中，**意味着我们要在某个时候和`URLRequest`职业战斗。这并不总是容易的，如果我们不小心的话，会导致各种各样的错误和问题。

首先，为了更好地理解这些例子，我们需要知道这些定义:

[https://gist . github . com/胡安佩/8c 902 c8 c 0 C5 f 342767 a67ee 62308 d5e 2](https://gist.github.com/Juanpe/8c902c8c0c5f342767a67ee62308d5e2)

假设我们有一个执行网络请求的 APIClient。这个类有一个构建 **URLRequests** 的方法。我们的代码可能是这样的:

[https://gist . github . com/胡安佩/58439 c 60 e 7d 63d 605 ad 06427 a6 fa 0 caf](https://gist.github.com/Juanpe/58439c60e7d63d605ad06427a6fa0caf)

正如您所看到的，这个方法非常长，而且也有不止一个责任。因此，我们增加了测试的复杂性，并且我们没有遵守 SRP 原则。

# 是建造者的时候了

使用`Builder`模式是一个完美的场景:

> **Builder** 是一个创造性的设计模式，让你一步一步地构造复杂的对象。该模式允许您使用相同的构造代码生成不同类型和表示的对象。

虽然我认为这很清楚，但基本上这种模式帮助我们以更简单的方式构建复杂的对象。

该模式将对象构造组织成一组步骤。要创建一个对象，您需要对一个构建器对象执行一系列这些步骤。重要的是你不需要调用所有的步骤。您可以只调用产生对象的特定配置所必需的那些步骤。

现在，我们知道了模式的定义，所以我们要创建我们的`URLRequestBuilder`:

[https://gist . github . com/胡安佩/e 8 b 3c 2d 5 a 17 a 14d 68 CCC 01d 79 aa 553 b](https://gist.github.com/Juanpe/e8b3cd2c5a17a14d68ccc01d79aa553b)

> 在每个方法之上有一个关键字`*discardableResult*`来避免警告未使用的结果。

这个类拥有我们创建一个`URLRequest`所需的所有属性，有些是可选的，有些是强制的。然后我们定义步骤，每个 set 方法就像是我们创建对象所需的一个步骤。

最后，我们有一个编译方法，它收集所有的信息并生成最终的对象。

多亏了 Swift，我们可以使用方法链接，所以方法看起来像这样:

[https://gist . github . com/胡安佩/6707 fc 371 d0a 496910 D1 a9 BD 008 c 29 e 4](https://gist.github.com/Juanpe/6707fc371d0a496910d1a9bd008c29e4)

结果是一个可读性更好的方法，只有 5 行代码和一个责任。所以，我们已经从我们的`APIClient`中分离出来，请求的创建。

# 奖金

很多时候，在发送请求之前，我们必须做一个或多个修改来满足 API 的需求。例如，如果我们使用 OAuth2 来认证请求。

使用 [**Alamofire**](https://github.com/Alamofire/Alamofire) 的方法，我们可以创建一个`RequestAdapter`。它的目标是接收请求，进行必要的修改，然后再次返回请求。

`RequestAdapter`将是:

[https://gist . github . com/胡安佩/d 953000 cc 55522 c0f 5 f 2312 CD 2 a1 d 662](https://gist.github.com/Juanpe/d953000cc55522c0f5f2312cd2a1d662)

以 **OAuth2** 为例，他的适配器应该是这样的:

[https://gist . github . com/胡安佩/e 877 f 96 aaac 1c 7 E1 c 122 DD 6 e 96 ca 80 c](https://gist.github.com/Juanpe/e877f96aaaac1c7e1c122dd6e96ca80c)

要在我们的构建器中包含这个新元素，我们只需要添加一个带有所有必要适配器的新属性，并在编译方法中添加逻辑:

[https://gist . github . com/胡安佩/721 a 2198 bed 905 becd 0 cc 859628895 E4](https://gist.github.com/Juanpe/721a2198bed905becd0cc859628895e4)

# 结论

也许看到我们添加到项目中的所有代码，我们认为我们增加了复杂性，但事实并非如此。在最初的例子中，我们的`APIClient`负责创建请求，这是不正确的，因为唯一应该做的事情就是发送请求。所以我们会违反 SRP 原则。

此外，现在我们的代码更具可扩展性、可读性和可测试性。在一个真实的项目中，API 的规范经常变化，所以我们必须修改请求的创建。使用我们的代码，我们只需要修改我们的构建器，而不需要修改我们结构的任何其他元素。

最后，我们已经看到了构建器模式是如何工作的。我可以告诉你，你最终很可能会在应用程序的其他部分使用它。例如，在具有多个步骤的寄存器中使用它是非常常见的。

我希望这篇文章能帮助你更多地了解构建器模式，以及他如何帮助我们以一种更简单的方式构建`URLRequests`。

感谢阅读这篇文章。分享它，如果它对你有用或者你认为对某人有用。如果您有任何疑问或任何改进建议，请随时在下面发表评论。