# Golang 型断言

> 原文：<https://betterprogramming.pub/golang-type-assertion-d5517d81c366>

## 使用接口来确定行为

![](img/247856218f934d46ec96da0cbe326faf.png)

作者图片

Golang 提供了一种检查接口值是否属于特定类型的简单方法。这被称为类型断言，它通常用于确保一个接口值满足另一个接口，或者用于找到接口的具体类型。

断言值是否属于特定类型的语法如下所示:

```
var age interface{}
age = 20
str := age.(string)
```

变量`age`是类型`interface{}`的，这意味着它可以保存任何类型。赋给变量的具体类型是`int`，但是在这个例子中，类型断言是针对`string`类型的。这里的问题是接口的具体类型不是字符串，所以运行这个程序会产生运行时错误。

要解决这个问题，请使用“逗号，ok”习语，它允许检查接口类型而不会产生错误。

```
str, ok := age.(string)
```

如果具体类型不是字符串，那么`ok`变量将是`false`，而`str`变量将具有用于作出断言的类型的零值。在这种情况下，字符串的零值是一个空字符串。

如果具体类型是字符串，`ok`变量将是`true`，`str`变量将具有指定类型的值。

让我们看一个更详细的例子:

这里，`str`变量的类型是`StringSelecter`，它是一个有两个方法`String()`和`Validate()`的接口。

还有一种叫做`MyString`的类型，它的具体类型是 a `string`。这个类型有两个值接收器方法，`String()`和`Validate()`。由此推断该类型满足`StringSelecter`接口。

在第 12 行，打印出`str`变量的类型。

当调用函数`typeAssert`时，有趣的部分就来了。这个函数将一个`interface{}`作为它唯一的参数。该函数需要确保作为参数传入的值的具体类型满足`fmt.Stringer`接口，这样它就可以安全地调用该类型的`String()`方法。

为此，它使用类型断言，使用“逗号，ok”语法。如果类型断言成功，该函数将对值调用`String()`方法。

运行上面的程序确实会输出第 19 行的打印语句，因为参数满足`fmt.Stringer`接口。

如果程序也试图调用`typeAssert`函数中的`Validate()`方法，特别是在第 19 行之后，该怎么办？将出现以下运行时错误:

```
stringer.Validate undefined (type fmt.Stringer has no field or method Validate)
```

这是为什么呢？为了理解这一点，我们先来看看前面两个例子的区别。

在第一个例子中，类型断言是针对一个具体的类型`string`。在第二个例子中，类型断言是针对一个`interface`类型的。

根据用于进行断言的类型，结果值会有所不同。

如果断言使用具体类型，并且断言成功，那么结果将是被断言的变量的值。

如果断言使用接口类型，并且断言成功，那么结果将是被断言的接口类型。

前面的示例在调用`Validate()`方法时失败，因为断言返回了一个接口类型`fmt.Stringer`，而该接口没有实现`Validate()`方法。

如果`typeAssert`函数使用`StringSelecter`接口，那么结果类型将具有可访问的`String()`和`Validate()`方法。

接口类型断言的这一特性主要用于扩展接口值拥有的方法集，以及确定接口值的行为。

让我们检查 Go 源代码中的`copyBuffer`函数，它使用类型断言来确定调用哪个方法来进行复制。

使用`io`包中的`Copy`时使用该功能。

```
bytes, err := io.Copy(ioutil.Discard, resp.Body)
```

上面的`copyBuffer`函数使用类型断言来确定哪个方法可以用来执行复制操作。当源满足`WriteTo`接口时，调用`WriteTo`方法进行复制。当源满足`ReaderFrom`接口时，调用`ReadFrom`方法进行复制。否则，它会运行不同的策略来进行复制。

这是从类型接口`Reader`建立行为，并假设如果这两个接口中的任何一个被`src`、然后是`WriteTo`和`ReadFrom`满足，方法就适合进行复制。

还有另一种做类型断言的方法，使用 Golang 中的`switch`这篇文章中的 [Switch 语句解释了](https://levelup.gitconnected.com/switch-statement-in-golang-explained-9cd3a85aa19d)，描述了如何做！

如果您是 Golang 的新手，以下文章可能会有所帮助:

[](/go-api-design-with-protocol-buffers-and-grpc-991838e4852b) [## 使用协议缓冲区和 gRPC 的 Go API 设计

### 基于社交媒体应用的分步指南

better 编程. pub](/go-api-design-with-protocol-buffers-and-grpc-991838e4852b) 

我希望你喜欢阅读这篇文章，并学到一些新东西。欢迎分享您的宝贵意见。