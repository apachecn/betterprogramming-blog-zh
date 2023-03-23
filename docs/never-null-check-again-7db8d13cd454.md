# 不再进行空值检查

> 原文：<https://betterprogramming.pub/never-null-check-again-7db8d13cd454>

## 开始在像 Java 这样的语言中使用可选类型

![](img/559869f92cb43760957f542db9192291.png)

由[萨法尔·萨法罗夫](https://unsplash.com/@codestorm?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 问题是

在允许变量中有`null`值的语言中，开发人员必须显式地进行空值检查，以防止他们的程序出现运行时错误。

虽然空值检查很重要，但它可能会成为一个非常乏味的过程，并且经常会损害可读性。它通常需要大量嵌套的代码，并且妨碍了业务逻辑。

典型的空值检查代码可能类似于下面的代码片段:

在这个简单的例子中，我们看到一个处理来自`Person`对象的`name`字段的基本方法变成了一个复杂的嵌套代码块，需要几个阶段的空值检查。

代码片段的业务逻辑只是处理人名，隐藏在空检查层中，开发人员必须进行空检查才能创建有弹性的程序。

# 更好的方法—可选类型

我们可以利用`Optional`类型，而不是每次处理一个新值时都必须进行冗长的空检查。

`Optional<T>`基本上可以被认为是一个潜在地保存类型为`T`的值的容器。所以，`Optional<T>`要么持有`T`要么持有`null`。

`Optional`类提供了几个方法，使得处理潜在的空值更加显式和可读。这些方法的一些例子包括:

*   通过静态的`Optional.ofNullable(T value)`方法创建一个`Optional`对象。
*   通过`optional.isPresent()`方法检查`Optional`是否为空值。
*   通过`optional.orElse(T defaultValue)`方法，如果`Optional`为空，则返回默认值。
*   通过`.map()`和`.flatMap()`方法提取并转换`Optional`中的值。这两种方法都接受一个函数作为参数，该参数应用于`Optional`对象的值。这两种方法的不同之处在于，`map`旨在接受一个将值映射到值的函数，而`flatMap`接受一个将值映射到`Optional`的函数。这种区别很有用，因为如果您的映射函数已经返回了一个`Optional`，那么`map`方法将创建`Optional<Optional<T>>`，而`flatMap`将取消嵌套(展平它们)并生成`Optional<T>`。

# 丰富

现在，回到我们最初的代码片段，我们可以使用`Optional`值来显著地清理它。

如果我们重写`getPerson`和`getName`方法来返回`Optional`类型，新代码将如下所示，假设`processName`返回一个`String`:

```
return getPerson()                   // returns Optional<Person>
    .flatMap(Person::getName)        // returns Optional<String>
    .map(this::processName)          // returns Optional<String>
    .orElse(DEFAULT_VALUE);          // returns String
```

我们看到，我们已经大大减少了所需代码的长度，并且能够将其重写为一个方法调用链。方法链接是处理`Optional`类型时遇到的典型模式，类似于 Java 的`Stream`类经常被使用的方式。

通过使用`Optional`类型，该方法的逻辑现在更纯粹地表示了它的业务逻辑，它只是简单地处理一个人的名字。

既然我们不必让嵌套和空检查使我们的代码陷入困境，我们可以编写更清晰的代码，在意图和效果上更加明确。

此外，通过为方法使用`Optional`返回值，我们现在可以明确地说方法可以返回`null`，它使用语言的类型系统来迫使开发人员适当地处理潜在的空值。

## 请在评论中告诉我你对可选类型的体验！