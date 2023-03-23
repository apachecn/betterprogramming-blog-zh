# 用 Java 中的 Optional 避免空指针异常

> 原文：<https://betterprogramming.pub/avoiding-the-null-pointer-exception-with-optional-in-java-96cd75d9b058>

## 如何避免 Java 中的“十亿美元错误”

![](img/485a07882679280dc06aee14620d1c11.png)

由 [Barnabas Hertelendy](https://unsplash.com/@barnabas_1?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

1964 年，英国计算机科学家东尼·霍尔发明了空指针引用。

空指针异常是生产异常中最多的错误。它是用许多编程语言实现的，包括 C、C++、C#、JavaScript、Java 等等。

为解决这个问题而损失的财政资源、时间和人力资源促使霍尔称之为“十亿美元的错误”

Java 是实现空指针引用的编程语言之一。如果你一直在用 Java 进行开发，我相信你一定见过很多。不管你是 Java 新手还是有十年经验都没关系。您总有可能会遇到空指针异常错误。

# Java 中可选

`Optional`是 Java 8 中引入的 API。如果使用正确，它可以解决空指针异常的问题。

`Optional` API 实现函数式编程，使用函数式接口。如果想了解更多 Java 中的函数式编程，可以看我的另一篇文章，[Java 中的函数式编程，](https://medium.com/better-programming/functional-programming-in-java-explained-ae396e9e516f)讲解。

在我们继续之前，请注意我在本文中使用 Java 11 作为例子。如果您使用的是不同版本的 Java，有些方法可能不存在或行为完全相同。

# 空可选

使用`Optional` API 时，空的`optional`是避免空指针异常的主要方法。

在`Optional`的流程中，一个 null 将被转换成一个空的`Optional`。空的`Optional`不会被进一步处理。这就是我们在使用`Optional`时避免`NullPointerException`的方法。

在本文的后面，我们将进一步了解空的`Optional`的行为。

# 创建可选对象

有三种方法可以初始化一个`Optional`对象:

*   `Optional.of(T)`
*   `Optional.ofNullable(T)`
*   `Optional.empty()`

## 可选的

`Optional.of`接受任何参数中有不可空值的类型。要用`Optional.of`创建一个`Optional`对象，我们只需在它的参数中传递一个值。

向`Optional.of`传递值时要非常小心。记住`Optional.of`在其参数中不接受空值。如果你试图传递一个空值，它将产生一个`NullPointerException`。

## 可选. ofNullable

`Optional.ofNullable`类似于`Optional.of`。它接受任何类型。不同的是，使用`Optional.ofNullable`，您可以向它的参数传递一个空值。

当使用空对象初始化`Optional.ofNullable`时，它将返回一个空的`Optional`。

## 可选。空

空的`Optional`可以通过使用`Optional.empty()`进行初始化。

# 访问可选的

有一些方法可以得到一个`Optional`的值。

## 得到

一个非常简单的方法。如果存在的话，`get`方法将返回`Optional`的值，如果该值不存在，则抛出一个`NoSuchElementException`。

## 奥利斯

如果您想在`Optional`为空时使用默认值，您可以使用`orElse`方法。

## orElseGet

`orElseGet`非常类似于`orElse`的方法。只是`orElseGet`接受`Supplier<T>`作为它的参数。

## orElseThrow

`orElseThrow`将返回`Optional`的值，如果`Optional`的值为空，则抛出异常。

# 处理可选的

一个`Optional`有很多种加工转化方式。在本节中，我们将学习常用的方法。

正如我在文章开头所写的，一个空的`Optional`不会在流中被处理。我们可以从本节的例子中看到这一点。

## 地图

`map`是处理`Optional`对象时最常用的方法。它接受`Function<? super T, ? extends U>`作为其参数，并返回一个`Optional<U>`。这意味着您可以使用带有任何类型参数的`Function`,返回值将被包装到`map`方法中的`Optional`。

如果您试图在`Function<? super T, ? extends U>`中返回一个`null`值，那么`map`方法将返回一个空的`Optional`。

空的`optional`不会被`map`处理。我们可以通过以下测试来证实这一点:

## 平面地图

这与`map`类似，但`flatMap`不会将`Function`的返回值包装到`Optional`。`flatMap`方法接受`Function<? super T, ? extends Optional<? extends U>>`作为其参数。这意味着您需要定义一个接受任何类型并返回一个`Optional`的`Function`。

当你的代码调用另一个返回`Optional`对象的方法时，你通常会使用`flatMap`方法。

## 过滤器

在前面的`flatMap`示例中，我们使用了声明式风格来区分`getString`方法的返回值。但是我们实际上可以使用函数式风格的`filter`方法。

## 如果存在

`ifPresent`方法接受一个`Consumer`，只有当`Optional`不为空时才会执行。

# 要避免的事情

如果你想在你的代码中使用`Optional`，你需要避免一些关键的事情。

## 不要创建接受可选的方法

创建一个接受`Optional`作为参数的方法可能会引入一个它想要解决的问题`NullPointerException`。

如果使用带有`Optional`参数的方法的人没有意识到这一点，他们可能会向该方法传递一个`null`而不是`Optional.empty()`。处理一个空值会产生一个`NullPointerException`。

## 不检查就获取值

如果你使用`Optional`，那么你应该尽可能避免使用`get`方法。如果出于某种原因您仍然想要使用它，请确保您首先用`isPresent`方法检查它，因为如果您在空的`Optional`上使用`get`，它将产生一个`NoSuchMethodException`。

# 结论

谢谢你一直读到最后！`Optional`是每个 Java 开发人员都应该知道的强大特性。如果你从头到尾正确使用`optional`特性，那么我敢肯定你不会再遇到`NullPointerException`了。

`Optional`也被用作其他大型库的基础，如 [Reactor](https://projectreactor.io/) 和 [RXJava](https://github.com/ReactiveX/RxJava) ，所以了解`Optional`如何工作也将有助于你理解它们。

您可以在下面的文章中找到带有示例的存储库:

[](https://github.com/brilianfird/java-optional) [## brilianfird/Java-可选

### Java 的可选演示。在 GitHub 上创建一个帐户，为 brilianfird/java-optional 开发做贡献。

github.com](https://github.com/brilianfird/java-optional) 

# 参考

1.  [空指针引用:十亿美元的错误](https://medium.com/@hinchman_amanda/null-pointer-references-the-billion-dollar-mistake-1e616534d485)
2.  [https://en . Wikipedia . org/wiki/Tony _ Hoare #道歉 _ 和 _ 撤回](https://en.wikipedia.org/wiki/Tony_Hoare#Apologies_and_retractions)
3.  [https://docs . Oracle . com/javase/8/docs/API/Java/util/optional . html](https://docs.oracle.com/javase/8/docs/api/java/util/Optional.html)