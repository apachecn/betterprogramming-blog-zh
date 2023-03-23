# 使用“justBeforeEach”改进您的 Swift 测试

> 原文：<https://betterprogramming.pub/making-quickspec-lazier-proposing-the-assignbefore-operator-2abc9df47b8d>

## Quick framework 的最新命令使得 BDD 测试更加枯燥

![](img/b41ceeff0c119da42c27b302d6556e28.png)

较小的“it”块跟随较大的“beforeEach”块。由 [Vlad Tchompalov](https://unsplash.com/@tchompalov?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/ducks-in-a-row?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的照片

如何在 Swift 中为一个 API 调用编写有效的单元测试？

你可能已经看过或写过几十个(几百个？)的次数:

这种测试方法很好，但是当您的调用应该在用户完成之前的操作之后才被调用时会发生什么呢？

你现在开始描述 [BDD 测试和](https://cucumber.io/blog/bdd/bdd-vs-tdd/#:~:text=BDD%20is%20designed%20to%20test,pieces%20of%20functionality%20in%20isolation.)TDD 测试。任何时候你进行多个方法调用，你都是在进行一种“旅行”,并且必须决定如何设置你的测试。无论您是否实践 BDD 测试，假设您正在编写测试，测试设置和模拟是一个主要的决策点。

# 设置你的测试，不重复你自己

开箱即用，`XCTestCase`为我们提供了一个`setUp`方法。我们用它来配置我们的`apiService`和`apiRepository`类，但是我们仍然需要重复分配`apiService.postCallResult =`和`apiRepository.postCall()`。

我们可能希望将对`postCall`的调用从`test`函数中提升到`setUp`函数中，但是考虑到 Swift 中函数的范围，我们无法在测试中分配不同的`postCallResult`。

# 更好的快速 BDD 测试编写

几年前，iOS 社区发布了 [Quick](https://github.com/Quick/Quick) ，“一个 Swift 和 Objective-C 的行为驱动开发框架”，其灵感来自 [Rspec](https://rspec.info/) 和其他人。

Quick 使您能够嵌套 Swift 块来创建 BDD 风格的测试。在我们的示例中，这可能采用以下形式:

# 代码中的旅程

这种嵌套结构使我们能够更好地在代码中创建旅程。在旧的方法中，如果我们在调用`postCall`成功后有一个额外的测试，我们将需要复制粘贴并编写一个新的复合测试:

在新的方法中，我们可以将这个测试嵌入到我们的嵌套块中，并避免重复我们自己:

# 但我们不还是在重复自己吗？

尽管在组织上有所改进，但我们仍然需要使用 Quick 为成功和失败的上下文重复调用`.postCall()` API。

这是因为我们在`postCallResult`模拟 API 上设置了不同的值，我们需要在调用 API 之前设置这些值。如果我们要编写如下的测试:

`context(“failure”)`块中的行总是被求值，并且会破坏我们的`context(“success”)`案例。如果我们要编写如下的测试:

当第一个`beforeEach`被执行时`postCallResult`将会是`nil`，并且测试将会崩溃，因为`beforeEach`块是按照声明的顺序被评估的。

一定有更好的方法！

# 快速介绍新的“justBeforeEach”命令

我们需要的是一种特殊类型的块，它允许我们在`beforeEach`块之后、示例执行之前分配变量值，并且只在我们的`context`或`describe`组的范围内执行。

我们需要的是`[justBeforeEach](https://github.com/Quick/Quick/blob/be947fe35f2745650ef3aecf4f54d9de5811ab9f/Documentation/en-us/QuickExamplesAndGroups.md#sharing-setupteardown-code-using-justbeforeeach)`块。

这是我的公司在今年夏天早些时候提出的一个命令，并于 2022 年 9 月并入 Quick。通过添加这个新命令，我们可以编写如下测试:

现在，您可以在测试内部分配变量值，但是在测试外部和上面的块中使用公共代码中的变量值。

这将 [RSpec 的](https://relishapp.com/rspec/rspec-core/v/2-11/docs/helper-methods/let-and-let) `let`操作符的懒惰评估的[好处带到了我们在 Swift 中的快速测试中，并且在名称和行为上与](https://stackoverflow.com/questions/5359558/when-to-use-rspec-let) [Gingko 的](https://onsi.github.io/ginkgo/#separating-creation-and-configuration-justbeforeeach) `[justBeforeEach](https://onsi.github.io/ginkgo/#separating-creation-and-configuration-justbeforeeach)`相似。

# 这真的有那么重要吗？

一旦你的应用程序发展到足够大的规模，你将拥有数百个，甚至数千个类。大多数方法调用都有一个成功和失败的场景，这些场景意味着测试中的大量复制。

编写测试可能会很耗时，所以我们认为任何提高开发人员满意度的测试改进都是值得做的。

# 如何使用 justBeforeEach？

因为 Quick project 最近引入了这一变化，为了从中受益，您应该将您的测试指向 Github 中的`main`分支。

```
.package(url: "https://github.com/Quick/Quick.git", .branch("main"))
```

感谢[快速项目](https://github.com/Quick/Quick)及其[维护者](https://github.com/younata)如此迅速地考虑并采用这个新特性！

# 想要连接吗？

*埃里克·西尔弗伯格是佩里街软件* [*的首席执行官，佩里街软件*](https://www.perrystreet.com/) *是 LGBTQ+约会应用 SCRUFF 和 Jack'd 的发行商，在全球拥有超过 3000 万会员。*