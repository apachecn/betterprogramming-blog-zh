# Rust 中的枚举和模式匹配

> 原文：<https://betterprogramming.pub/rust-enums-and-pattern-matching-177b03a4152>

## 铁锈基础知识

![](img/bd9fe5a7ea5bdfd6fc7ef6c2ab2c7255.png)

帕特里克·托马索在 [Unsplash](https://unsplash.com/s/photos/pattern?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的照片

这篇文章的代码可以在 [GitHub](https://github.com/abhirockzz/learning-rust) 上找到。

# 列举型别

从某种意义上说，`enum`类似于`struct`,因为它是一种可以有多个变量的类型(与类及其实例的类型相同)。但是`enum`的变体是固定的，并且在`enum`本身中定义。

一个`Player`可以用一个`struct`来表示:

```
struct Player {
    name: String,
    rank: i32,
}
```

您可以实例化一个`Player`的多个实例。但是我们来看一个稍微小众一点的类型。

```
enum PlayerAccountType {
    Free,
    Paid,
}
```

假设你只有两种账户——免费账户和付费账户。你可以用一个`enum`来表示这个概念。而且，由于它只是另一种类型，你可以在`Player`结构中使用它。

```
struct Player {
    name: String,
    rank: i32,
    acc_type: PlayerAccountType,
}
```

您可以用一个`struct`来表示`PlayerAccountType`类型，但是您不必这样做，因为它可能的变体已经是已知的了。

您可以使用`::`引用枚举变量。例如，为了实例化具有付费账户的`Player`:

```
let paid_member = Player{acc_type: PlayerAccountType::Paid, name: String::from("john"), email: String::from("john@doe.com")};
```

也可以向`enum`变量添加数据。例如:

```
enum foo {
    foo1(String),
    foo2(String),
}
```

注意:

*   除了数据，`enum` s 还可以有方法
*   `[Option<T>](https://doc.rust-lang.org/std/option/enum.Option.html)`和`[Result<T,E>](https://doc.rust-lang.org/std/result/enum.Result.html)`被广泛使用`enum` s，是 [Rust](https://www.rust-lang.org/) 标准库的一部分。

# 模式匹配

Rust 提供了`match`关键字，其行为方式与`switch`语句相同(Rust 没有`switch`)。在探索`match`之前，让我们看一个简单的`if-else-if`例子:

这里没有惊喜！但是你*不能*用`match`写这个。

这是不可能的，因为`match`操作符需要一个值来执行匹配过程。它不会为您执行给定的表达式。

**更新**:根据[此注释](https://medium.com/@rpring9/your-if-else-if-is-possible-with-guards-843c312d281)，使用[防护装置](https://doc.rust-lang.org/stable/rust-by-example/flow_control/match/guard.html)可以实现`if-else-if`。谢谢， [@rpring9](http://twitter.com/rpring9) ！

一般格式如下:

*   您有一个想要与之匹配的值和一堆可能的选项，也称为`match arm`。
*   每个机械手都是要匹配的模式和匹配成功时要运行的相应`expression`的组合。

所有这些听起来都很抽象，所以让我们来看一个用`enum`进行`match`运算的例子。

## 匹配枚举

让我们从定义一个`enum`开始:

```
enum Choice {
    One,
    Two,
    Three,
}
```

并与`Choice` s 匹配。

这里，变量`choice`是被`match` ed 的值，后跟三个`match`臂。对于每个手臂，都有一个模式，例如`Choice::One`和相应的表达式，例如由`=>`分隔的`println!("Option 1")`。

在这种情况下，输出将是`Option 1`。

## 匹配一个选项

Rust 标准库提供了`Option` `enum`，其目的是定义一个类型来表示一个场景，其中*可能有值，也可能*没有值。

这使得代码显而易见，并且比使用`null`、`nil`或类似选项来表示缺少值更好

这类似于 Java 中的`[Optional](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/Optional.html)` [。](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/Optional.html)

```
pub enum Option<T> {
    Some(T),
    None,
}
```

不要担心`T`符号。只需理解它是一个`generic`类型参数，允许`enum`与各种类型一起工作，而不是将其绑定到一个特定的类型，例如`String`。

我们用一个例子来理解这个。

这是一个简单的 CLI 程序，它接受来自用户的参数(名称),并使用它来显示问候语。

如果用户没有传递任何东西，它使用默认的问候。这是一个如何使用`Option`的合理例子，因为我们可能有也可能没有用户传入的参数(这是可选的！咄！).

下面是相应的函数:

`parse_name_arg`函数将传入的参数转换成向量(`Vec<String>`)并返回一个`Option`。

如果一个参数没有被传递，它返回`None`或`Some`(带有值)——两者都是`Option`的变体。现在我们可以这样使用这个函数:

```
let name = parse_name_arg();
match name {
   Some(n) => println!("Hello {}!", n),
   None => println!("Hello there!"),
}
```

我们将`parse_name_arg`的结果存储在一个名为`name`(这是一个`Option`)的变量中，匹配不同的可能性，并相应地执行问候。

如果你将`john`作为一个参数传递，你将返回`Hello john!`。如果你没有传递一个参数，你将得到一个通用的问候`Hello there!`

要尝试这一点，只需[克隆 GitHub repo](https://github.com/abhirockzz/learning-rust) ，切换到正确的目录，即`cd learning-rust/enums-match`，并使用`cargo run`。要传递一个论点，可以用`cargo run <your argument`。

枚举的值在`match`子句中可用(也称为`arm`)。这就是为什么我们能够使用`Some(n) => println!("Hello {}!", n),`提取传入的参数。

## **使用 let with match**

将问候语存储在变量中怎么样？您也可以将`let`与`match`一起使用来返回值。让我们稍微调整一下程序。

```
let greeting = match name {
        Some(n) => n,
        None => String::from("there"),
    };
    println!("Hello {}!", greeting)
```

我们将比赛结果存储在一个名为`greeting`的变量中，并与`println!`宏一起使用，这使得程序更加简单！

# 用尽你的选择！

默认情况下，`match`要求您履行或说明*所有*可能的选项。让我们看一个例子。这是另一个`enum`，我们将对比它的值。

这就是我们想要的匹配方式(在这种情况下，我们只对`Friday`感兴趣):

```
let today = Days::Friday;
match today {
   Days::Friday => println!("thank god its Friday!"),
}
```

但这不会编译。您将看到如下错误:

```
error[E0004]: non-exhaustive patterns: `Monday`, `Tuesday`, `Wednesday` and 3 more not covered
```

为了解决这个问题，我们可以使用`_`占位符:

```
match today {
        Days::Friday => println!("thank god its Friday!"),
        _ => (),
    }
```

这仅仅是*忽略了*其他的可能性。在这种情况下，你也可以使用`if let`来保持简洁。

```
let today = Days::Monday;
    if let today = Days::Monday {
        println!("its Monday already! :(");
    }
```

# 结论

不要忘记查看这些资源:

*   [Rust 编程语言](https://doc.rust-lang.org/book/#the-rust-programming-language)书
*   [锈标准库](https://doc.rust-lang.org/std/)文档
*   [铁锈示例](https://doc.rust-lang.org/stable/rust-by-example/)

这就是对`enum`的快速浏览以及如何使用模式匹配的全部内容。敬请关注更多内容！