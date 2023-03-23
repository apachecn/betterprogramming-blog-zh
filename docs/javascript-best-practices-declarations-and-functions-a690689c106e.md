# JavaScript 中声明和函数的常见错误

> 原文：<https://betterprogramming.pub/javascript-best-practices-declarations-and-functions-a690689c106e>

## JavaScript 太宽容了，但这并不意味着你必须宽容

![](img/3ec3997256b90e5b63e353bb9d0b17cd.png)

[NeONBRAND](https://unsplash.com/@neonbrand?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照。

JavaScript 是一种非常宽容的语言。编写可以运行但有错误的代码很容易。

在这篇文章中，我们将看看在编写 JavaScript 代码时需要注意的一些最佳实践，包括在`switch`语句中声明和在函数中返回内容。

# switch 语句中没有 case 或 default 子句中的词法声明

以`let`、`const`、`function`和`class`开头的词法声明(如表达式)不应该出现在`switch`块的`case`或`default`子句中，因为它们只有在被赋值时才会被初始化。

这只发生在未达到的`case`中。因此，为了防止访问`switch`块中未初始化的变量，我们应该包装`case`和`default`语句来创建一个块，这样它们就不能作为未初始化的变量在外部被访问。

例如:

在上面的代码中，因为`foo`是 2，所以`case 2`块将会运行，这将会给我们带来“初始化前不能访问‘x’”的错误。

为了防止这种错误，我们应该将`case`和`default`语句块化，这样很明显我们不能访问块外的变量:

我们在`case`和`default`语句中添加了花括号来创建块。我们将`z`函数切换为函数表达式，因为函数表达式不应该在块内。

# 不要在构造函数中返回值

如果我们在块中返回值，可能是错误的。如果我们在构造函数中有一个`return`语句，它将返回那个值而不是类实例。

例如，如果我们写:

那么`foo`就是`{foo: “bar”}`。即使我们将`this.baz`设置为一个值，它也不会在`foo`中显示出来。

这可能是一个错误，因为我们可能想获得`Foo`实例，而不是作为`foo`的值在构造函数中返回的对象。

因此，我们应该写:

然后我们看到`foo`的值是`{baz: “baz”}`，正如我们所料。

但是，我们仍然可以使用`return`进行如下流量控制:

# 没有看起来像除法的正则表达式

看起来像除法的正则表达式是不明确的。看起来我们试图做除法，但实际上我们试图定义一个正则表达式。

例如，不写:

```
const regex = /=bar/;
```

我们应该写:

```
const regex = /[=]bar/;
```

它们做同样的事情，但是当我们实际定义一个正则表达式时，看起来不像是在做除法。

# else 块内没有返回

在`else`模块中添加`return`是多余的。这是因为如果上述`if`块中的条件为`false`，则`else`块中的`return`语句总是运行。

例如，如果我们有:

即使我们没有`else`块，它也会运行。因此，我们不需要`else`块。

因此，我们可以如下消除`else`块:

它做同样的事情，但是没有`else`块。由于`x`为`false`，所以`if`块不运行，而`return 'bar'`运行。

# 结论

概括一下:

*   如果我们不将`switch`中的声明转换成块，就可以在`case`或`default`语句之外访问它们，所以我们应该将`case`和`default`语句转换成块，以消除任何混淆。
*   在 JavaScript 类的`constructor`中返回值将在`return`语句中返回，而不是在类实例中。所以退东西很可能是个错误。
*   应该改变看起来像除法的正则表达式，使它看起来不像除法，这样每个人都清楚这是正则表达式而不是除法。
*   最后，`else`块中的`return`语句是多余的，因为如果上面的`if`块中的条件是`false`，那么`return`语句将会运行。