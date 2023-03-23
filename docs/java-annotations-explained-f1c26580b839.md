# Java 注释解释

> 原文：<https://betterprogramming.pub/java-annotations-explained-f1c26580b839>

## 如何在我们的代码中添加和使用元数据

![](img/8e91d940af4f5469f38ec86435b0d300.png)

照片由 [Jasmin Ne](https://unsplash.com/@jasminnb?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/@jasminnb?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

> *“注释| a-nə-ˈtā-shən
> 1:以评论或解释的方式添加的注释”——*[*韦氏词典*](https://www.merriam-webster.com/dictionary/annotation)

有了 [JSR-175](https://www.jcp.org/en/jsr/detail?id=175) ，Java 5 获得了[元数据功能](https://en.wikipedia.org/wiki/Metadata_facility_for_Java)，允许我们用*装饰性语法元数据*来注释我们的代码。

可以为类型、字段、方法、参数、构造函数、局部变量、类型参数、类型用法甚至其他注释类型提供元数据。通过各种各样的工具，可以在代码生命周期的不同阶段使用它。

# 注释剖析

注释类型的基本定义很简单:

让我们一行一行地看一遍，下面会详细解释每件事:

*   1: `[@Retention](https://docs.oracle.com/javase/8/docs/api/java/lang/annotation/Retention.html)` —注释将在我们代码的哪个生命周期中可用。
*   2: `[@Target](https://docs.oracle.com/javase/8/docs/api/java/lang/annotation/Target.html)` —我们可以使用注释的地方。
*   3: `[@Inherited](https://docs.oracle.com/javase/8/docs/api/java/lang/annotation/Inherited.html)` —如果存在，被注释的类型将把它传递给任何子类型。
*   4: `[@Documented](https://docs.oracle.com/javase/8/docs/api/java/lang/annotation/Documented.html)` —如果存在，像`[javadoc](https://docs.oracle.com/javase/8/docs/technotes/tools/windows/javadoc.html)`这样的文档工具可以访问它。
*   5: `@interface` —标记注释类型。
*   6–7:注释的值，可选默认值。

## 基本用法

最简单的注释用法是在兼容的目标站点使用`@MyAnnotation`。

但是如果没有提供默认值，注释可能需要设置多个值。值名`value()`是一个特殊的名称。如果没有其他值，它可以不带名称使用。

## @保留

我们代码的典型生命周期如下:

```
Source Code
   ▼
   ▼ ◁ Compiler
   ▼
Class file
   ▼
   ▼ ◁ JVM
   ▼
Runtime
```

注释的*保留策略*反映了这些生命周期，并为我们提供了一种方式来指定元数据的确切可用性:

*   `RetentionPolicy.SOURCE`
    注释只在源中可用。编译器将丢弃元数据，因此编译器和运行时都不能访问它。这个保留策略对于*预编译*工具很有用，比如[注释处理器](https://docs.oracle.com/javase/8/docs/api/javax/annotation/processing/Processor.html)。
*   `RetentionPolicy.CLASS`
    默认保留策略。注释对编译器是可见的，并且在类文件中可用，但在运行时不可用。任何*后编译*字节码工具都可能使用元数据。
*   `RetentionPolicy.RUNTIME`
    所有元数据在运行时都将可用。

我们的定制注释需要哪种保留策略取决于我们的需求。

提供的元数据可能包含关于带注释的代码内部工作的敏感信息。我们应该始终选择尽可能低的保持率，以便我们的代码仍然能够工作。

## @目标

不是每个注释在每个可用的目标上都有意义。这就是为什么我们可以明确地设定可接受的目标。八个可用目标在`[java.lang.annotation.ElementType](https://docs.oracle.com/javase/8/docs/api/java/lang/annotation/ElementType.html)`中定义:

*   `ElementType.PACKAGE` —包装声明。
*   `ElementType.TYPE` —类、接口、枚举。
*   `ElementType.TYPE_PARAMETER`-类属类型参数。从 Java 8 开始可用。
*   `ElementType.TYPE_USE` —类型的任何用法，如声明、泛型参数或类型转换。从 Java 8 开始可用。
*   `ElementType.ANNOTATION_TYPE`-注释类型。
*   `ElementType.CONSTRUCTOR` —构造函数声明。
*   `ElementType.FIELD` —字段和枚举常量。
*   `ElementType.METHOD` —方法声明。
*   `ElementType.LOCAL_VARIABLE` —局部变量声明(不保留在类文件中或运行时)。

`@Target`注释接受一组目标:

如果没有指定`@Target`，注释默认为除`ElementType.TYPE_PARAMETER`之外的所有可用的`ElementType`。

## @继承

默认情况下，注释不会被继承。通过将`[@Inherited](https://docs.oracle.com/javase/8/docs/api/java/lang/annotation/Inherited.html)`添加到注释类型，我们允许它被继承。这只适用于带注释的类型声明，这将把它传递给它们的子类型。

## @已记录

Java 对文档的默认行为是忽略任何注释。使用`@Documented`我们可以改变这一点，使元数据及其值可以通过文档访问。

## @可重复

在 Java 8 之前，我们只能在目标上应用一次特定的注释类型。在注释`[@Repeatable](https://docs.oracle.com/javase/8/docs/api/java/lang/annotation/Repeatable.html)`的帮助下，我们现在可以通过提供一个中间注释来声明一个可重复的注释:

现在我们可以多次使用我们的注释:

# 注释值

能够注释我们的代码并检查注释是否出现在不同的生命周期事件中是很棒的。但是，除了注释类型本身之外，提供额外的值会更好。甚至支持默认值。

值是可选的，将注释分成两组:

*   标记-没有值。仅仅是存在就是实际的元数据。例子:`[@Documented](https://docs.oracle.com/javase/8/docs/api/java/lang/annotation/Documented.html)`、`@Inherited`、`[@Override](https://docs.oracle.com/javase/8/docs/api/java/lang/Override.html)`。
*   配置—存在值，可能带有默认值，以便在使用时减少输入。例子:`[@Target](https://docs.oracle.com/javase/8/docs/api/java/lang/annotation/Target.html)`，`[@Retention](https://docs.oracle.com/javase/8/docs/api/java/lang/annotation/Retention.html)`。

Java 语言规范(JLS) [将](https://docs.oracle.com/javase/specs/jls/se8/html/jls-9.html#jls-9.7) 配置拆分为普通注释和单元素注释。但在我看来，这两者的行为重叠足以被视为(几乎)相等。

配置注释支持多个值。允许的类型在 [JLS 9.6.1](https://docs.oracle.com/javase/specs/jls/se8/html/jls-9.html#jls-9.6.1) 中定义:

*   原始类型
*   `String`
*   类型`Class`或`Class<T>`
*   枚举类型
*   注释类型
*   任何先前类型的数组(仅一维)

数组是唯一处理的。如果使用时只提供一个值，我们可以省略花括号。

默认值必须是常量表达式，尽管`null`是不可接受的。通过使用`{}`作为默认值，数组可以返回一个空数组。

# 内置注释

除了我们已经遇到的创建注释类型本身之外，JDK 还包括多个注释:

*   `[@Override](https://docs.oracle.com/javase/8/docs/api/java/lang/Override.html)`
    表示一个方法覆盖/替换一个继承的方法。这些信息并非绝对必要，但有助于减少错误。如果我们想要覆盖一个方法，但是在签名中有一个简单的类型，或者错误的参数类型，这个错误可能会被忽略。但是如果我们提供了一个`@Override`注释，编译器会确保我们确实覆盖了一个方法，而不仅仅是意外地添加或重载了它。
*   `[@Deprecated](https://docs.oracle.com/javase/8/docs/api/java/lang/Deprecated.html)`
    另一个只编译的注释。我们可以将代码标记为不推荐使用，编译器/IDE 可以访问这些信息来告诉我们不应该再使用这些代码。从 Java 9 开始，以前的标记注释变成了配置注释。添加值`String since() default ""`和`boolean forRemoval() default false`是为了给编译器和 ide 提供更多信息。
*   `[@FunctionalInterface](https://docs.oracle.com/javase/8/docs/api/java/lang/FunctionalInterface.html)`
    从 Java 8 开始，我们可以将接口标记为*单抽象方法接口* (SAM)，所以可以作为 lambdas 使用。这个标记注释允许编译器确保一个接口只有一个抽象方法。如果我们添加另一个抽象方法，我们的代码将不再编译。这个注释启用了编译器检查，但不是绝对必要的。任何 SAM 都自动成为一个功能接口。
*   `[@SafeVarargs](https://docs.oracle.com/javase/8/docs/api/java/lang/SafeVarargs.html)`
    另一个“相信我，我是工程师”的记号笔标注。告诉编译器我们在使用 [varargs](https://docs.oracle.com/javase/8/docs/technotes/guides/language/varargs.html) 时不会做任何不安全的操作。
*   `[@SuppressWarnings](https://docs.oracle.com/javase/8/docs/api/java/lang/SuppressWarnings.html)`
    一个配置注释，接受一个警告名数组，这些警告名在编译时应该被禁用。

# 如何在运行时访问注释

添加元数据是不够的。我们也需要以某种方式访问它。多亏了反射，我们可以通过类对象来访问它:

## 检查注释

## 访问元数据

相当于`boolean isAnnotationPresent(Class<? extends Annotation> annotationClass)`，我们也有访问实际注释实例的方法，为我们提供对其值的访问。

以下是适用于不同目标的一些方法:

[**类**](https://docs.oracle.com/javase/8/docs/api/java/lang/reflect/Method.html)

*   `[<A extends Annotation> A getAnnotation(Class<A> annotationClass)](https://docs.oracle.com/javase/8/docs/api/java/lang/Class.html#getAnnotation-java.lang.Class-)` —如果存在，返回特定的注释，否则返回`null`。
*   `[Annotation[] getAnnotations()](https://docs.oracle.com/javase/8/docs/api/java/lang/Class.html#getAnnotations--)` —返回给定类型的所有注释。
*   `[<A extends Annotation> A[] getAnnotationsByType(Class<A> annotationClass)](https://docs.oracle.com/javase/8/docs/api/java/lang/Class.html#getAnnotationsByType-java.lang.Class-)`-返回给定注释类型的所有注释。

[**方法**](https://docs.oracle.com/javase/8/docs/api/java/lang/reflect/Method.html)

*   `[<T extends Annotation> T getAnnotation(Class<T> annotationClass)](https://docs.oracle.com/javase/8/docs/api/java/lang/reflect/Method.html#getDeclaredAnnotations--)` —如果存在，返回特定的注释，否则返回`null`。
*   `[Annotation[] getDeclaredAnnotations()](https://docs.oracle.com/javase/8/docs/api/java/lang/reflect/Method.html#getDeclaredAnnotations--)` —返回方法的所有注释。
*   `[Annotation[][] getParameterAnnotations()](https://docs.oracle.com/javase/8/docs/api/java/lang/reflect/Method.html#getParameterAnnotations--)` —返回一个二维数组，包含按声明顺序排列的参数注释。

# 用例

一个很好的用例是序列化。有了注释，可以提供许多关于如何处理数据结构的额外元数据。

JSON 序列化框架 [Jackson](https://github.com/FasterXML/jackson) 使用`[@JsonProperty](https://github.com/FasterXML/jackson-annotations/blob/master/src/main/java/com/fasterxml/jackson/annotation/JsonProperty.java)`注释来提供修改默认序列化过程所需的所有信息:

另一个很好的用例是 [RESTEasy](https://resteasy.github.io/) 如何使用注释来[描述 REST 端点](https://docs.jboss.org/resteasy/docs/4.4.2.Final/userguide/html/Using_Path.html)，因此在其他地方不需要额外的配置:

通过这种方式，RestEASY 可以执行路由(`@Path`)，验证允许的 HTTP 方法(`@POST`和`@HEAD`)，提供从请求中提取的数据(`@FormParam`和`@HeaderParam`，并使用定义的媒体类型进行响应(`@Produces`)。

所有这些都不需要任何附加的配置文件或对象。配置就在相应的代码中。

# 结论

注释是为我们自己或者第三方工具和库提供额外数据的好方法。但是要注意解析、编译和查找注释的额外成本，尤其是在运行时。

```
**You like my ramblings about Java? Check out my upcoming book!**
[https://belief-driven-design.com/book/](https://belief-driven-design.com/book/)
```

# 资源

*   [Java 教程—注释](https://docs.oracle.com/javase/tutorial/java/annotations/index.html)(甲骨文)
*   `[java.lang.annotation](https://docs.oracle.com/javase/8/docs/api/java/lang/annotation/package-summary.html)` [包概要](https://docs.oracle.com/javase/8/docs/api/java/lang/annotation/package-summary.html) (JavaSE 8)
*   [创建自定义注释](https://www.baeldung.com/java-custom-annotation) (Baeldung)