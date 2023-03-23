# 如何在 Swift 中将 Async/await 功能与未来类型结合起来

> 原文：<https://betterprogramming.pub/how-to-bridge-async-await-functions-to-combines-future-type-in-swift-16c668333bdc>

## 了解如何在基于 Combine 的 API 中调用异步/等待代码。

![](img/1b36b46289fb883a1058c0b1b1add3fa.png)

当在 Swift 中处理异步代码时，我们可能必须找到混合和连接不同异步模式的方法，比如将 Combine 框架与 Swift 的 async/await API 一起使用。

在本文中，我们将研究如何在基于 Combine 的 API 中调用异步标记的函数。

让我们看看下面的异步函数，它使用异步/等待模式从服务器加载用户。

为了用 Combine 实现同样的行为，它的 *Future* publisher 就派上了用场。一个未来用一个带*未来的闭包来初始化。许诺*。在做了一些异步工作后，我们称这个闭包的*结果*要么是成功，要么是失败。然后，Combine 会自动将结果映射到适当的发布者事件中。

让我们看看如何用未来的*发布者实现上面的异步功能:*

但是由于我们已经有了一个加载用户的实现，我们希望避免重复编写相同的逻辑两次。

因此，让我们尝试找到一个更通用的解决方案，允许我们将一个*异步 func* 转换为一个*未来*发布者。

在上面的代码中，我们用一个方便的初始化器扩展了 *Future* 类型，该初始化器允许我们用异步闭包初始化一个实例。应用于我们的例子:

对于非抛出异步函数，我们可以添加另一个扩展:

有了这样一个通用的解决方案，我们现在有可能在任何异步函数上使用它，以将其连接到 Combine 的未来类型。

*原载于*[*https://tanaschita.com*](https://tanaschita.com/20220822-bridge-async-await-to-combine-future)*。*