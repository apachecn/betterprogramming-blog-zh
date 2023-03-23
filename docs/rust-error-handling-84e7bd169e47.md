# Rust 错误处理

> 原文：<https://betterprogramming.pub/rust-error-handling-84e7bd169e47>

## 如何安全正确地处理 Rust 中的错误

![](img/42eaacdcdc26f5dcab7c992399ede4f7.png)

[马特·阿特兹](https://unsplash.com/@mattartz?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照。

铁锈没有例外。它使用`Result`枚举，它是 Rust 标准库的一部分，用于表示成功(`Ok`)或失败(`Err`):

```
enum Result<T,E> {
    Ok(T),
    Err(E),
}
```

当你在编写函数并需要处理潜在的问题时，你可以使用`[panic](https://doc.rust-lang.org/std/macro.panic.html)` [(宏)](https://doc.rust-lang.org/std/macro.panic.html)退出程序。这应该很少见。

# `panic`

这里，如果随机数大于 5，我们称之为`panic!`。

为了让我们的意图更加清晰，我们可以返回一个`Result`。

# 使用`Result`

在这种情况下，如果数字小于或等于 5，我们返回`Ok`变量。否则，我们返回一个带有`String`消息的`Err`。

我们可以使用一个`[Option](https://doc.rust-lang.org/std/option/enum.Option.html)`返回类型，但是在这种情况下，我们使用`Result`，因为`a number greater than five`是一个错误的场景(在我们虚构的用例中)。

现在我们有了一个返回`Result`的方法，让我们研究一下调用者如何使用这个方法。有几个选项:

*   使用`panic!`。
*   承认问题并处理它。
*   传下去。

# `panic`(又来了！)

这是处理问题最简单的方式，但往往是最不可取的方式。恐慌是个糟糕的主意。我们可以让它变得更好。

## **使用 expect**

`[expect](https://doc.rust-lang.org/std/result/enum.Result.html#method.expect)`是捷径。这是一个在`Result`上可用的方法，如果可用的话，它返回来自`Ok`变体的内容。否则，它用传入的消息和`Err`内容`panics`。

```
fn caller() -> String {
    let n = gen2().expect("generate failed!");
    n.to_string()
}
```

## **使用展开**

`unwrap`类似于`expect`，除了它不允许您指定自定义消息(显示在`panic!`上)。

```
fn caller() -> String {
    let n = gen2().unwrap();
    n.to_string()
}
```

我们可以用`unwrap_or()`做得更好一点，在出现`Err`的情况下用它返回一个默认值:

```
fn caller() -> String {
    let zero = gen2().unwrap_or(0);
    zero.to_string()
}
```

在这种情况下，如果出现错误，我们将返回`0` ( `String`表单)。

# 处理问题

在这种情况下，我们在`Result`上`match`，返回一个`String`(使用`i.to_string()`)，或者返回`Err`内容(在这种情况下恰好也是一个`String`)。

# 不是我的问题…传下去

如果您不想处理这个问题，而需要将它传播给调用者(以稍微不同的方式)，该怎么办呢？

不返回显式的`String`，你可以返回一个`Result<String,String>`，其中`Ok`变量将包含一个`String`结果，而`Err`变量也将包含一个解释错误细节的`String`结果。

注:A `String`也用于表示错误(`Err`)。这只是为了保持事情简单。通常，您会使用一种特殊的错误类型(例如`std::io:Error`)。

有一种方法可以做到:

我们将返回类型更改为`Result<String, String>`，调用函数和`match`(返回一个`Result<i32,String>`)。我们所需要的就是确保我们返回了`i32`的`String`转换。所以我们匹配返回的`Result`。如果是`Ok`，我们返回另一个`Ok`，包含`i32`的`String`版本(即`Ok(i) => Ok(i.to_string())`)。如果有错误，我们简单地返回它。

# `?`操作员

我们可以通过使用`?`操作符来进一步简化。这类似于`match`过程，其中它返回`Result`的`Ok`变量中的值，或者通过返回`Err`本身退出。在我们的例子中，它的用法如下:

```
fn caller() -> Result<String, String> {
    let n = gen2()?;
    Ok(n.to_string())
}
```

该方法被调用—注意末尾的`?`。它所做的只是返回`Ok`变量中的值，我们使用`Ok(n.to_string())`返回它的`String`版本。这就照顾到了`Result`的一半(与`Ok`的幸福之路)。另一半呢(`Err`)？就像我之前提到的，使用`?`，所有的事情都在幕后处理，并且`Err`会随着它的值自动从函数中返回！

请注意，如果返回`Result`或`Option`或实现`std::ops::Try`的其他类型，则可以使用`?`运算符。

如果`Err`型号不同呢？您可能会遇到要求您在`Result`中返回不同错误类型的情况。在这种情况下，您需要为您的错误类型实现`std::convert::From`。这不会起作用，因为`Result<String, NewErr>`是返回类型:

在这种情况下，您需要告诉 Rust 如何从`String`转换到`NewErr`，因为`gen2()`函数的返回类型是`Result<String,String>`。这可以通过以下方式完成:

```
impl std::convert::From<String> for NewErr {
    fn from(s: String) -> Self {
        NewErr { ErrMsg: s }
    }
}
```