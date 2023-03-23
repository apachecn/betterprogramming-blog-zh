# Java 接口与抽象类有何不同

> 原文：<https://betterprogramming.pub/how-java-interfaces-are-different-from-abstract-classes-33fce6fc6d76>

## 了解相似之处以及何时使用什么

![](img/036a6fa8bdba161b88d63a55cb4d32a7.png)

图片来自 [Pixabay](https://pixabay.com/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=5090067) 的 [xresch](https://pixabay.com/users/xresch-7410129/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=5090067) 。

尽管接口和抽象类乍一看有许多相似之处——特别是在引入了`default`方法之后——但它们有不同的用例和功能。

```
**TABLE OF CONTENTS**[Interfaces](#5e41)
[Abstract Classes](#4266)
[Interface or Abstract Class?](#55aa)
[Why not use both?](#27d5)
[Behavioral Contract vs. Shared Logic](#a6b3)
```

*注意:假设使用 Java 8，但是如果适当的话，使用 Java 10 特性* [*局部变量类型推断*](https://developer.oracle.com/java/jdk-10-local-variable-type-inference.html) *来增加可读性。*

# 接口

如果一个类实现了一个接口，它就是一个它的实例如何与它周围的世界交互的行为契约。通常，存在方法签名，但是空的“标记”接口也是可能的。

想象一个简单的闹钟，只有一个闹钟:

任何实现`AlarmClock` *的类型都必须*实现符合接口的方法。

## 默认方法

Java 8 通过引入`default`方法改进了接口，允许我们提供一个不能被覆盖的方法的默认实现:

具体的实现不再需要提供暂停逻辑，但仍然可以选择覆盖一个或两个暂停方法。

## 静态方法

Java 8 还引入了`static`接口方法:

像`default`方法一样，`static`方法通过在一个地方聚集相关的方法/逻辑来提供更多的内聚性，而不需要像`AlarmTimeCalculator`这样的辅助对象。但是与`default`方法不同，我们不能覆盖`static`方法。

# 抽象类

一个`abstract class`是一种特殊的不可实例化的类，它可以被部分实现。它们被设计成由另一个类来完成。通过将方法签名定义为`abstract`，必须省略方法体，就像在`interface`中一样。

我们甚至可以轻松复制我们的`AlarmClock`接口:

与接口相比，抽象类最显著的优势是能够使用状态和提供构造函数。

## 状态

像任何其他类一样，我们可以定义一个内部状态，从而包含比使用`default`方法更多的逻辑:

## 构造器

我们也可以在`abstract class`中声明构造函数。是否声明构造函数以及声明什么类型的构造函数会直接影响我们在任何子类型中调用它们的方式。

*   无构造函数:没有任何显式构造函数，我们不必在任何子类型中调用`no argument`默认构造函数。
*   默认构造函数:如果`no argument`默认存在于`abstract class`中，仍然不需要在任何继承类型中显式调用它。电话将在“幕后”进行例如，我们的`AlarmClock`可以使用一个`Optional<LocalTime>`并在构造函数中初始化它:

*   参数化构造函数:与“普通”类之间的继承一样，默认`no-arg`构造函数的存在依赖于参数化构造函数的存在。如果一个参数化的构造函数存在，但是没有一个显式默认的`no-arg`构造函数，任何继承类型*必须*在它自己的构造函数中调用一个`super`构造函数。

假设我们想在构造函数中设置小睡持续时间:

具体的实现现在需要一个显式的构造函数调用`super`来让编译器高兴。它可以是默认的`no-arg`构造函数，也可以是参数化的构造函数。如果基类型中存在多个构造函数，调用其中任何一个都可以:

# 接口还是抽象类？

尽管两者都提供了一组重叠的功能，但它们的预期用途和应用是不同的。

## (多)遗传

与其他语言不同，Java 不支持多重继承。一个类只能*扩展*一个单独的其他类，比如`LongSnoozeClock`。

另一方面，接口不会屈服于这种限制。一个类可以*实现*多个接口，一个接口可以*扩展*多个接口。

所有的`default`方法都会被继承，导致一个问题:如果多个接口实现同一个方法怎么办？

没有显式的`@Override`，编译器将不知道调用哪个默认方法实现，所以我们被迫覆盖它:

但是我们仍然可以通过利用`<type>.super`来依赖默认实现:

另一种选择是扩展`Foo`接口来聚合`Bar`的逻辑:

## 可见性修改器

在一个`interface`中，一切都是隐式的`public`，只允许`static`方法为`private`。在缺乏状态的情况下，这种限制是有意义的。`default`方法使用的公共逻辑可以安全地重构为`private static`方法，以对实现者隐藏。

但是如果没有`protected`，一个接口永远不能只提供一个方法给它的直接实现者，而这个方法对所有其他类型都不可用。

唯一可用的可见性限制是基于类型的。如果接口声明本身没有被声明为公共的，那么它只能在包级别上使用。

抽象类是*类*，所以它们可以利用所有的可见性修饰符。当被继承时，它们遵循相同的规则:被重写的方法必须至少具有相同的可见性，但是可以选择增加可见性。

```
**Visibility increments**(none=package) -> protected -> public
```

## 展开性

在引入接口`default`方法之前，抽象类是确保可扩展性的首选方式。

如果我们向一个接口添加一个新方法，它的所有实现者都被迫实现它，很可能会破坏大量代码。另一方面，抽象类可以很容易地添加非抽象方法，所有相关类型都可以。

使用`default`方法，我们最终可以在不破坏现有类型的情况下，通过添加新方法来改进接口。实际上，JDK 8 用`default`方法实现了很多额外的特性，比如`java.util.Collection#stream()`。

即使没有默认逻辑，抛出一个`UnsupportedOperationException`也比破坏每个相关类型要好。

# 为什么不两个都用？

我们可以一前一后地使用它们，而不是使用接口或抽象类。接口可以描述类型的公共契约，抽象基类提供了实现它的起点。这对[服务定位器模式](https://en.wikipedia.org/wiki/Service_locator_pattern)特别有用。

我的公司对我们的数据访问对象使用了这种组合。一个`DAO`接口描述了如何访问数据，一个`HibernateDAO`抽象类提供了用 Hibernate 访问数据的基本功能。

下面是一个简化的例子:

一个实际的`DAO`服务接口现在可以只扩展`DAO`，它的具体实现可以扩展`HibernateDAO`:

由于接口和抽象类的这种结合，我们获得了多种优势:

*   `DAO`是任何道必须履行的最低契约。
*   `HibernateDAO`提供了任何具体的基于 Hibernate 的`DAO`可能需要的最基本的功能。
*   标准的 Dao，比如`BeanDAO`，以及它们的具体实现，只需要很少的额外代码。
*   嘲笑测试也更简单。就像`HibernateDAO`一样，我们可以创建一个名为`MockDAO`的共享抽象类。

# 行为契约与共享逻辑

决定使用哪一种的最好方法是认识到它们的最初目的是什么。

接口基本上是描述类型能力的行为契约。其自身及其实现者的多重继承允许广泛的使用，从简单的标记接口到要遵循的合同包。添加`default`方法甚至允许我们向所有实现者提供逻辑，并使可扩展性成为可能。

抽象类不仅仅是契约。它们是部分实现的类型，有时甚至根本不提供任何实现。这允许由继承者完成共享逻辑和实现细节的基类的创建。

```
**You like my ramblings about Java?
Check out my upcoming book!** [https://belief-driven-design.com/book/](https://belief-driven-design.com/book/)
```

# 资源

*   [什么是接口？](https://docs.oracle.com/javase/tutorial/java/concepts/interface.html)(甲骨文)
*   [抽象方法和类](https://docs.oracle.com/javase/tutorial/java/IandI/abstract.html)(甲骨文)
*   [接口](https://docs.oracle.com/javase/specs/jls/se7/html/jls-9.html) (Java 语言规范)
*   [抽象类](https://docs.oracle.com/javase/specs/jls/se7/html/jls-8.html#jls-8.1.1.1) (Java 语言规范)

[](https://medium.com/@benweidig/java-8-interfaces-default-methods-for-backwards-compatibility-2767a6a70947) [## Java 8 接口:向后兼容的默认方法

### 确保向后兼容性并编写更少的代码

medium.com](https://medium.com/@benweidig/java-8-interfaces-default-methods-for-backwards-compatibility-2767a6a70947)