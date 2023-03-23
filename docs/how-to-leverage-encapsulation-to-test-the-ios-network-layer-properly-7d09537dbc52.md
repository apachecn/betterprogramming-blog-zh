# 如何利用封装来正确测试 iOS 网络层

> 原文：<https://betterprogramming.pub/how-to-leverage-encapsulation-to-test-the-ios-network-layer-properly-7d09537dbc52>

## 轻松测试网络层

![](img/26999c3b0d6f00f48a48c729fbba0af7.png)

由[韦斯·希克斯](https://unsplash.com/@sickhews?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

大多数应用程序都必须执行网络呼叫。我们需要它们从服务中检索数据、执行登录或者向远程服务器发送数据。

在一天结束时，我们通常会在网络层上编写一个抽象，并且我们想要测试这一层。

但是，测试网络层并不像看起来那么简单。网络固有地与外部依赖进行交互:后端。我们无法控制这种依赖性，如果我们依赖它，我们的测试就会变得缓慢、不可靠。

我们希望测试是**快速**、**可靠、**和**可重复**。为了实现这一点，我们需要隔离和排除这种外部依赖性。有不同的技术可以帮助我们实现这个目标，使测试更加可靠。最常见的方法是:

*   模仿后端的外部库(已知的几个有: [**震撼**](https://github.com/justeat/Shock) 、 [**Mockingjay**](https://github.com/kylef/Mockingjay) 、 [**Mocker**](https://github.com/WeTransfer/Mocker) )。
*   定制机制来模仿像`[URLProtocol](https://developer.apple.com/documentation/foundation/urlprotocol)`这样的系统类(正如[墨迹工程师](https://medium.com/u/a7cfccf536da?source=post_page-----7d09537dbc52--------------------------------)在这篇[博客文章](https://www.theinkedengineer.com/articles/mocking-requests-using-url-protocol/)中所展示的)。

这两种方法都是有效的，并在行业中广泛使用。我不喜欢这些方法的地方在于，它们引入了比我们实际需要的更多的复杂性。在前一种情况下，他们向我们的测试代码添加外部库。在这两种情况下，它们都需要大量样板代码来设置模拟和存根。最后，他们的速度没有想象中那么快。他们总是依赖于可能导致测试延迟的预期。没有它们我们也能进行测试。

今天，我要提出一种不同的方法。它隐藏了这两种方法的复杂性，让我们更容易地测试我们真正需要的东西。

# 当前的解决方案

为了更好地理解所提出的方法的优点，我们应该首先看看测试通常是如何进行的。我将介绍一个标准网络层的简化接口和实现。然后，我将展示如何使用这种方法编写测试，强调各种复杂性。

网络层有多种不同的实现方式。为了简单起见，让我们使用最广为人知的带有回调和`Result`类型的`Protocol`导向方法。我在这里介绍的一切都可以适用于`Protocol Witnesses`和**联合收割机。**

## 界面

所以，让我们为我们的`NetworkClient`准备我们想要使用的接口。

在这个代码片段中，我们有一个定义了`NetworkClient`接口的协议。它为`GET`提供了从`URL`获取一些数据的可能性。

这个接口是最基本的，但是我们可以将同样的推理应用于任何其他网络调用:`POST`、`PUT`、`DELETE`等等。

## 实施

为了实现这个接口，我们创建了一个`LiveNetworkClient`类。为了执行网络调用，客户机接收一个`URLSession`对象作为参数，利用[依赖注入](https://en.wikipedia.org/wiki/Dependency_injection)。代码如下所示:

我用编号注释标记了代码的各个部分。有序列表中的每个数字对应于代码中的一个特定注释:

1.  我们为我们的类型定义了一个自定义的`NetworkError`。
2.  我们定义了一个注入到`init`中的`URLSession`属性。
3.  我们协议的签字。
4.  我们用我们的`httpMethod`和`headers`配置请求。
5.  我们开始`dataTask`。别忘了第 52 行的`.resume()`。
6.  我们处理错误(提前退出)。
7.  我们提取了一些有用的信息。
8.  我们根据可能的选择来处理响应。

代码看起来不错:干净、可靠。现在是测试的时候了

# 测试

我们想测试一下，给定一组特定的参数(URL、标题等等)，我们从后端接收到一个特定的响应——数据或错误。

第一个障碍是如何控制`URLSession`。最简单的方法是创建该类的模拟版本，以便我们可以覆盖它的方法:

现在，当涉及到测试时，我们可以编写如下内容:

在代码中，我们首先为测试准备一些数据。我们有一个可解码的结构`TestDecodable`,这是我们希望被(模仿的)后端接收的数据类型。而且我们有我们想要联系的`URL`。

在`testGet_whenSucceeds()`方法中，我们初始化通过`MockedURLSession`的`LiveNetworkClient`。我们立即发现一些令人担忧的事情:

![](img/50af5d329f28cdca42dbcba225f04f2a.png)

我们不应该用默认的`init`创建一个模拟会话:它已经被弃用，我们的测试可能会在未来的版本中中断。但目前这只是一个警告，我们可以接受。

我们带着期望完成测试，并处理结果。我们想测试快乐之路，成功了。如果出现网络错误或失败，我们决定失败，但最终，如果成功，我们将在变量中捕获接收到的对象。

最后，我们必须断言接收到的数据与我们预期的数据相同。但是我们没有办法控制会话返回哪些数据。我们甚至不能检查我们实际上是在用正确的参数调用正确的 URL！

为了解决这些问题，我们可以尝试在`MockURLSession`中覆盖`dataTask`方法。归根结底，这就是我们创建该子类的原因。`dataTask`签名如下:

```
func dataTask(with request: URLRequest, completionHandler: @escaping (Data?, URLResponse?, Error?) -> Void) -> URLSessionDataTask
```

然而，这并不像看起来那么简单:

1.  该请求接收回调参数，但不会立即执行。
2.  该函数返回一个我们也必须返回的`URLSessionDataTask`对象。
3.  当在`URLSessionDataTask`对象上调用`resume`方法时，请求被执行。就在那时，回调被调用。

这些点意味着我们应该创建一个`URLSessionDataTask`的子类并覆盖它的`resume`方法。我们还应该找到一种方法，将调用`resume` 时必须调用的回调以及必须返回的数据传递给 URLSessionDataTask。很可能，这需要将额外的数据传递给`dataTask`方法，这样它就可以将数据传递给`URLSessionDataTask`对象。

当我们想要测试错误时，我们需要遵循与成功数据相同的路径。如果我们需要支持其他类型的任务，比如`URLSessionUploadTask`，我们必须实现`URLSessionTask`对象的许多子类，并复制代码来传递参数和调用回调。

你可以看到，对于一个非常简单的测试，我们需要做多少工作。应该有更好的办法。

# 我们真正需要测试的是什么？

回到问题的根源，我们对`URLSession`如何工作的实现细节不感兴趣。

我们需要一种方法来模拟一个简单得多的行为:执行一个网络请求并调用一个回调，然后找到一种方法来实现这个行为。

# 解决方案

我们可以通过增加一个名为`RequestExecutor`的新协议来做到这一点。我们可以让`URLSession`符合协议，在生产中使用它。有了这个协议，我们可以创建一个我们完全控制的简单模拟实现。

作为第一步，让我们创建`RequestExecutor`协议，并让`URLSession`符合它:

在这个代码片段中，`execute`方法的签名类似于`dataTask`方法的签名。主要区别在于`execute`方法返回的是`Void`而不是自定义对象。

为了使`URLSession`符合协议，我们需要在扩展中实现`execute`方法。该实现一个接一个地调用`dataTask`和`resume`方法。

第二步，我们可以修改我们的`NetworkClient`类来使用这个新协议。代码更改如下:

`NetworkClient`没有太大变化。我们只是在第 6、8 和 9 行用`requestExecutor`替换了所有对`session`的引用。在这个方法中，我们使用了`requestExecutor`而不是`URLSession`对象，并且删除了对`resume()`方法的调用。

我们将`URLSession.shared`参考作为默认参数保留在`init`中，以简化我们在生产中初始化`NetworkClient`的方式。

这在产品代码中并不是一个大的改变，但是它极大地改进了我们的测试！

# 测试

作为第一步，让我们为请求执行器创建一个模拟实现。我们想完全控制它:

通过这个模拟，我们定义了两个属性:一个`executeCount`和一个`executeClosure`。计数器用于检查`execute`函数被调用的次数。这可以防止我们忘记调用它的错误或者多次调用它的错误。

然后，我们有了`executeClosure`。这是在我们的测试中控制模拟依赖的一种方法:我们可以在编写测试时注入我们需要的代码。默认值是一个失败的闭包:它允许我们检查我们没有在不应该的时候调用依赖项。如果一个组件在不应该的时候调用请求执行器，测试就会失败，因为我们不会在组件的测试中设置闭包。如果一个组件需要网络，而我们忘记设置闭包，测试就会失败。这是一种安全的方法，可以反复检查各种组件的方法是否如我们预期的那样运行。

`execute`的实现很简单:我们递增计数器，然后调用闭包。注意执行人是**而不是**直接调用回调；测试必须使用正确的参数在正确的时间调用它。

让我们用下面的代码来看看我们的测试是如何变化的:

1.  我们定义了代表后端返回数据的`TestDecodable`结构。
2.  我们定义想要联系的 URL。
3.  我们定义后端返回的 JSON，并且我们的代码应该解码。
4.  在`testGet_whenSucceed()`方法中，我们准备了测试中需要使用的变量:`mockRequestExecutor`和`liveNetworkClient`。
5.  我们配置`mockExecutor.executeClosure`。在这个闭包中，我们需要做两件事:断言参数正是我们期望的参数；向测试提供正确的数据，用正确的参数调用回调。
6.  我们作用于`liveNetworkClient`，执行网络请求。在回调中，我们处理结果。
7.  我们断言我们只执行一个网络请求:我们不想执行多个请求。

注意另一件事:我们去除了所有的期望——以及它们的延迟！我们完全控制流程，可以同步执行所有代码。不再需要期待了。测试现在已经尽可能快了。

![](img/a14cde8b77d37b6faf12bc38bbd7994f.png)

此外，我们可以非常容易地编写所有其他测试用例——只需用不同的数据替换回调参数并相应地断言。

# 结论

在今天的文章中，我们看到了如何在没有外部依赖或样板文件的情况下测试应用程序中的网络层。

这种方法的工作原理是对 iOS 中如何执行网络调用的实现细节进行抽象。我们正在应用一个可靠的原则，即[依赖倒置](https://en.wikipedia.org/wiki/Dependency_inversion_principle):

> “抽象不应该依赖于细节。细节(具体实现)应该依赖于抽象。”

`NetworkClient`是一个抽象，不应该依赖于`URLSession`的实现细节。

我们能够去除所有的期望，完全控制我们的依赖。我们实现了**快速**、**可靠、**和**可重复**测试。

最后，这也允许我们实施更严格的测试。我们不仅可以检查代码是否如我们预期的那样工作，还可以检查`execute`方法被调用了多少次，我们是否用正确的参数调用了它，以及它是否用在了不应该用的地方。