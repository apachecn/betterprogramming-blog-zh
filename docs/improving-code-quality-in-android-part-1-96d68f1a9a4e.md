# 提高 Android 中的代码质量

> 原文：<https://betterprogramming.pub/improving-code-quality-in-android-part-1-96d68f1a9a4e>

## 帮助您编写更简洁代码的提示

![](img/65b3bfc301235e7607f3daea3fdf95f6.png)

乔舒亚·沃罗尼耶基在 [Unsplash](https://unsplash.com/s/photos/improvement?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的照片

这篇文章记录了我读到的东西，我收到的公关评论，以及我从同事那里学到的一些东西。如果你能对此补充更多，请不吝赐教。

# Val 或 Var

可变性与不可变性。如果让你选择，你会选择哪一个？我总是更喜欢不变性(`val`而不是`var`)。当一个变量变得可变时，你就打开了不确定性的大门。没有人能够预测该变量在该类中的某个点的值，并且该变量可以对整个类产生负面或正面的影响，因为它的不断进化的性质。只有当你确定`val`在你的场景中无法工作时，才尝试使用`var`。不要把奢侈给`var`。使用`val`将变量限制在某个范围内。

# 可见性修改器

Kotlin 中有四种类型的可见性修改器:

*   `private`
*   `protected`
*   `internal`
*   `public`

如果你的代码架构不好，这些修饰符中的大部分将被限制为私有或公共的。当你的班级开始分享东西而不太依赖彼此时，它们就会发挥作用。或许，如果你写的是干净的代码。

让我们来看看下面的代码:

```
abstract class Car {

    protected var name: String? = null
    protected var year: Int = 0
    private val welcomeMessage = "Hi, Welcome to your new car"

    abstract fun modelName()
    abstract fun manufacturedYear()

    fun printWelcomeMessage() {
        println(welcomeMessage)
    }

}

class Chevrolet : Car() {
    override fun modelName() {
        name = "Beat"
        printWelcomeMessage()
    }

    override fun manufacturedYear() {
        year = 2012
    }

}

class Hyundai : Car() {
    override fun modelName() {
        name = "Jazz"
        printWelcomeMessage()
    }

    override fun manufacturedYear() {
        year = 2015
    }
}
```

我们有一个基类叫做`Car`。雪佛兰和现代将其延伸。他们可以访问姓名和年份，这是`Car`的一部分，只是因为它是`protected`。因此，`protected`与`private`具有相同的可见性。但是在子类中也是可见的。

如果将姓名或年份作为内部变量会怎样。？然后，您可以从同一个模块的任何地方访问它。我们不必扩展类本身。`Car`类中的`welcomeMessage`变量呢？它是私有的，只能在`Car`类中访问。理解可见性修饰符之间的差异有助于您根据坚实的原则编写代码。

# 减少参数中的 Android 对象

稍有不慎，现在就有了不可测试的方法。或者，你必须加倍努力使它可测试。

```
fun importantMethod(size: Size) {
    // some logic using Size
}
```

我们已经传递了一个 Android 对象`Size`作为这个方法的参数。当您试图创建一个相同的模拟时，问题就来了。为 Android 相关的东西构建模拟对象是一项挑战。相反，你可以将 int 的`width`和`height`作为参数传递。在 Java/Kotlin 对象中寻找替代对象。总是试图在方法参数中限制 Android 对象的使用。

# 不要过度开发扩展函数

扩展功能是创新的。我们现在能够扩展一个类，而不实际继承它。此外，我们可以向它添加新的属性，使它看起来像是原始实现的一部分。

```
fun Activity.showToast(message: String) {
 Toast.makeText(this, message, Toast.*LENGTH_SHORT*)
 .show()
}
```

这是一个很好的扩展功能。为什么？因为它是通用的。您正在扩展活动的上下文，并创建一些方便的东西。很多活动都可以利用这个扩展功能。

```
fun Activity.solvingAnIssueForActivityA() {
    // some logic
}
```

这个怎么样？我们已经创建了一个扩展函数，而没有考虑好的设计。它将如何影响代码。？当有人在活动中使用点号时，这个函数会自动完成。开发人员会想我的活动是如何知道 ActivityA 内部的事情的。仅当您认为该代码是可重复的、其他人可以使用它、不涉及业务逻辑等等时，才创建扩展功能。明智地使用扩展函数。尽量不要虐待他们。

# Kotlin 对象不是线程安全的

```
object someCalculations {

    var value1 = 0 
    var value2 = 0

    fun doingSomethingWithValue1() {
    // some logic
    }

    fun doingSomethingWithValue2() {
    // some logic
    }
}
```

您可能已经知道，我们主要使用 Kotlin 对象来创建单例。在 Kotlin 中使用一个对象的主要优点是我们不必创建它的新实例。问题是我们有可能使用`objects`在其他对象/类之间共享数据。

让我们回顾一下上面的实现。我们设计它是为了让`value1`和`value2`可以从任何地方改变，并且使用这些属性的方法也可以从任何地方访问。如果您不走运，它们将被同时访问，您可能得不到您期望的更新结果。因此，如果出现这种情况，最好重构您的代码设计，让一些中间类可以帮助共享数据。或者，如果您计划继续处理该对象，请确保使用某种同步机制来确保您的字段得到更新。

我了解到，当你根据设计原则编写代码时，你的画布会扩大。不要局限于只从 Android 的角度思考。这将有助于所有相关人员。当我发现新的东西时，我会写更多。祝你愉快。😇

请随意看看我最近的文章:

[一个位图故事](https://clintpaul.medium.com/a-bitmap-story-a948bcd02acc)

[Android 上的 JNI:回调是如何工作的](https://clintpaul.medium.com/jni-on-android-how-callbacks-work-c350bf08157f)

[用工作管理器调度任务](https://medium.com/codex/scheduling-tasks-with-workmanager-13c4ad62704d)

本文最初发表于 clintpauldev.com