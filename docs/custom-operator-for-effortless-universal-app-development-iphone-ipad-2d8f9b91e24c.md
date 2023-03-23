# 用于构建通用 Apple 应用程序的自定义操作符

> 原文：<https://betterprogramming.pub/custom-operator-for-effortless-universal-app-development-iphone-ipad-2d8f9b91e24c>

## 利用运算符重载来构建自定义运算符，以简化 iPhone iPad 应用程序开发

![](img/420f3bdb282ca5c8e77ea99efa5a12d0.png)

杰里米·贝赞格在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 先决条件

我建议你通读一下我在通读本文之前写的[操作符重载](https://medium.com/@nikhil.vinod/understanding-operator-overloading-in-swift-1f2cad16243c)一文。在通读这篇文章之前，我们需要有关于[操作符重载](https://medium.com/@nikhil.vinod/understanding-operator-overloading-in-swift-1f2cad16243c)的基础知识。

我见过许多人通过编写`ternary operator`或`if`块来编写相同的样板检查代码来管理 iPhone 和 iPad。相反，我们可以使用自定义操作符轻松实现区分符。

# 履行

为此，我们将需要`precedencegroup`
用于所有自定义中缀运算符，它属于一个优先组。优先组指定了一个运算符相对于其他中缀运算符的优先级，以及该运算符的结合性。

`**higherThan**` `<>`是相邻符的集合。而且它的优先级是无序的，因为它可以取`AssignmentPrecedence`。因此，我们将`higherThan`值指定为`AssignmentPrecedence`

`**assignment**`当设置为`true`时，相应优先组中的操作符在可选链接期间使用与标准库中的赋值操作符相同的分组规则。

否则，当设置为`false`或省略时，优先组中的运算符遵循与不执行赋值的运算符相同的可选链接规则。

例如，`testing?.number ++ 5`是我们用`++`操作符执行的操作。如果我们给赋值为假，我们会得到一个错误，如果我们给赋值为真，那么它会给我们一个值`Optional(previousValue + 5)`

现在我们将在`IdiomPrecedence`中嵌入`<>`，使其在可选链接期间可赋值，赋值值设置为 true。

```
infix operator <>: IdiomPrecedence
```

现在我们将为`<>`操作符添加一个函数。

现在，我们可以很容易地使用这个操作符`<>`来区分 iPhone-iPad 或为其分配特定的值，如下所示。

```
nameLbl.font = UIFont.systemFont(ofSize: 20) <> UIFont.systemFont(ofSize: 35)
```

在这里，iPhone 的`nameLbl`将被设置为 20，iPad 的`fontSize`将被设置为 35。同样，您可以以多种方式使用`<>`操作符进行微分器赋值。

我希望你已经理解了我们如何使用自定义操作符来设置 iPhone iPad setters。下面是[项目链接](https://github.com/Nikilicious09/-Operator)同。