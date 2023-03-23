# 揭开 Rust 中错误处理的神秘面纱

> 原文：<https://betterprogramming.pub/error-handling-in-rust-that-every-beginner-should-know-9efa59deb934>

## 什么时候该慌，什么时候不该慌！

![](img/84d6e9c083c127faeb41b22fe6be3615.png)

由[布雷特·乔丹](https://unsplash.com/@brett_jordan?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/error?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

Rust 有一个非常有效的错误处理机制，不会影响可读性。你将学会什么时候去`panic!`以及如何以一种实用和平易近人的方式处理 `Result<T, E>`的事情。

大约 4 年前，我写了关于 Go 中的错误处理的文章，它很受欢迎。本文将遵循那篇文章的思路，那篇文章侧重于语言特性和 API 的实用方面。

我之所以写这篇文章，是因为当时我能找到的所有书籍和其他资源似乎都没有回答我的很多问题。

我经历了许多尝试和错误，最后，我得到了我的问题的答案。我把我学到的东西整理成这篇文章。希望它能帮助其他 Rust 初学者理解 Rust 编程中很小但非常重要的部分——错误处理。

# 生锈是安全的

Rust 不仅仅是一种语言。这是原则。众所周知，编译器通过借用、移动和生存期来保证对数据的安全访问。

这是有代价的，因为语言变得更难写，但同时，也更难失败。对安全的思考跨越了语言的边界，延伸到了 API。

许多 Rust APIs(主观上)难以使用。API 非常明确地指出了每一个可能出错的步骤，并且您还必须对每一步做出决定。

让我比较两种不同框架/语言的两个 API，你就会明白我想说什么了。

假设我想从 HTTP header 中获取一个头的值，头的名称是“Authorization”。

## **Actix Web / Rust**

```
request.headers().get("Authorization").**unwrap**().to_str().**unwrap**();
```

## **Gin Gonic / Go**

```
context.GetHeader("Authorization")
```

如果头文件不存在，Rust API 将会死机，而 Go API 将返回一个空字符串。此时，你可能会觉得围棋更直观。为什么任何一个头脑正常的人都想破坏程序，只是因为头不见了，对吗？你可能没有错。但是，您如何知道头值实际上是否是一个空字符串呢？如何判断该值是否只包含可见的 ASCII 字符？有人可以尝试通过代码欺骗你的头的解析器吗？你怎么能确定一个字符串是可读的呢？这些问题都可以用 Actix Web API 来回答。

最重要的是，当预期的头文件不存在时，您不必中断程序。我用“打破”而不是“崩溃”这个词，因为它不是崩溃。你永远不会从 Rust 得到一个分段错误。

# 慌！

搞笑的是实际 API 也是`panic!`(带感叹号！).感叹号表示它是一个宏。编译器会在编译时重写这部分代码。

你应该尽可能避免使用`panic!`。它应该被视为致命错误。例如，一个必需的输入丢失了，没有它程序就不能正常运行。终止一个程序是有意义的，这是你唯一可以考虑使用`panic!`的情况。

这与 Go 不同，在 Go 中，死机可以很容易地恢复，在任何代码库中看到死机/恢复都是很常见的。铁锈就不是这样了。

处理 Rust 中非致命错误的惯用方法是使用`Result<T, E>`。我们一会儿会讨论这个问题。

## 展开与预期

如果你是 Rust 新手，你会在书籍、在线教程和其他地方看到许多使用`unwrap`函数的代码示例，有时使用`expect`函数从`Option<T>`或`Result<T, E>` 枚举中解开或提取所需的输出。这两个函数实际上是相同的，除了`expect`函数允许您使用定制的错误消息，而不是使用默认的错误消息。

当没有结果时，这两个函数都会死机。当恐慌发生时，程序将被终止，除非您在一个特殊的闭包中捕捉到它(我们一会儿也会谈到这一点)。所以，如果你不能绝对肯定它不会死机，那么调用这些函数是没有意义的。

如下面的代码片段所示，您可以看到`unwrap`和`expect`函数是如何工作的。实现简单得可笑。我只是从 Rust 标准库中复制了代码。对于`Option`，`unwrap`函数进行模式匹配，当结果为空时会出现混乱。在其他一些语言中，这将是一个异常，就像 Scala、Kotlin 和 Java 中的`NoSuchElementException`一样，可以通过`try..catch`块恢复。铁锈里没有`try..catch`。

如果你看看`Result<T, E>`枚举的`unwrap`函数，你会发现它和`Option<T>`很相似，除了我们匹配的是`Ok`和`Err`类型，而不是`Some`和`None`。

## 从恐慌中恢复过来！攻击

虽然您应该避免使用`panic!`(除非您真的想这么做)，但您仍然可能不得不处理来自库或其他人代码的`panic!`。

有时候，这是不可能改变的，因为这可能超出了你的控制。

我们如何从`panic!`中恢复以避免终止申请流程？`catch_unwind`前来救援。

`catch_unwind`将 panic 转换为`Result<T, E>` enum。这里需要注意的是，您必须执行在`catch_unwind`的闭包内可能会出错的代码。铁锈封闭是棘手的。它不允许您访问(共享)闭包之外的数据，除非您移动和/或克隆您的值。这是出于安全原因，编译器会为您保证这一点。

不需要 GC，也不需要免费跟踪(双关语)。虽然有可能从`panic`中恢复，但建议首先不要使用它。

值得注意的是，恐慌箱中还有其他有用的功能。我不是故意提到他们的。如果你对有什么感兴趣，你可以在这里查看:[https://doc.rust-lang.org/std/panic/index.html](https://doc.rust-lang.org/std/panic/index.html)

# 结果

这是这个故事的主角。`Result`是一个有两个可能值的枚举。如果你熟悉 Scala 或者 Haskell，你会有家的感觉。

这个`Result`枚举类似于一元类型——`Try<T>`和`Either<L, R>`。Rust 是一种系统编程语言，它有一种语法糖，使代码看起来更干净。你没看错:)

不要以为接近汇编的语言总是让你日子不好过。这不是铁锈！唯一接近汇编的是编译后的二进制文件。Rust 代码可以是优雅的，也可以是华而不实的。我将向您展示 Go、Scala 和 Rust 如何处理结果——就像下面的代码练习一样。

```
**enum** Result<T, E> {
 Ok(T),
 Err(E),
}
```

## 练习 1

实现半径为 r 的圆的方程，圆心在原点(0，0)。

我们将用 3 种不同的语言实现这个练习:Go、Scala 和 Rust。我知道这是铁锈的文章。但是，我认为在不同的语言中看到不同的方法是有用的。请注意，未来事情可能会发生变化，因为语言正在发展，它们喜欢相互启发(复制)。

## 去的方式

Go 支持多个返回值，按照惯例，错误是两个返回值中的第二个值。如果你想了解更多，[请阅读同一个人(我)写的这篇很棒的文章。](https://hussachai.medium.com/error-handling-in-go-a-quick-opinionated-guide-9199dd7c7f76)

Go 代码通常简单得可笑，这个也不例外。对于错误处理来说，总是重复和枯燥的。

有一种类固醇叫做 Go+[https://github.com/goplus/gop](https://github.com/goplus/gop)
上面的错误处理可以使用与 Rust 相同的语法重写。

## Scala 方式

Scala 是一种函数式语言，我们都知道它有多优雅。我们将使用`scala.util.Try<T>`封装一个可能出错的结果(如果这令人困惑，请阅读[这个简短而有趣的 Scala 书籍章节](https://docs.scala-lang.org/overviews/scala-book/functional-error-handling.html)，并将返回`Try<T>`的 2 个函数链接在一起，形成`x² + y² = r²`方程。

for-yield 理解是 flatMap/map 的语法糖，可以重写如下。

```
*square*(x).flatMap { x2 => 
  *square*(y).map { y2 =>
    Math.*sqrt*(x2 + y2)
  }
}
```

当你做这样的事情时，你会觉得自己很聪明。它们连在一起的方式就像是用英国口音说话，尤其是当你用 for..屈服。你放弃了先生的头衔。

## 生锈的方法

您在这里看到的代码是本机运行的。没有垃圾收集。这些值存储在一个堆栈中。没有多余的脂肪。最重要的是，它看起来很棒，易于阅读。

？函数调用的最后是模式匹配的语法糖，如下所示。

```
**let** x2 = **match** square(x) {
    *Ok*(i) => i,
    err => **return** err
};
**let** y2 = **match** square(y) {
    *Ok*(i) => i,
    err => **return** err
};
```

你可以将所有可能失败的函数链接在一个扁平的结构中，这种结构像 Go 一样非常易读。语法糖使代码看起来像 Scala 一样整洁。除此之外，你还可以做一些很酷的事情，比如像 Scala 一样使用函子和单子。

```
square(x).**and_then**(|x2| square(y).**map** (|y2| {
    (x2 + y2).sqrt()
}))
```

`and_then`与`flatMap` **相同，**与`map` 是耶..`map` **。**`and_then`和`map`功能都使用引擎盖下的图案匹配。因此，上述所有解决方案应该给你相同的性能特征。归结为个人喜好。

# 是时候面对现实了

大家都喜欢句法糖？它以安全的方式展开结果，并在`Result<T, E>`为`Err<E>`时立即返回调用。它简洁而强大。

在你学会了这个新语法之后，你迫不及待地想在你的小项目中使用它(实际上我不了解你。我指的是我自己)。然后，我面对现实，虽然书中的代码示例看起来很不错，但在我的小项目中并不适用。我稍后会对此进行详细说明。在此之前，我们再做一个练习。

## 练习 2

使用`?`打开 Actix-web 处理器中的结果。

假设我们有一个队列，在使用队列之前，我们必须从一个连接创建一个通道。如果您熟悉 RabbitMQ 或任何其他队列中间件，您会知道这是让队列系统在您的项目中工作的基本步骤。

在第 13 行和第 14 行，我们将 API 调用从`**unwrap**`改为`?` **。现在看起来更好了，因为当它无法创建连接或通道时，我们不想惊慌，因为这些资源可以自行恢复。这个变化看起来非常简单，我感觉真的很好，直到我编译…**

```
**let** connection = pool.get().**await**?;
    | ^ the trait `ResponseError` is not implemented for `deadpool::managed::errors::PoolError<deadpool_lapin::lapin::Error>`
**let** channel = connection.create_channel().**await**?;
    | ^ the trait `ResponseError` is not implemented for `deadpool_lapin::lapin::Error`
```

等一下！

它在书中的例子中工作得很好。为什么它没有编译？

如果你仔细看看错误信息，你就会明白为什么编译器会抱怨。从 API 返回的错误类型没有实现`ResponseError`特征。任何库都不可能实现 HTTP 特有的特性，它是`Actix-web`框架的一部分。

在这种情况下，dead pool Lapin(rabbit MQ 连接池)不希望也不应该对 Actix-web 有具体的依赖。

`?`并不总是有效，因为预期的类型是特定于库的，而不是像`std::error::Error`那样的通用类型。

顺便说一下，这是有道理的。`actix-web` API 不想为你承担 HTTP 错误代码和错误内容。你必须自己指定它，这就是为什么处理函数期望`Result<HttpResponse, Error>` 的原因，其中`Error`在`actix-web`箱中定义如下。

```
**pub** **struct** Error {
  cause: Box<dyn ResponseError>,
}**impl**<T: ResponseError + *'static*> From<T> for Error {
  ...
}
```

## 如何解决这个问题？

简易柠檬榨汁机。你必须将误差从一种类型映射到另一种类型。

当无法建立连接时，正确的 HTTP 错误代码可能是 500(内部服务器错误)，我们可以提供解释错误原因的消息。

```
**let** connection = pool.get().**await**.map_err(|e| {
  error::ErrorInternalServerError("Failed to get a connection")
})?;**let** channel = connection.create_channel().**await**.map_err(|e| {
  error::ErrorInternalServerError("Failed to create a channel")
})?;
```

现在我们可以使用`**?**`魔法了，而且果然有效。

感谢你的阅读，乡巴佬！