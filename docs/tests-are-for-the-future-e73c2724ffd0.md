# 测试是为了未来

> 原文：<https://betterprogramming.pub/tests-are-for-the-future-e73c2724ffd0>

## 现在就写测试，满怀信心地重构代码

![](img/b1bad5cce285b475f613d0ad9a685f87.png)

文森特·所罗门在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

想象一下:您刚刚为一个新特性编写了一些代码。您正在编写一些单元测试。当你在写测试的时候，你开始有一个生存危机。“我为什么要写这些测试？”你扪心自问。“我已经手动验证了我的代码是有效的，我甚至让另一个开发人员也进行了测试。有什么意义？”

答案在于一个重要的认识:你的测试是为了未来。

当然，它们也是针对现在的，可以帮助你抓住一些你在开发新特性时可能已经忘记的边缘情况。但是测试主要是为那些在接下来的几个月或几年中从事代码工作的人准备的。

让我们探索一下这是怎么回事。

# 证明文件

测试作为文档记录了某个特性应该如何表现。

测试本质上是以代码形式编写的产品需求。稍后使用该特性的开发人员可能会对代码的意图或某些场景应该如何处理有疑问。

开发人员可以直接在他们的 IDE 中跳转到测试套件，而不是挖掘旧的 JIRA 门票或其他地方托管的可能过时的文档。通过查看测试用例，他们可以很好地了解该特性是如何工作的。

![](img/f0cc293b37def05d8ade590536af580f.png)

由[凯利·西克玛](https://unsplash.com/@kellysikkema?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄。

# 避免倒退

测试有助于您在开发新功能时避免代码库的退化。

虽然这些新特性可能看起来与某些现有代码无关，但总有可能这两者以某种方式联系在一起，而您却忽略了这一点。一个可靠的测试套件将捕捉到你无意中对现有代码产生负面影响的地方。

没有适当的测试，如果不进行大量(并且乏味的)手动测试，您永远无法确定您正在编写的新代码是否能很好地与旧代码一起运行。

![](img/44a7cbce04bef89d65ab4c4383eb2cda.png)

[Ivan vrani](https://unsplash.com/@hvranic?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片。

# 重构

编写测试的最有说服力的理由，以及为什么它们是未来的，是因为它们允许你自信地重构。

我相信您曾经在团队支持大型遗留应用程序的地方工作过。遗留应用程序中隐藏了一些绝对重要的东西。可能是你的支付处理业务逻辑。可能是你的验证码。

不管它是什么，它对你的应用程序的核心功能是必不可少的，每个人都害怕接触它。它很旧，看起来工作正常，但它已经变成了一大堆没有人真正理解的代码。

为什么每个人都害怕去研究它？因为它没有任何测试！这意味着你修改的任何一行代码都有可能在你不知情的情况下破坏某些东西。这意味着你对这个功能的每一个小的改变都需要大量的手工测试。这意味着当你点击“提交”按钮来合并你的代码时，你会变得非常紧张并交叉手指。

![](img/d2e22cb92b1005df7ca51bd1afcb9db2.png)

[JESHOOTS.COM](https://unsplash.com/@jeshoots?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照。

现在，在另一个现实中，想象同样的核心功能，但是有一个很好的测试套件充分覆盖代码。当需要重构代码时，您可以放心地去做。为什么？因为你会知道你是否打碎了什么东西。如果现在测试都通过了，你做了一些改变，现在你有一些失败，很明显有些事情还不太对劲。

但是这并不麻烦，因为在将这些新的更改发布到生产环境之前，您就已经发现了错误，并且能够找到根本原因，确保您的重构这次能够正常工作。

# 结论

测试是未来的事。它们提供文档，帮助您避免回归，并允许您满怀信心地进行重构。

另外，如果你想了解更多，请查看[我写的关于测试驱动开发的文章](https://medium.com/swlh/a-case-for-test-driven-development-8e36df33dc21?source=friends_link&sk=b1756de0b41e2956ac1012faba43aa1e)。