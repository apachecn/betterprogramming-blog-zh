# 如何在中使用结果类型？网

> 原文：<https://betterprogramming.pub/bring-error-handling-and-eliminate-nullreferenceexceptions-using-a-result-type-in-net-f4447dceb6c4>

## 使用此结果类型消除 NullReferenceExceptions 并提升您的错误处理能力

![](img/ff48b3e081c0a7d47dc8dfd64fdd5e5e.png)

[JESHOOTS.COM](https://unsplash.com/@jeshoots?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

非函数式编程语言的一个小缺点是，如果不检查，你不能 100%确定你当前处理的值不是空的。通常，我们只是实现一个`Function<T>`并在代码中继续使用该返回类型，而不检查它是否为空。

像 Rust 或 F#这样的编程语言将这个问题提升到一种`Result`类型或`Option`类型。这种类型提醒您——并且有点强迫您——首先检查被调用的函数是否真的返回了预期的值或 null。

然而在 C#中，对于这个问题还没有一个好的解决方案。您可以使用一个可空类型，并在您的。csproj 文件，但这仍然缺少结果类型的一个重要方面:显式性。当您在代码中接收到一个可空类型时，您可能只会看到，它被它旁边的`?`操作符可空，或者由于您的 IDE 提醒您，您可能正在处理一个空值。

所以现在我们需要一个`Result`类型，它明确地提醒我们检查底层值是否被设置。现在想象一下，我们收到一个失败的函数返回的`Result`。调用者肯定想知道哪里出错了，这样他就可以记录一个错误或者做任何其他方面的逻辑。这可以通过在方法失败的情况下向`Result`添加异常来解决。

# 履行

> 更新(25.09.2022):我被告知，这里已经实施了类似的解决方案:

[](https://github.com/louthy/language-ext/blob/main/LanguageExt.Core/Common/Result/Result.cs) [## 主 louthy/language-ext/result . cs

### 此文件包含双向 Unicode 文本，其解释或编译可能与下面显示的不同…

github.com](https://github.com/louthy/language-ext/blob/main/LanguageExt.Core/Common/Result/Result.cs) 

> 更新(07.10.2022):另一个值得一提的是 [dotNext](https://github.com/dotnet/dotNext) 包中的结果类型，它由。网络基础:

 [## 结构结果

### 表示操作的结果，可以是实际结果或错误代码。汇编:DotNext.dll 语法公共只读…

dotnet.github.io](https://dotnet.github.io/dotNext/api/DotNext.Result-2.html) 

## 结果类型

为了处理所有用例，我们需要两种结果类型。一种结果类型用于不返回值的方法，另一种结果类型用于返回值的方法。我们称它们为`Result`和`Result<T>`，其中`Result<T>`继承`Result`。

`Result`有两个属性:`Success`和`Message`，以及一个方法`GetError()`，该方法返回底层异常(如果可用的话)。此外，我们实现了一个隐式转换，允许您直接返回任何`Exception`，它会自动转换为`Result`。

继承`Result<T>`通过添加类型`<T>`的有效载荷来扩展`Result`，该有效载荷可通过`GetOk()`方法访问。这里我们还添加了隐式转换，这样我们就可以返回有效负载，而不用每次都将它转换为`Result<T>`。

## 结果类型的扩展

在我们的`Result`类型之上有一点额外的特性，那就是一个扩展方法，它允许我们检查结果是正还是负，当结果是负时抛出一个异常。这个扩展方法将返回调用它的`Result`，并抛出异常**，同时保留 StackTrace。当我们想要容易地将结果冒泡到其他层，而不丢失关于底层异常的宝贵信息时，这是特别必要的。**

通常情况下，当我们抛出一个现有的`Exception`时，异常的 StackTrace 会被一个新的 StackTrace 替换，这个 Stack trace 会丢失之前的堆栈，我们不希望出现在这里，因为异常的来源当然非常重要。

这个扩展方法现在允许我们像这样使用`Result`:

这个结果类型是我的大多数项目的内部核心的一部分，这使得它对我的代码架构非常重要。如果正确使用它，它会强制执行更干净的代码风格，并消除 NullReferenceExceptions。

如果你想查看实现和一些单元测试，以及用例，你可以在这里查看我的 GitHub 库:

[](https://github.com/TobiStr/ResultType) [## GitHub - TobiStr/ResultType:中结果类型的建议。网

### 中结果类型的建议。这种结果类型在您希望适度实施检查的场景中很有用…

github.com](https://github.com/TobiStr/ResultType) 

感谢您花时间阅读这篇文章。我希望，你会觉得它有知识性、教育性和趣味性。非常感谢您的支持和参与。

如果您有兴趣了解干净架构、干净编码和最新技术栈的最新趋势、技巧和诀窍，尤其是在 C#环境中。净和有棱角——如果你考虑跟踪我，我会很感激。

祝你有美好的一天！

![](img/429378e6a7aee01df92851fb0e90e385.png)

如果你还没有每天使用媒体来增长你的知识，现在是开始的最佳时机！借助 Medium，您可以轻松获得更多关于高度专业的主题的知识，发布高质量的内容，并接触到更广泛的受众。要开始，只需使用以下链接创建一个中型帐户:

[*现在加入中等*](https://medium.com/@tobias.streng/membership)

通过这样做，你将获得一个强大的平台，可以帮助你联系新的作者和读者，每天学习新的东西。