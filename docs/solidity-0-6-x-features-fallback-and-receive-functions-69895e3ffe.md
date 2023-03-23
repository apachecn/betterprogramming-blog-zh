# Solidity 0.6.x 特性:回退和接收功能

> 原文：<https://betterprogramming.pub/solidity-0-6-x-features-fallback-and-receive-functions-69895e3ffe>

## 探索 Solidity 0.6.x 的两个关键特性

![](img/4d6f56bf72015f7b6825c37e5183fc4b.png)

照片由[布伦丹·丘奇](https://unsplash.com/@bdchu614?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/cross-road?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄。

在 0.6.x 之前的 Solidity 版本中，开发人员通常使用[回退函数](https://solidity.readthedocs.io/en/v0.5.15/contracts.html#fallback-function)来处理两种情况下的逻辑:

*   合同收到以太网，没有数据。
*   约定收到了数据，但没有与调用的函数匹配的函数。

0.6.x 之前的回退函数的主要用例是接收以太网并对其做出反应，这是令牌式契约用于拒绝传输、发出事件或转发以太网的典型模式。当在没有任何数据的情况下调用合同时，该函数执行(例如，通过`.send()`或`.transfer()`函数)。0.5.x 的语法是:

第二个用例因实现可升级契约的“委托代理”模式而变得流行。它有一个简单的代理契约，只声明一个后备函数。当协定中没有函数与调用数据中的函数标识符匹配时，调用回退函数。这允许“委托代理”模式，在该模式中，功能在被调用的契约之外实现。以下是实施的一个示例:

调用合同使用汇编代码，我们不会在这里深入讨论，但是你可以在 [Zeppelin 的文档](https://docs.openzeppelin.com/upgrades/2.6/proxies)中读到更多。

# 分割回退功能

我们意识到这个函数的双重目的让开发人员感到困惑，这可能会导致潜在的安全问题。例如，开发人员通常实现一个回退函数，只希望以太网传输调用它，但当契约中缺少某个函数时，也会调用它。令人困惑的是，由于这是预期行为，因此没有报告任何错误。下面是这种令人困惑的行为的一个例子:

当用慈善合同地址调用`CharitySplitter.donate()`时，它的`processDonation`函数被正确调用，按预期处理捐赠。然而，如果错误地传递了`Receiver`契约地址，那么它的回退函数将被调用，并接收发送的值:

因为 EVM 是无类型的，所以 Solidity 不能从它的地址检查合同的实际类型，只能依赖用户提供的内容。函数签名也不能提供防止类型混淆的完美解决方案，但在许多情况下可以工作。

这就是 0.6x 版中回退功能被分成两个独立功能的原因:

*   `[receive()](https://solidity.readthedocs.io/en/latest/contracts.html#receive-ether-function) external payable` —用于空呼叫数据(和任何值)
*   `[fallback()](https://solidity.readthedocs.io/en/latest/contracts.html#fallback-function) external payable` —没有其他函数匹配时(甚至没有`receive`函数)。可选`payable`。

这种分离为希望接收普通以太网的合同提供了备用功能。

# 接收()

一个契约现在只能有一个用语法`receive() external payable {…}`(没有`function`关键字)声明的`receive`函数。

它执行对没有数据的契约的调用(`calldata`)，比如通过`send()`或`transfer()`进行的调用。

函数不能有参数，不能返回任何东西，并且必须有`external`可见性和`payable`状态可变性。要在 0.6.0 下复制上述示例，请使用以下代码:

# 回退()

fallback 函数现在有一个不同的语法，使用`fallback() external [payable] {…}`声明(没有`function`关键字)。该函数不能有参数，不能返回任何内容，并且必须具有`external`可见性。fallback 函数总是接收数据，但是为了也接收 Ether，您应该将其标记为`payable`。要在 0.6.0 下复制上述示例，请使用以下代码:

# 迁移和固定样本合同

因此，我们将有问题的契约转换为 v0.6.x，让它声明一个`receive()`函数，该函数只接受没有数据的传入以太网，并避免导致上述价值损失的类型混淆。

当使用`Receiver`合同地址的调用失败时，对固定合同的调用将恢复:

```
// The following call now reverts
await charitySplitter.donate(badCharity, { value: 10 });
```

我们希望您发现回退功能的逻辑划分对您的设计来说更加清晰，并欢迎您对新语法的任何反馈。