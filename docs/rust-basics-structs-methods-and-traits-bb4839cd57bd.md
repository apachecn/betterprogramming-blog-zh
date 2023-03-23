# Rust 基础:结构、方法和特征

> 原文：<https://betterprogramming.pub/rust-basics-structs-methods-and-traits-bb4839cd57bd>

## 快速浏览 Rust，用简单的例子来演示概念

![](img/63bc162f8396bd8589c33a84dec14aac.png)

由[马克斯·尼尔森](https://unsplash.com/@maxcodes?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/how?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

这一块的代码可在 [GitHub](https://github.com/abhirockzz/learning-rust) 上获得。

# 结构

Rust 中的一个`struct`和 Java 中的一个`Class`或者 [Golang](https://golang.org/) 中的一个`struct`是一样的。它是一个命名的类型，你可以给它分配状态(属性/字段)和行为(方法/函数)。

下面是一个带有字段的`struct`:

```
struct Programmer {
    email: String,
    github: String,
    blog: String,
}
```

要实例化一个`Programmer`，你可以简单地:

```
let pg1 = Programmer {
        email: String::from("abhirockzz@gmail.com"),
        github: String::from("https://github.com/abhirockzz"),
        blog: String::from("https://dev.to/abhirockzz"),
    };
```

# 方法

方法是与给定类型关联的行为。方法中的第一个参数总是`self`，它表示方法被调用的实例。

我们给`Programmer`加个方法吧。为此，我们需要使用一个`impl`模块:

```
impl Programmer {
    fn is_same_as(&self, other: Programmer) -> bool {
        return self.email == other.email;
    }
}
```

`is_same_as`方法接受对在(`&self`)和另一个`Programmer`实例上调用的实例的引用。要调用它，创建一个`Programmer` ( `pg2`)的另一个实例，并将`pg1`与它进行比较。

## 自我类型

我们使用`&self`作为`is_same_as`的参数。这样，我们将只传递一个引用，函数将*而不是*拥有该值——只借用它(参见 [Rust:所有权和借用](https://medium.com/better-programming/rust-ownership-and-borrowing-9cf7f081ade0))。也可以使用`self`和`&mut self`。

您可以使用`self`，但是要小心它会将所有权传递给函数。我们来看看这个函数。

您可以像使用`pg2.details();`一样使用`pg2`实例来调用它，您应该得到:

```
Email: abhirockzz@gmail.com,
GitHub repo: https://github.com/abhirockzz,
Blog: [https://medium.com/@abhishek1987](https://medium.com/@abhishek1987)
```

如果您试图再次使用`pg2`(例如`pg2.is_same_as(&pg1);`)，您将得到一个编译器错误:

```
error[E0382]: borrow of moved value: `pg2`
```

如果您想改变`Programmer`实例，可以如下使用`&mut self`:

如果您没有将变量(`pg3`)标记为`mut`(可变)，您将得到一个编译器错误:

```
error[E0596]: cannot borrow `pg2` as mutable, as it is not declared as mutable
```

## 与结构相关的其他函数

您可以添加与`struct`的实例相关联的`associated functions`——可以把它想象成 Java 中的`static`方法。这些通常用作构造函数。

这与 Java 中构造函数的使用方式不同，但类似于 Go 方法。

你可以这样使用它:

```
let pg3 = Programmer::new(
        String::from("abhirockzz@gmail.com"),
        String::from("https://github.com/abhirockzz"),
        String::from("https://medium.com/@abhishek1987"),
    );
```

注意，我们使用`::` ( `Programmer::new`)来访问`struct`(本例中是一个函数)的`associated`成员。

同一个`struct`可以使用不同的`impl`模块。

# 特征

Rust 中的`Trait`类似于 Java 等其他语言中的`Interface`。它们帮助定义一组或多组行为，这些行为可以由不同的类型以它们自己独特的方式进行`implemented`。

Rust 中实现`Trait`的方式与 Java 中的方式非常相似。在 Java 中，可以使用`implements`关键字，而 Rust 使用`impl`。

接口和实现它的类型之间有显式的关联。这与 Go 有很大的不同，在 Go 中你不需要声明你正在实现哪个接口——如果你已经实现了所需的行为，编译器会很高兴的。

让我们从定义一个`Trait`开始。

```
trait PrettyPrint {
    fn pretty_print(&self);
}
```

并付诸实施。

不要担心例子的细节，只关注关键部分。

这很简单——使用`impl`关键字，后跟您正在实现的特征，并在`for`关键字后包含实现该特征的类型。现在，您可以像使用任何其他方法一样使用它:

```
let pg = Programmer::new(...);
pg.pretty_print();
```

## 使用一个库特征

Rust 的标准库提供了许多你可以使用的`Trait`。让我们实现`[std::fmt::Display](https://doc.rust-lang.org/std/fmt/trait.Display.html)`特征。这是面向用户的输出，例如，我们可以使用`println!`宏。

不要担心实现的细节和类型，比如`std::fmt::Formatter`等。现在，只要理解你可以从 Rust `std::fmt`模块中实现`Display`特征。

从现在开始，您可以像这样使用带有`println!`的`Programmer`的实例。

```
let another_pg = Programmer::new(...);
println!("programmer details: {}", another_pg);//output:
programmer details: (abhirockzz@gmail.com, https://github.com/abhirockzz, [https://medium.com/@abhishek1987)](https://medium.com/@abhishek1987))
```

## 免费赠品

Rust 提供了很多有用的特性，你可以免费使用！这意味着你不需要显式地实现它们(如果你想的话，你当然可以)。方法是使用`#[derive]`属性。

一个`attribute`只是一段元数据，你可以把它应用于结构等等。它们让我想起了 Java 注释。

这里有一个例子。我们可以在`Programmer`上应用`std::fmt::Debug`特征，然后和`println!`一起使用。

这类似于我们对`Display`所做的，但是关键的区别在于它不可能`derive`到`Display`(你*让*去实现它)。您所需要做的就是将`#[derive(Debug)]`添加到`Programmer`结构中:

简单地使用`:?`格式来利用默认的`Debug`功能。

如果你添加一个`#`到组合中，你可以免费得到漂亮的打印…耶！

还有其他效用性状如`Eq`、`Clone`、`PartialEq`等。哪能`derive`。

## 使用特征

为了利用特征，您应该能够从函数和方法中接受和返回它们，以利用“一般”行为。我们可以编写一个接受实现`PrettyPrint`的类型的函数。

注意`impl`已经添加到参数中。

也可以返回一个`PrettyPrint`:

## **默认方法**

可以在 trait 本身中提供方法的默认实现(如果需要，可以覆盖这些实现)。这些方法也可以调用其他 trait 方法(默认或非默认)。

请继续关注另一个关于 Rust 基础知识的博客！