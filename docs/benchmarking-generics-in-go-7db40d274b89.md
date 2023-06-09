# Go 中的泛型基准测试

> 原文：<https://betterprogramming.pub/benchmarking-generics-in-go-7db40d274b89>

## 泛型将如何影响性能？让我们通过对几个用例进行基准测试来找出答案。

![](img/2c5dc179a44484813bc7710fa7359612.png)

图片由珀西·博尔梅尔提供。Gopher 由拓也·上田提供，原始 Go Gopher 由勒内·弗伦奇提供(CC BY 3.0)

上周我写了我们将在 Go 1.18 中看到的新功能。如果你没有，你应该读一读。

[](https://towardsdatascience.com/go-1-18-comes-with-many-amazing-changes-d33ac0afd6ee) [## Go 1.18 带来了许多惊人的变化

### Go 1.18 将于二月发布，它包含了多项改进语言的变化

towardsdatascience.com](https://towardsdatascience.com/go-1-18-comes-with-many-amazing-changes-d33ac0afd6ee) 

那篇文章的许多读者在各种社交媒体平台上联系我，我想对那些读者说谢谢。我喜欢它带来的参与和讨论。

其中一个讨论是我想写的主题，我们将在泛型介绍中看到的*性能影响*。许多读者担心泛型会降低性能，然而我的论点是泛型会提高性能。我的论点背后的理由是，泛型将允许我们在运行时跳过类型转换、断言和反射，而是依靠编译器在编译时解决这个问题。

在我关于[学习泛型](https://towardsdatascience.com/learning-generics-in-go-318f53752ccd)的文章中，我解释了泛型的用法，两个主要的好处是减少了基于数据类型的重复函数和避免了`interface{}`。这些是我们将在本文中进行基准测试的用例，以发现变化的性能。

> 让我在这里做一个小的概念，我不是一个标杆向导。我只是一个卑微的标杆 noob。对我来说，标杆管理非常困难。

为了制定一个公平的基准，我们将为每个用例建立一个测试用例。这将意味着我们将

1.  使用重复函数进行基准测试
2.  使用泛型进行基准测试
3.  基准测试使用`interface{}`

## 准备要进行基准测试的功能

我们将重用来自[学习泛型](https://towardsdatascience.com/learning-generics-in-go-318f53752ccd)的一些代码，在其中，我们有一个`Subtract`函数，它减去三个`Subtractable`数据类型之间的值。

我们需要确定哪种减法效果最好。

我们将进行基准测试的减法。在[游乐场](https://gotipplay.golang.org/p/BLU8pHOzmvS)试试吧。

在这里，我们将开始对函数进行基准测试。它们应该很容易理解，我们涵盖了减法、基于数据类型、类型切换和泛型的可能解决方案。

## 准备基准

创建一个常规的测试文件，我们可以在其中存储基准，如果你不熟悉 Go 中的基准，你可以阅读我的教程。

[](/we-measure-the-power-of-cars-computers-and-cellphones-but-what-about-code-91ed5583f298) [## 我们测量汽车、电脑和手机的功率。但是代码呢？

### 深入了解 Golang 的基准测试

better 编程. pub](/we-measure-the-power-of-cars-computers-and-cellphones-but-what-about-code-91ed5583f298) 

在基准测试的顶部，我将生成两个切片，一个是随机整数切片，一个是随机浮点数 32s 切片。这些随机切片将被用作减法方法的输入参数。

然后我们创建一个`b.Run`,它将一次触发一个函数，触发的次数与我们设置为 benchmarker 运行`-benchtime`标志的次数一样多。对于这个基准测试，我将强制基准测试者运行每个函数 1000000000 次。如果不指定运行函数的次数，基准测试程序会在特定时间内尽可能多地运行函数。这将导致他们不再运行相同数量的操作，我希望他们这样做。

这就是我的最终基准看起来的样子。我希望我能提供一个基准的操场链接，但操场上只允许常规的`testing.T`。所以如果你想自己尝试，你必须复制要点。

用于执行基准测试以确定一般性能影响的测试文件。

基准测试将在所有用例中测试`int`和`float32`的减法函数。在通用基准中，我添加了第三个选项，推断数据类型。我还想确定如果我们让泛型函数将数据类型推断为`int`，我们会损失多少性能。

要运行基准测试，请使用以下命令。请注意，`-count 5`参数用于运行每个基准 5 次。这是因为如果您运行每个基准一次，您可能会得到不公平的结果。在其中一次基准测试中，可能有其他处理器窃取了计算机的功率。

```
go test -v -bench=Benchmark -benchtime=1000000000x -count 5
```

## 分析结果

基准测试将输出正在运行的函数的名称，我们可以用它来识别不同的函数。第二个值是运行的操作数，在我们的例子中，我们将其设置为一个固定的数字，因此所有的行应该显示相同的内容。

第三个输出是有趣的，它是每个操作的纳秒数(ns/op)。这是显示函数平均速度的指标。

来自 go 测试工具的基准测试结果。

从结果中，我们可以确定类型断言函数要慢得多。大约慢了 50-90%。在这个测试案例中，这可能看起来很荒谬，因为我们谈论的是半纳秒。

通用函数的性能与特定于数据类型的函数差不多，只是速度稍有提高。这种速度上的小小提高可能是由于我电脑上运行的其他软件的推断。在我的思想状态中，我认为在编译器完成它的工作后，通用函数调用应该和常规函数调用一样。

我们可以在结果中看到的另一个要点是`int`减法比`float32`减法更耗时。常规`int`减法的平均速度是 *0，85478 ns/op* ，常规`float32`减法的平均速度是 *0，8188 ns/op.* 这意味着`float32`减法在我的基准测试中大约快 *5%* 。

因此，从该基准测试中得出的关键结论是:

*   根据我的论文，类型断言/类型转换解决方案是最慢的
*   泛型和常规数据类型函数具有同等的性能
*   `Float32`减法比`int`快

## 基准测试的真实场景

让我们也来比较一下现实生活中的场景。在用例中，我们有两个结构`Person`和`Car`，它们都可以`Move`。这两种结构都有一个接受距离的`Move`函数，但是，人的距离作为`float32`传递，而汽车接受一个`int`。

这两种结构都在同一个工作流中处理，所以我们希望在同一个函数中处理它们。

通用的解决方案是创建通用的结构，在这个结构中我们可以定义创建时要使用的数据类型。接口解决方案是接受结构作为输入，类型断言它们，并转换正确的数据类型。由于数据类型不同，我们无法为它们提供共享接口。

我不会详细解释泛型解决方案是如何工作的，如果你想理解的话，你可以看看我关于泛型的文章。

[](https://towardsdatascience.com/learning-generics-in-go-318f53752ccd) [## 在围棋中学习泛型

### 泛型在 Go 1.18 中发布，是时候学习如何利用这个新特性了

towardsdatascience.com](https://towardsdatascience.com/learning-generics-in-go-318f53752ccd) 

在代码示例中，泛型和旧的类型断言解决方案都有一个实现，类型断言以`Regular`为后缀，因此我们可以更容易地知道什么与什么解决方案相关。

对不同数据类型的汽车和人员执行移动的通用解决方案。在[游乐场](https://gotipplay.golang.org/p/P6XR8TtDSJf)试用。

键入切换的解决方案移动，尝试在[操场](https://gotipplay.golang.org/p/QQKH1jmJT2K)它。

现在我们已经有了解决方案，是时候建立基准了。我将在基准测试之前创建人和车，我们将测量`Move`和`MoveRegular`的性能。

将运行 Move 和 MoveRegular 函数的基准

我使用以下命令运行测试

```
go test -v -bench=Benchmark_Structures -benchtime=1000000000x -count 5
```

运行基准测试的结果

看到类型断言解决方案比通用解决方案快，我有点惊讶。我确保多次运行基准测试，所以它不是临时的。

我们可以从基准测试中看到，基于 Cars、`Int`的解决方案比基于 Person、`float32`的数据类型更快。

`Person`移动功能具有相同的性能，无论是通用的还是常规的解决方案。然而，你可以看到不同的汽车，与类型断言汽车是最快的。这种型号的汽车运行速度比普通汽车快 20%。

因此，从该基准中获得的关键信息如下。

*   基于浮点的类型共享相同的性能，而类型断言的整数 cars 更快，正如我的论文所言
*   `Float32`加法比`int`慢

## 结论

所以，我们现在已经测试了一些用例，在这些用例中我可以看到泛型是有帮助的。

老实说，我确实希望第二个基准测试也能证明泛型更快。这将加强我的主张，即泛型更具性能，因为它是在编译时而不是运行时决定的。

通过使用泛型或特定于数据类型的函数，我们可以在第一个用例中看到相当大的性能提升。我知道几纳秒可能看起来很荒谬，但在一些用例中，这种类型的极端优化非常重要。我曾经做过一个高性能的网络嗅探器，它必须实时处理大量的网络数据。编写这样的软件需要所有的优化。

我们已经看到，选择正确的数据类型对性能有很大的影响。然而，我认为我们可以说，那些表达了对泛型使软件变慢的恐惧的读者可以冷静了。从好的方面来看，我看到通用解决方案允许我们更容易地交换数据类型，从而提高性能。

另一方面，Go 中的类型断言和类型转换似乎具有超强的性能。

正如我们所看到的，许多因素都会对结果产生影响，比如使用的[算术运算符](https://www.techopedia.com/definition/25582/arithmetic-operator)，数据类型等等。我的基准中可能有我不知道的错误。

如果您对如何改进基准有任何想法或者想要讨论它们，请随时联系我们。你可以在 [GitHub](https://github.com/percybolmer/benchmarkinggenerics) 找到完整的代码。