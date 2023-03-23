# Jetpack Compose 中的优化重组:稳定系统

> 原文：<https://betterprogramming.pub/optimizing-recomposition-in-jetpack-compose-stability-system-f8ec0c92de33>

## 深入了解 Jetpack Compose 的稳定性

![](img/537fbf5b358c5f1dfc380e8f378c04d0.png)

照片由[阿比盖尔·林恩](https://unsplash.com/@shmabbss?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

Jetpack Compose 具有智能重组功能，这是一个有助于高效重组的系统。它基本上是基于位置记忆设计的，对可组合的稳定性也有很大的影响。在本文中，我们将深入研究 Jetpack Compose 的稳定性。

首先，Compose 中的“稳定”是什么？稳定状态遵循三个条件。

*   当一个值改变时，它应该被通知给 composable。(诱导重组)
*   如果两个实例处于相同的状态，那么两个实例上的 equals 应该总是返回相同的结果。
*   所有公共字段也必须是稳定的。

如果符合这三个条件，则合成会认为该字段处于稳定状态，如果该字段的值没有改变，则跳过可合成的重新合成。这被称为可跳过状态。skippable 是跳过重组，直到有一个值改变的通知，由于条件是，当该字段的值改变时，composable 将被通知。

基本上，基本类型和函数类型被认为是稳定的，因为它们不能被定义改变。

做出这种稳定状态的注释是`[@StableMarker](https://developer.android.com/reference/kotlin/androidx/compose/runtime/StableMarker)`，Compose 使用了两种稳定状态。(两个注释都使用了`[@StableMarker](https://developer.android.com/reference/kotlin/androidx/compose/runtime/StableMarker)`

*   [@不可变](https://developer.android.com/reference/kotlin/androidx/compose/runtime/Immutable)
*   [@稳定](https://developer.android.com/reference/kotlin/androidx/compose/runtime/Stable)

第一个是`@Immutable`注释，表示所有的公共字段自从创建以来都是不可变的，并且可以应用于一个类。因为值在创建后不会改变，所以稳定性的第一条规则是“当值改变时，应该通知可组合组件。”被忽略。这是一个比 Kotlin 的`val`关键词更强的承诺。在`val`的例子中，实例是相同的，但是内部值可以改变。

`@Immutable`不允许这样。这对于所有字段都是`val`并且没有额外的 getters 的`data class`非常有用。

第二个是`@Stable`，表示一个可变的状态，其值可以改变，根据应用的对象，角色略有不同。如果应用于任何类型，则 StableMarker 的角色在没有任何附加角色的情况下被接受。如果应用于函数或属性，它将具有 StableMarker 之外的角色。对于相同的输入，它总是产生相同的输出(一个纯函数)，在函数的情况下，它承诺所有的自变量也是稳定状态。如果输入相同，将跳过重组，因为输出也相同。

对于稳定系统的不变状态，有一点需要注意。下面代码中`TextWithImmutableList`的`texts`参数是不可变的吗？

定义本身是不可变的，因为列表类型不是到`MutableList`而是到`List`，但是由于`MutableList`扩展了`List`，可变列表可以作为不可变类型传递。

因此，上述函数的`texts`自变量被判断为不处于稳定状态，不可跳过。为了使`texts`稳定，我们需要告诉编译器`List`是不可变的。

第一种方法是使用`@Immutable` StableMarker 为`List`创建一个包装器。

这使得`ImmutableListWrapper`本身是不可变的，所以编译器认为`texts`参数是稳定的，而`TextWithImmutableList`可组合的变得可跳过。

第二种方式是用科特林的`ImmutableCollections`。这是一个没有内置到 Kotlin 中的 Kotlin 附加库。它由 JetBrains 创建和维护，目前处于 alpha 版本，但 Compose 在其稳定性系统中使用它。

[](https://github.com/Kotlin/kotlinx.collections.immutable) [## kot Lin/kot linx . collections . immutable

### Kotlin 的不可变集合接口和实现原型。

github.com](https://github.com/Kotlin/kotlinx.collections.immutable) 

如果使用`ImmutableCollections`接收到`texts`参数，它被判断为不可变的，并且`TextWithImmutableList`可组合的变得可跳过。

此外，所有的稳定性都被传播。在下面的代码中，由于`provideSungbinLand`函数的返回类型`SungbinLand`处于稳定状态，所以即使函数中没有 StableMarker，也认为`provideSungbinLand`函数处于稳定状态。

让我们将上面代码中的 StableMarker 从`SungbinLand`移到`SungbinLandImpl`。

因此，`provideSungbinLand`函数被认为是不稳定的，因为 StableMarker 注释已经从`provideSungbinLand`函数的返回类型中移除。再看`SungbinLandDisplay` composable，`data`参数的类型是不稳定状态，但是用于在`provideSungbinLand`函数中提供一个值用作`data`参数的默认值的`SungbinLandImpl`类是稳定状态。所以，这个可组合的东西就可以跳过了。然而,`data`争论仍然保持在不稳定的状态。

如果`@StableMarker`使用不当，结果可能与 Compose 编译器预期的不同，并且可能出现运行时错误。此外，即使正确使用，也很难始终遵守规则。所以 Compose 有一个系统，除了基本公认稳定的类型之外，还能推断出是稳定类型。

Compose 的稳定性推理系统只适用于类，`[@StabilityInferred](https://developer.android.com/reference/kotlin/androidx/compose/runtime/internal/StabilityInferred)`在编译时注释**Compose 中使用的所有**类。

例如，在上面的代码中，`Name`类被认为是稳定的，因为所有字段都是不可变的稳定类型。

泛型类呢？因为`T`没有具体化，所以`NameWithGeneric`类不知道类型。在这种情况下，Compose 编译器在编译期间计算`T`的位掩码，然后将相应的值作为`StabilityInferred`的参数传递。这样，稳定性就可以从那个论点的值中推断出来。

然而，因为唯一的`value`字段是可变的，所以不能保证“当一个值改变时，它应该被通知给可组合的”规则得到遵守，所以即使`T`类型是稳定的，也认为`NameWithGeneric`类是不稳定的。

到目前为止，我们已经了解了 Compose 的稳定性系统。感谢阅读。

```
[[View in Korean]](https://sungbin.land/a-deep-dive-into-jetpack-compose-stability-38b5b109da71)
```