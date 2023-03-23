# 你错过的科特林特色

> 原文：<https://betterprogramming.pub/kotlin-features-youve-been-missing-out-on-f80a415334c1>

## 看看一些很酷但经常被忽视的功能

![](img/828c860f4b08e6e939aaaca091ae7463.png)

Marc Reichelt 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 析构声明

[析构声明](https://kotlinlang.org/docs/reference/multi-declarations.html)允许我们从对象中提取字段，类似于在 TypeScript/ES6 中可以做到的。

想象我们有以下的`data class`:

```
data class Person(val name: String, val age: Int)
```

可以在一条指令中将`name`和`age`字段析构为两个独立的变量:

```
val john = Person("John Doe", 23)
val (name, age) = john
println(name) // John Doe
println(age) // 23
```

不过，有一点需要记住。变量的名字与过程完全无关。重要的是它们相对于对象场的位置。如果你移动了一个`data class`的一些字段的位置并且使用了析构，这可能会有问题。如果您添加一个新的字段，这也可能会有问题。

```
// before
data class Person(val name: String, val age: Int)
val jane = Person("Jane Doe", 23)
val (name, age) = jane// after
data class Person(
  val firstName: String,
  val lastName: String,
  val age: Int
)val (name, age) = jane // this will now be firstName and lastName
```

如果你使用的是 [TDD](https://en.wikipedia.org/wiki/Test-driven_development) ，你的测试应该会发现这些问题。反正小心点还是好的。

析构也可以用于 lambda 参数:

```
val people = listOf(
   Person("John Doe", 23),
   Person("Jane Doe", 21),
   ...
)
people.forEach { (name, age) -> ... }
```

默认情况下，这可用于配对、`map`条目和许多其他类型:

```
map.forEach { (key, value) -> ... }
val (x, y) = Pair(1, 2)
```

默认情况下，简单类不支持析构。可以通过添加`componentN`方法来启用它，其中`N`是析构位置。例如:

```
class Person(val name: String, val age: Int) {
  operator fun component1() = name
  operator fun component2() = age
}
```

`component`后面的数字表示对象析构后值的最终位置。

您不需要每次都析构所有的字段。您可以选择只析构您需要的第一个字段。您也可以使用`_`字符忽略字段。例如:

```
data class Person(
   val firstName: String,
   val lastName: String,
   val age: Int
)val john = Person("John", "Doe", 23)
val (firstName, lastName) = john
val (_, _, age) = john
```

# 运算符重载

Kotlin 支持[运算符重载。一些类已经有了重载的操作符。例如:](https://kotlinlang.org/docs/reference/operator-overloading.html)

```
val result = listOf(1, 2, 3) + listOf(4, 5, 6)
println(result) // 1, 2, 3, 4, 5, 6val duration = Duration.ofSeconds(10) + Duration.ofSeconds(5)
println(duration) // PT15S (15 seconds)
```

您可以通过声明`operator`方法来为您的类重载操作符。可用操作员的完整列表可在[官方文档](https://kotlinlang.org/docs/reference/operator-overloading.html)中找到。举个简单的例子，让我们只考虑`plus`操作符:

```
data class Quantity(val value: Int) {
    operator fun plus(other: Quantity) =
       Quantity(value + other.value)
}
```

`other`不一定是同一类型——它可以是任何类型。现在我们可以写:

```
val a = Quantity(10)
val b = Quantity(15)
val c = a + b // Quantity(25)
```

另一件很酷的事情是你可以使用[扩展](https://kotlinlang.org/docs/reference/extensions.html)来添加操作符到现有的类中:

```
operator fun Quantity.plus(quantity: Int) =
   Quantity(value + quantity)val a = Quantity(10) + 15 // Quantity(25)
```

## 可比较的

任何实现`Comparable`的类都自动带有以下比较运算符:`<`、`>`、`<=`和`>=`:

```
data class Quantity(val value: Int) : Comparable<Quantity> {
    override fun compareTo(other: Quantity) =
            value.compareTo(other.value)
}// now we can compare:
val a = Quantity(10)
val b = Quantity(5)
if (a >= b) ...
```

![](img/7f40db9155a10839dc3ab94734c6fb5b.png)

照片由[迈克·肯尼利](https://unsplash.com/@asthetik?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 复制

你有没有遇到过一个巨大的[数据传输对象](https://en.wikipedia.org/wiki/Data_transfer_object)或[值对象](https://martinfowler.com/bliki/ValueObject.html)有很多属性，而想要创建一个副本，只改变一个属性？Kotlin 为`data class` es 提供了开箱即用的特性。

实际上，用 Kotlin 的 [copy](https://kotlinlang.org/docs/reference/data-classes.html#copying) on `data class` es，你可以一次改变不止一个属性。所有其他属性将使用原始对象中设置的值。

这里有一个例子:

```
data class Person(
    val firstName: String,
    val lastName: String,
    val age: Int
)val john = Person("John", "Doe", 23)
val jane = john.copy(
  firstName = "Jane",
  age = 21
) // Jane Doe, 21
```

# 中辍法

即使你不知道 [*中缀*](https://kotlinlang.org/docs/reference/functions.html#infix-notation) 关键词*，*你肯定用过。例如，`"a" to 10`创建一个对，`0 until 10`创建一个范围都使用中缀符号。 [Kotest](https://github.com/kotest/kotest) 在它的[断言](https://github.com/kotest/kotest/blob/master/doc/matchers.md)上提供了中缀符号，使得它们读起来很好:`something shouldBe someValue`。

`infix`符号允许您在调用函数时省略点号和括号。例如，上述示例相当于:

```
"a" to 10 == "a".to(10)
0 until 10 == 0.until(10)
something shouldBe someValue == something.shouldBe(someValue)
```

要声明一个函数`infix`，它应该只有一个没有任何默认值的参数，并且应该是一个成员或扩展函数。

```
enum class CoffeeType { espresso }
class CoffeeMachine {
    infix fun make(coffee: CoffeeType) {
       // ...
    }
}val coffeeMachine = CoffeeMachine()
coffeeMachine make espresso
```

中缀调用可以根据它们的返回类型进行链接。这让我们可以写下:

```
0 until 10 step 2
```

# 使具体化

关键字`[reified](https://kotlinlang.org/docs/reference/inline-functions.html#reified-type-parameters)`允许您访问一个类类型，而不必将其作为参数提供。这只适用于`inline`方法，不使用任何反射。这不是你经常需要的东西，但是在处理类类型的时候它会派上用场。

这就是[mock](https://github.com/mockk/mockk)嘲笑类时使用的:

```
val service = mockk<Service>()
// instead of having to write
val service = mockk(Service::class.java)
```

下面是我遇到的一个例子，其中一个`reified`方法被用来为一个工厂创建一对类型到创建者的 lambda。工厂需要根据类类型调用不同的创建者 lambda:

```
inline fun <reified T> factory(noinline creator: () -> T) =
    T::class.*java to* creatorval factoriesByClass = *mapOf*(
    *factory* **{** Person("John Doe", 23) **}**,
    *factory* **{** CoffeMachine() **}** )
// instead of 
val factoriesByClass = *mapOf*(
    Person::class.*java to* **{** Person("John Doe", 23) **}**,
    CoffeMachine::class.*java to* **{** CoffeMachine() **}** )
```

这使得工厂代码可读性更好，因为它不会被多余的类类型参数所污染。

![](img/cbbd4002918efe85d2c6e3138098be5e.png)

亚历山大·曾在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 类型别名

当你开始得到多种类型的复杂组合时，比如一个`Map<String, List<Person>>`(我敢肯定你遇到过比这更复杂的方式)，每次都要把它打出来是相当乏味的。幸运的是，Kotlin 附带了[类型别名](https://kotlinlang.org/docs/reference/type-aliases.html)，允许您简化这些类型，使它们更具表现力:

```
typealias Registry = Map<String, List<Person>>
```

这个特性也可以仅仅用于表现:

```
typealias Position = Pair<Int, Int>
```

# 委派属性

委托属性允许您向属性中添加逻辑，而不必在同一个类中手动编写该逻辑。您可以编写自己的委托。这样，委托逻辑可以从您的业务逻辑中独立出来并进行测试。在[官方文档](https://kotlinlang.org/docs/reference/delegated-properties.html)中解释的很好。你也可以使用一些标准的委托。

最常见的是`Lazy`委托，它将在第一次调用时计算 lambda 的结果，并在以后的调用中返回一个记忆化的值。如果初始化值非常昂贵和/或可能不需要，这非常有用。

```
val *lazyValue*: SomethingExpensiveToCreate by *lazy* **{** SomethingExpensiveToCreate()
**}**
```

另一个标准的方法是`Observable`，通过提供一个用新旧值调用的 lambda，允许您跟踪对字段所做的任何修改。

因为您可以实现自己的委托，所以可能性是无限的。

# 包扎

希望你会发现其中一些功能很有用——如果你还不知道的话。了解更多关于 Kotlin 的一个很好的资源是 [EduTools](https://kotlinlang.org/docs/tutorials/edu-tools-learner.html) 插件。也请务必查看一下 [Kotlin 参考文献](https://kotlinlang.org/docs/reference/)。这里有很多我没有涉及的主题，比如协程，在那里有详细的解释。

感谢阅读！