# 用 Jest 测试框架测试和模拟异步调用

> 原文：<https://betterprogramming.pub/test-and-mock-asynchronous-calls-with-the-jest-testing-framework-c0efbbbde2c3>

## 关于如何为异步调用建立单元测试用例的详细指南

![](img/684393603e47f8c9315c4b0fde80d507.png)

由[Petr macha ek](https://unsplash.com/@machec?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/calls?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

单元测试隔离程序的每个部分，并验证各个部分是正确的。单元测试用例通常是由开发人员编写和运行的自动化测试。这使得问题能够在开发周期的早期被发现。 [Jest](https://github.com/facebook/jest) 是一个 JavaScript 测试框架，确保任何 JavaScript 代码库的正确性。

异步调用不会阻塞或等待调用返回。发出调用后，程序继续执行。当调用返回时，执行回调函数。由于异步的本质，很难测试异步调用。

# 异步调用的陷阱

下面是一个异步调用的单元测试用例，`setTimeout`。

显然，`1`不是`2`，但是测试通过。

这是异步调用的缺陷。3 号线调用`setTimeout`返回。测试在执行第 4 行之前结束。在测试退出之前没有发现错误——因此，测试用例通过。

嗯，很明显`1`不是`2`。然而，对于复杂的测试，您可能不会注意到假阳性情况。

我们如何发现问题？

## 检测:使用`assertions`

下面是 Jest 对*断言*的定义:

> "`*expect.assertions(number)*`验证在测试过程中调用了一定数量的断言。这在测试异步代码时非常有用，可以确保回调中的断言确实被调用了。”

我们可以在第 3 行加上`expect.assertions(1)`。

这一改变确保了在这个测试用例中将有一个`expect`被执行。因此，由于在退出之前没有调用`expect`，测试用例如预期的那样失败。

如果一个测试用例中有 n 条 *n 条*语句，`expect.assertions(n)`将确保`n`期望语句被执行。测试用例中`expect.assertions(n)`的顺序并不重要。如果把第 3 行移到第 6 行，也是一样。

如果您不在乎`expect`语句执行了多少次，您可以使用`expect.hasAssertions()`来验证在测试期间至少调用了一个断言。

我们能够通过断言来检测问题。我们如何解决这个问题？

## 解决方法:使用“完成”回调

我们可以通过等待`setTimeout`完成来解决这个问题。

我们将 Jest 的`done`回调传递给第 2 行的测试用例，并等待`setTimeout`完成。然后我们调用`done()`告诉 Jest 它现在可以退出了。在`done`回调的帮助下，这个测试用例如预期的那样失败了。

拥有断言来确保异步调用得到实际测试总是一个好主意。

# **测试基于承诺的异步调用**

我们处理了基于回调的异步调用，比如`setTimeout`。基于承诺的异步调用怎么样？

下面的测试用例会通过吗？

测试用例失败，因为`getData`在承诺解决之前退出。它在第 3 行断言时失败。

我们如何解决这个问题？

只需在承诺前加上`return`。由于它返回一个承诺，测试将等待该承诺被解决或拒绝。因此，`then`和`catch`方法中的`expect`语句有机会执行回调。

有四种方法可以正确测试异步调用。

## 方法 1:在承诺的“然后”前加上“返回”,然后抓住电话

通过在每个承诺前添加`return`，我们可以成功测试`getData` `resolved`和`rejected`案例。

## 方法 2:在“expect”之前添加“return”。解析“和”。拒绝的呼叫

Jest 为`expect`语句提供了`.resolves`和`.rejects`匹配器。这些媒人会等待承诺的解决。当然，你还是需要在每个`expect`语句前加上`return`。

## 方法 3:使用不带“return”的“async”和“await”调用

你也可以使用`async`和`await`来做测试，而不需要语句中的`return`。

在第 2 行和第 7 行，关键字`async`声明函数返回一个承诺。在第 4 行和第 10 行，关键字`await`让 JavaScript 等到承诺完成并返回结果。

## 方法 4:应用“期望”。解析“和”。拒绝对“异步”和“等待”的调用

`expect`的`.resolves`和`.rejects`同样适用于`async`和`await`。语句中也不需要有`return`。

# 模拟功能

有时，我们想跳过实际的承诺调用，只测试代码逻辑。模拟函数帮助我们实现目标。有两种模仿函数的方法:

*   创建一个在测试代码中使用的模拟函数。
*   编写一个手动模拟来覆盖模块依赖。

让我们先来看看模拟函数。

`jest.mock(moduleName, factory?, options?)`模仿具有特定名称的模块。`factory`和`options`是可选的。我们有一个模块，`PetStore/apis`，里面有几个承诺调用。

我们可以在测试套件中模拟它们:

我们已经成功地模拟了这三个电话。拒绝案例怎么样？

我们可以将返回值从`Promise.resolve`更改为`Promise.reject`。

如果我们要测试一些成功的案例和一些失败的案例呢？

事实上，Jest 提供了一些方便的方法来模拟 promise 调用。

这些方法可以组合起来以任何顺序返回任何承诺调用。

第 2 行模仿`createPets`，第一次调用返回成功，第二次调用返回失败。

第 3–20 行模拟`listPets`，其第一次调用返回一个一项数组，第二次调用返回 failed，其余调用返回一个二项数组。

第 21 行模仿`showPetById`，它总是返回失败。

这些模拟函数不提供灵活性吗？

如果您已经模拟了模块`PetStore/apis`，您可能希望在测试后将其卸载。

Jest 提供了许多 API 来清除模仿:

*   `jest.clearAllMocks()`:清除所有仿制品的`mock.calls`和`mock.instances`属性。
*   重置所有模拟的状态。除了`jest.clearAllMocks()`之外，它还移除任何被嘲笑的返回值或实现。
*   `jest.restoreAllMocks()`:将所有模拟恢复到原始值。除了`jest.resetAllMocks()`之外，还恢复了原来(非嘲讽)的实现。

Jest 还提供了许多 API 来设置和拆卸测试。

*   `beforeAll(fn)`:它在这个文件中的任何测试运行之前运行一个函数。
*   `afterAll(fn)`:该文件中的所有测试完成后运行一个函数。
*   `beforeEach(fn)`:在这个文件中的每个测试运行之前运行一个函数。
*   `afterEach(fn)`:它在这个文件中的每个测试完成后运行一个函数。

如果上面的函数返回一个承诺，Jest 会在运行测试之前等待这个承诺得到解决。

除了`jest.mock()`，我们还可以通过`jest.spyOn(object, methodName, accessType?)`窥探一个函数。它创建了一个类似于`jest.fn()`的模拟函数，但是也跟踪对`object[methodName]`的调用。它返回一个 Jest 模拟函数。`jest.spyOn()`接受可选的第三个参数`accessType`，可以是`'get'`或`'set'`，如果您想分别监视 getter 或 setter。

假设我们已经嘲讽了`listPets`到`jest.fn().mockRejectedValue([])`，并且`ACallThatInvolveslistPets()`在承诺被拒绝之前写了一个`console.error`，下面的测试就通过了。但是，`console.error`会被执行，污染测试输出。

在 [6 种静默运行 Jest 测试用例的方法](https://medium.com/swlh/6-ways-to-run-jest-test-cases-silently-67d2fead8c11)中，我们已经讨论了如何关闭`console.error`。解决办法就是用`jest.spyOn()`来嘲讽`console.error()`无所事事。

第 3 行创建一个间谍，第 5 行重置它。

此外，间谍可以检查它是否已被调用。

在第 4 行，spy 被调用 0 次，但是在第 6 行，spy 被调用 1 次。

`jest.spyOn()`在这种情况下非常有效。

# 手动模拟

有时，为单个测试用例创建模拟函数太麻烦了。我们可以选择手动模拟来模拟模块。这里，`axios`被用作手动模仿的例子。

手动模拟是通过在紧邻模块的`__mocks__`子目录中编写模块来定义的。如果要模仿的模块是节点模块，那么模仿应该放在与`node_modules`相邻的`__mocks__`目录中。

```
<rootDir>
  ├── __mocks__
  │       └── axios.js
  ├── node_modules
  └── ...
```

如果一个给定的模块存在手动模仿，就像上面的例子，Jest 将在显式调用`jest.mock('moduleName')`时使用那个模块。但是，如果有手动模拟，节点模块会被自动模拟。

这里是一个`axios`手动模拟的例子:

它适用于基本的 CRUD 请求。这是 Jest 中关于如何测试异步调用的整个过程。类似的过程可以应用于其他基于承诺的机制。

**警告**:尽管对于`axios`，这个手动模拟对[拦截器](https://github.com/axios/axios/issues/511)不起作用。另一种方法是使用 jest 或 NODE_ENV 有条件地添加拦截器。

# 结论

我们介绍了如何用 Jest 测试框架测试和模拟异步调用的过程。

感谢阅读。我希望这有所帮助。你可以在这里看到我的其他媒体出版物。