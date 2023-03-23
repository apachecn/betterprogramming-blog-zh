# 我最喜欢的 7 种自定义扩展方法。NET 7 和 C#

> 原文：<https://betterprogramming.pub/my-top-7-custom-extension-methods-for-net-7-and-c-494acb2e6634>

## 用这些基本的扩展方法提高你的流畅编码！

![](img/4ed3af2acacc4df945feeee70986260f.png)

瑞兰德·迪恩在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 介绍

自从我开始了解扩展方法后，我不断发现新的可能性，让我的程序员生活变得更容易。扩展方法是 O 在固体中的完美应用— [开闭原理](https://en.wikipedia.org/wiki/Open%E2%80%93closed_principle)。一个类应该尽可能的简单，并且只在其他组件真正需要的时候才公开属性和方法。

通过扩展方法，您可以为您的类实现额外的方法，而无需更改类本身！这非常适合将类作为参数的递归方法。

实现扩展方法相当容易。看看下面的例子:

这实现了每个`IEnumerable<T>`(由`this`关键字引入)的`ForEach()`方法，就像你从`List<T>`类型中知道的一样。要访问这个方法，您唯一需要做的就是添加一个对各自名称空间的引用，在本例中是`System`。

# 我最常用的 7 种扩展方法

自从。NET 6，微软已经为`IEnumerable<T>`实现了一些扩展方法，包括`DistinctBy()`和`Chunk()`。然而，截至。NET 7 中，我仍然缺少一些非常重要的方法，尤其是处理任务集合的方法。

事不宜迟，下面是我在。网络 7:

# 1.try 异步

第一种扩展方法是我最喜欢的。有多少次你在一个刚刚创建的方法周围添加了一个新的`try-catch`块，并且因为它破坏了外观而变得有点恼火？当您的方法返回一个`Task`或`Task<T>`时，这里有一个非常简洁的解决方案:

现在你可以简单地写:

```
var result = await GetSomethingAsync().TryAsync();
```

您的方法将自动包装在一个`try-catch`块中。此外，您可以提供一个`errorHandler`用于额外的辅助逻辑，如日志记录。然后可以检查这些方法返回的`Result`是否成功，您可以继续您的逻辑。

顺便说一下，你可以在这些方法中找到的`Result`或`Result<T>`来自我以前的文章:

[](/bring-error-handling-and-eliminate-nullreferenceexceptions-using-a-result-type-in-net-f4447dceb6c4) [## 如何在中使用结果类型？网

### 使用此结果类型消除 NullReferenceExceptions 并提升您的错误处理能力

better 编程. pub](/bring-error-handling-and-eliminate-nullreferenceexceptions-using-a-result-type-in-net-f4447dceb6c4) 

# 2.何时同步

在一个集合中有一个以上的`Task`或`Task<T>`在正常情况下有点不方便。你需要给`Task.WhenAll(tasks)`打电话，这让我有点不知所云，因为这不是一种流畅的风格。它看起来是这样的:

现在，我可以简单地收集任何任务，并简单地编写:

```
var results = await tasks.WhenAllAsync();
```

# 3.WhenAllSequentialAsync

下一个扩展方法甚至为您节省了几行代码，并使您能够一个接一个地执行每个任务。这在您可能不会并行执行许多任务的情况下可能很有用。

# 4.WhenAllParallelAsync

最后但同样重要的是，可能有一个用例，您可以并行执行任务，但可能有一个最大值的限制。平行工人。对于这种情况，我有以下扩展方法:

使用`degree`参数，您可以指定应该并行执行多少个任务。

# 5.MapAsync

这个也是一个流畅的扩展，但是这次是针对单个的`Task`或者`Task<T>`。

使用这种扩展方法，您能够流畅地将一个`Task<T1>`映射到一个类似于`Enumerable.Select()`方法的`Task<T2>`。

# 6.DoAsync

一个类似的方法是`DoAsync()`，但是不是转换一个任务，你可以用`tasks`结果执行边逻辑而不改变它的返回值。

# 7.字符串。加入

最后一个是扩展方法，我有时用它来连接日志记录的字符串。通常情况下，您可以使用`string.Join()`来实现这一点，但是同样，这并不流畅，会使我脱离流程。

# 缩写

从技术上来说不是扩展方法，但对保存一些代码也很有用，下面是我的缩写方法:

为了方便地使用它们，您必须在您的根级别上创建一个特殊的文件(我通常称之为`GlobalUsings.cs`)，并在其中放入下面一行:

```
global using static System.Abbreviations;
```

# 1.arrive）

`Arr`方法是从已经存在的值创建新的`IEnumerable<T>`的缩写。通常，这需要一个非常难看的代码，如下所示:

```
var arr = new [] { param1, param2,... };
```

现在你可以简单地这样写:

```
var arr = Arr(param1, param2,... );
```

这样看起来更漂亮，而且你不用用手指在键盘上玩杂技。

# 2.尝试

你是否经常想在一个新方法周围添加一个简单的`try-catch`块，但感觉很难看，因为它在你的代码中占据了太多的空间？下面用缩写法`Try()`来解一下。

用最简短的形式，你可以写:

```
Try(TestMethod);
```

其中`TestMethod`是一个不带任何参数的方法。这使您能够编写非常短的代码，并消除那些讨厌的`try-catch`块。

对于异步方法，我推荐你使用上面的扩展方法`TryAsync`，因为它是流畅风格的。

当然，还有更多的可能性，您可以使用这种模式并创建自己的缩写和扩展方法。让我知道你对评论的看法。

感谢您花时间阅读这篇文章。我希望，你会觉得它有知识性、教育性和趣味性。非常感谢您的支持和参与。

如果您有兴趣了解干净架构、干净编码和最新技术栈的最新趋势、技巧和诀窍，尤其是在 C#环境中。净和有棱角——如果你考虑跟踪我，我会很感激。

祝你有美好的一天！

![](img/429378e6a7aee01df92851fb0e90e385.png)

如果你还没有每天使用媒体来增长你的知识，现在是开始的最佳时机！借助 Medium，您可以轻松获得更多关于高度专业的主题的知识，发布高质量的内容，并接触到更广泛的受众。要开始，只需使用以下链接创建一个中型帐户:

[*加入中现*](https://medium.com/@tobias.streng/membership)

通过这样做，你将获得一个强大的平台，可以帮助你联系新的作者和读者，每天学习新的东西。