# Kotlin 的“具体化”关键字如何简化泛型的使用

> 原文：<https://betterprogramming.pub/how-kotlins-reified-keyword-simplifies-working-with-generics-50a4e0cb254c>

## 如何访问 Kotlin 函数体中的泛型类型

![](img/ec22ea5cc230aa1debcd7aaf5b8263f9.png)

伯明翰博物馆信托基金会[在](https://unsplash.com/@birminghammuseumstrust?utm_source=medium&utm_medium=referral) [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

Kotlin 编程语言提供了一组很棒的特性，让程序员的生活变得更加轻松。与 Java 相比，Kotlin 提供了许多语法改进和技巧，使编程成为一大乐趣。`reified`关键词就是其中之一。这个关键字在许多其他语言中是未知的，因此许多人不直接理解它的用途。在这篇小文章中，我将通过一个例子来演示关键字的目的和用途。

# 我们到底在说什么？

为了同时解决许多不同的事情，软件需要抽象。许多流行且强大的语言，如 Java 或 C++(甚至 Golang，据我所知)都支持泛型。下面是一个用 Kotlin 编写的函数示例:

```
fun <T> myGenericFun(c: Class<T>)
```

在像`myGenericFun`这样的普通通用函数中，不可能直接访问函数体中的类型`T`。我们让它工作的方式是通过使用泛型类型`T`将类型`Class`的参数传递到函数中。需要这样做的原因是，像在 Java 中一样，所有的泛型类型在运行时都是不可用的。泛型类型在编译过程中被删除。因此，在函数体中引用泛型类型并不简单。例如，在某些情况下，您可能希望在函数的实现中访问`T`的类型。为此，我们需要显式地将类作为参数传递，如给定示例中所做的那样。这种方法是完全正确的，几乎可以在所有的 Java 代码库中找到。然而，Kotlin 试图简化使用泛型的工作，这就是`reified`关键字的用武之地。

# `reified type` 来救援了

如果我们以上面的示例函数为例，并希望利用 Kotlin 提供的具体化类型，那么要遵循的第一个先决条件是创建函数`inline`。内联函数由编译器解析，其代码被复制到所有调用点。如果我们现在将关键字`reified`添加到泛型类型`T`中，我们的签名如下所示:

```
inline fun <reified T> myGenericFun()
```

有了这样一个函数，我们现在可以像处理一个普通的`Class`一样处理`T`，例如，你可能想检查一个变量是否是`T`的一个实例，你可以很容易地这样做:`myVar is T`。

# 在后台

记住`reified`类型只能与`inline`功能结合使用。一个`inline`函数让编译器将函数的字节码复制到调用该函数的每个地方。当用`reified`类型调用`inline`函数时，编译器知道用作类型参数的实际类型，并修改生成的字节码以直接使用相应的类。因此，像`myVar is T`这样的调用在字节码中变成了`myVar is String`，因此可以在运行时工作。

# 在行动中具体化

让我们看一个例子来演示`reified`实际上带来了什么价值。任务是在`String`上创建一个扩展函数，该函数应该将 JSON 字符串转换成 Kotlin 类型，该类型由函数的泛型类型`T`指定。为此，我们将使用`com.fasterxml.jackson.module.kotlin`，我们的第一次尝试如下:

**第一种方法没有具体化类型**

```
fun <T> String.toKotlinObject(): T {
      val mapper = jacksonObjectMapper()
                                    //does not compile!
      return mapper.readValue(this, T::class.java)
}
```

最后一个语句中使用的`readValue`方法将一个类型作为它的最后一个参数(这里:`T::class.java`)。该参数用于指定解析应该发出的类型。如果我们现在在函数体内引用类型参数`T` ，编译器会报错如下:*“不能使用‘T’作为具体化的类型参数。请改用类。*”。这个错误基本上告诉我们，泛型类型 T 不能以我们的函数设置的方式使用。让我们在第一次迭代中修复它。

# **使用显式类参数的解决方法**

```
fun <T: Any> String.toKotlinObject(c: KClass<T>): T {
    val mapper = jacksonObjectMapper()
    return mapper.readValue(this, c.java)
}
```

为了解决这个问题，我们显式地传递`T`的`Class`，我们可以简单地将它传递给`readValue`。这种方法在 Java 世界中非常常见，并且工作正常。在调用端，我们的代码如下所示:

```
// type definitiondata class MyJsonType(val name: String)
// variable definitionval jsonTypeAsString = """{"name":"example"}"""
// call to String::toKotlinObjectjsonTypeAsString.toKotlinObject(MyJsonType::class)
```

# 使用`reified`的科特林方式

使用带有`reified`类型参数`T`的`inline`函数可以实现如下功能:

```
inline fun <reified T: Any> String.toKotlinObject(): T {
    val mapper = jacksonObjectMapper()
    return mapper.readValue(this, T::class.java)
}
```

不需要通过`T`的`Class`另外，`T`可以像普通班一样使用。对于客户端，代码简化为以下内容:

```
json.toKotlinObject<MyJsonType>()
```

# 与 Java 的互操作性

内联具体化函数不能从 Java 代码中调用，而普通的内联函数可以。这可能是为什么不是所有在`inline`函数中使用的泛型类型都默认成为`reified`的原因之一。

# 结论

我们已经看到`reified`关键字可以解决 JVM 的泛型类型擦除的常见问题。许多情况下需要我们访问函数体内的泛型类型，这在函数和被调用者端都很难实现。本文通过一个简单的例子展示了具体化类型简化函数实现和调用者代码的能力。