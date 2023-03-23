# Kotlin 中的类型安全域建模

> 原文：<https://betterprogramming.pub/type-safe-domain-modeling-in-kotlin-425ddbc73732>

## “如果它能编译，它就能与 Valiktor 和 Konad 一起工作”

![](img/f091ce7c4a0df0db754457318014a625.png)

照片由[乔治·鲁达科夫](https://unsplash.com/@rudakov_g?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

在领域驱动设计中，有一个[无处不在的语言](https://martinfowler.com/bliki/UbiquitousLanguage.html)的概念。简单来说，这通常与您给域模型中的实体起的名字有关。

然而，有可能更进一步。我们可以将代码转换成领域的明确表达。

例如，这里它遵循了美国领域专家如何描述一个“联系人”和一些业务规则。

*联系人有名字、姓氏和电子邮件。姓名可以有中间名的首字母。必须验证该电子邮件。您可以发送密码恢复只验证电子邮件。*

一个典型的、面向持久性的建模可能是这样的:

这段代码表达了任何需求吗？是的，它列举了所有的数据。反正字符串有约束吗？所有的数据都是必须的吗，还是有些数据可能会丢失？

通过在模型中声明这些信息，我们可以做得更好。

# 声明性领域模型

首先，回忆一下基础。

## 内聚力

如果一些数据是相关的，那么将它们分组是一个好主意。

这个模型表达了哪些信息是相关的。保持高内聚也有利于重用。

## 声明约束

`PersonalName`的字符串属性不能为空。这个在代码中没有明确，我们来明确一下。

注意`middleInitial`是可空的。这表示在编译时中间的首字母是可选的。

我在`NotEmptyStrings`中包装了字符串，以确保符合约束。

这里我使用了一个[内联](https://kotlinlang.org/docs/reference/inline-classes.html)类，并且声明了一个编译时错误以防止使用构造函数。这个类只能使用工厂方法`of`进行实例化，因此没有机会跳过验证。我用 [Valiktor](https://github.com/valiktor/valiktor) 来实现它。

Kotlin 中存在内联类，目的是增强原始类型的表达能力。在域模型中使用原始类型是一种代码味道，被称为[原始痴迷](https://refactoring.guru/smells/primitive-obsession)。

## 使其在编译时安全

`NotEmptyString.of`的签名声明，如果你输入一个`string`，你将总是得到一个`NotEmptyString`。

```
fun of(value: String): NotEmptyString
```

这是谎言。如果验证失败，那么 Valiktor 将抛出一个`ConstraintViolationException`。它抛出的可能性没有在签名中声明，那么您可能会得到一个意外的运行时错误。让我们用 [Konad](https://github.com/lucapiccinelli/konad) 来解决这个问题:

这里我使用了助手函数`valikate`，它捕捉异常，用 Valiktor API 格式化它的消息，并将所有东西包装在`Result`中。

构建器`ok()`和`error()`是来自 [Konad](https://github.com/lucapiccinelli/konad) 的扩展方法，分别构建一个`Result.Ok`和一个`Result.Errors`。

看看新的签名:

```
fun of(value: String): **Result**<NotEmptyString>
```

失败的可能性现在很明显。在 Konad 中，`Result`是一个[密封的](https://kotlinlang.org/docs/reference/sealed-classes.html)类，可以是`Result.Ok`或`Result.Errors`。使用它，功能是免费的副作用。

## 撰写个人姓名

现在我们可以创建一些可能导致错误或期望值的`Result<NotEmptyString>`。从这些，我们需要获得一个`PersonalName`。如果你习惯于像[Java](https://www.baeldung.com/java-optional)或 [Kotlin nullables](https://www.baeldung.com/kotlin-null-safety) 这样的“单子式”结构，那么你可以预期我将会写一个无限的`.flatMap.flatMap.map…`或`let.let.let…`列表。

我不是。这就是 [Konad](https://github.com/lucapiccinelli/konad) 大放异彩的地方。我们将使用 Konad 的组合 API 创建一个`PersonalName`:

Konad 累积所有的误差。示例中的`println`将打印所有错误的列表，用新的一行`name.description("\n")`隔开。

## 移除标志

图片由 [Giphy](https://media.giphy.com/media/Lec4TntkAgnQc/giphy.gif) 提供

邮件可以验证，也可以不验证。在初始模型中，有一个布尔标志`isVerified`保存这个信息。

该标志不是类型安全的。每当我们需要一封经过验证的电子邮件时，都需要进行检查。例如:

这在编译时无法检查。我的首选方法是为`Unverified`状态创建一个类型，为`Verified`创建一个类型。

`sendPasswordRecovery`功能可以改变如下:

`if`已经过去了，不可能有人忘记检查`verified`状态。此外，您不需要阅读实现来理解只有经过验证的电子邮件才能实现密码恢复。

## 验证电子邮件

我们需要限制电子邮件的结构，就像我们已经为`NotEmptyString`做的那样。

要创建一个`Verified`电子邮件，你需要一个`Unverified`电子邮件。由此可见，只对`Unverified`电子邮件实施验证就足够了。

*注意* `*Unverified*` *变成了普通类，而不是数据类。这是因为没有办法使私有的* `*copy*` *方法成为一个数据类。*

# 最后，声明性模型

最后，这个模型看起来是这样的:

只要看一下就能明白所有的要求:一个联系人由一个名字和一个邮箱组成；名称由三个不能为空的字符串组成；其中一个字符串可能丢失；电子邮件可以是未验证的，也可以是已验证的。

让我们来看看一些示例服务:

最后，如何打造一个`ContactInfo`。再次，用 [Konad](https://github.com/lucapiccinelli/konad) 的组合 API:

# 结论

使用建议的方法，您不需要查看任何方法实现就可以理解:

*   数据的结构
*   所有的限制
*   涉及这些数据的流程

一切都在签名里！

设计确保编译时安全且没有副作用的类并不容易。我提出了一些实现示例，使用:

*   用几行代码实现可读的数据检查。
*   [Konad](https://github.com/lucapiccinelli/konad) 提高编译时安全性。

单子的使用增加了代码的复杂性。使用 Konad，您可以受益于它们的编译时安全性，同时对代码复杂性的影响最小。Konad composition API 易于使用，不需要任何函数概念的知识。

# 代码和材料

下面是完整的代码示例。你可以在以下回购处找到:【https://github.com/lucapiccinelli/typesafe-domain-model】T4。

# 感谢

这篇文章基于 Scott Wlaschin 带来的概念，他的演讲(和书)“[领域建模功能化](https://www.youtube.com/watch?v=Qt2iGKT09UY&t=3095s)”。

感谢阅读！