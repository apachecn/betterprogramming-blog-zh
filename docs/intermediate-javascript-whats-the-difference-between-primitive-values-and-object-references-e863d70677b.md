# JavaScript 中的原始值和对象引用有什么区别？

> 原文：<https://betterprogramming.pub/intermediate-javascript-whats-the-difference-between-primitive-values-and-object-references-e863d70677b>

## 不可变数据和可变数据之间的重要区别

![](img/e16d283b39aae6a2bd4df5ee82d1e2ae.png)

由[克里斯托弗·高尔](https://unsplash.com/@cgower?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

JavaScript 中的所有数据类型都可以归为两类:**原始值**和**对象引用**。

原始值和对象引用的行为不同。这种行为上的差异会影响变量赋值的方式、等式运算符获得结果的方式以及 JavaScript 程序的一般运行方式。

理解原始值和对象引用之间的区别对于掌握 JavaScript 这种编程语言至关重要。这篇文章将深入解释和说明这种区别。

将涵盖以下细节:

*   哪些 JavaScript 数据类型属于每个类别。
*   一个值和一个参考值之间的差别。
*   不可变数据和可变数据的区别。
*   比较原始值和对象引用如何工作的实际例子。

# 对 JavaScript 的八种数据类型进行分类

JavaScript 目前支持八种不同的数据类型。这包括七个原始值类型和`objects`。这是完整的清单。

*   `Boolean`
*   `Null`
*   `Undefined`
*   `Number`
*   `BigInt`
*   `String`
*   `Symbol`
*   `Objects`

如果您是 JavaScript 新手，这个列表可能会让您觉得奇怪:`arrays`、`functions`和`dates`都不见了。

这不是一个错误。`Arrays`、`functions`和`dates`都在 JavaScript 程序中扮演着重要的角色，但它们实际上只是引擎盖下的对象。

# 值和引用之间的差异

原始值和对象引用以不同的方式存储在 JavaScript 程序中。

当一个原始值被分配给一个变量(如`let foo = ‘bar’`)时，该变量被直接设置为那个**值**。

然而，当变量被赋予一个对象时，情况就不同了。该变量包含一个对它的引用，而不是直接包含值。这里有一个例子:

```
const moe = {
  name: 'Moe Szyslak',
  occupation: 'Bartender',
  voicedBy: 'Hank Azaria'
}
```

当执行上面的代码时，JavaScript 创建对象并将其存储在计算机内存中的某个地方。变量`moe`不直接包含新对象，它包含一个对当前存储该对象的内存地址的引用。

这有点像街道地址不包含与特定住所相关的所有信息，它只包含住所在地理上的位置**。**

# 不可变数据和可变数据的区别

原始值和对象引用之间的一个关键区别是**可变性**。原始值是不可变的，对象引用是可变的。

简单来说，这意味着原语值不能改变(或者*突变*，但是对象引用*可以改变*。

为了说明这一点，我们来看一些代码。

## 不可变数据的一个例子

首先，我们来看一个**不可变的** 数据类型——字符串。首先，我们将创建一个变量`word`，并为其赋值。

```
let word = 'snow'
```

变量现在存在于内存中，您可以使用方括号符号来访问字符串中的各个字母。例如，如果我想获得第一个字母，我可以检查`0`索引中的值，并将其记录到控制台。

```
console.log(word[0]) // "s"
```

阅读字符串的单个字符是没有问题的。然而你做不到的，是改变(或者写)这些个别的字。让我们看看当你尝试时会发生什么:

```
word[0] = 'k'
console.log(word) // "snow" (The code above did not change the word)
```

你不能改变字符串，因为它是不可变的。

如果你需要绕过这个约束，你必须从旧的字符串创建一个新的字符串，并用新的值重新分配变量`word`:

```
word = `k${word.slice(1)}`
console.log(word) // "know"
```

## 可变数据的一个例子

接下来，让我们看一个**可变的**数据类型，数组。同样，数组实际上只是 JavaScript 中的特殊对象。

```
let letters = ['s', 'n', 'o', 'w']
letters[0] = 'k'
console.log(letters) // (4) ["k", "n", "o", "w"]
```

因为数组是可变的，你可以直接改变它们。没有必要给`letters`赋值，因为你可以直接改变现有的数组。

***抛开*** *:上面写的代码使用了* `*let*` *关键字来创建* `*letters*` *变量，但是你可以用* `*const*` *来定义变量，结果仍然是一样的。* `*const*` *阻止你对* ***重新赋值*** *的值，但不阻止你对***现有值进行突变。**

# *原始值和对象引用如何影响变量赋值*

*到目前为止，我们已经知道设置为**原始值**的变量包含实际值，设置为**对象引用**的变量只包含引用(或内存地址)。*

*让我们来看另一个探究这一事实如何影响变量赋值的例子。这是另一个例子:*

```
*let lead = 'John Lennon'
let coLead = lead
coLead = 'Paul McCartney'
console.log(lead, coLead) // John Lennon Paul McCartney*
```

*上面的例子创建了一个名为`lead`的变量，然后创建了一个名为`coLead`的新变量，并将其设置为包含`lead`的内容。然后，在第三行，`coLead`的值被重新赋值(记住，字符串是**不可变的**，所以它们实际上不能被改变)。*

*记录这些变量表明`lead`和`coLead`的值是`'John Lennon'`和`'Paul McCartney'`。*

*请注意，尽管我们在第二行将`lead`分配给了`coLead`，但在第三行将`coLead`重新分配后，这两个值仍然不同。*

*现在，让我们稍微修改一下示例，使用对象引用来代替原始值:*

```
*let lead = {
  name: 'John Lennon',
  group: 'The Beatles'
}
let coLead = lead
coLead.name = 'Paul McCartney'
console.log(lead, coLead)
// {name: "Paul McCartney", group: "The Beatles"} is logged twice*
```

*上面的代码与前面的例子非常相似。和前面一样，脚本创建一个名为`lead`的变量，然后创建一个名为`coLead`的新变量，并将其设置为包含`lead`的内容。*

*关键区别在于将`name`属性更改为“Paul McCartney”的那一行。我们没有重新分配`coLead`的值，而是让**变异**它现有的对象。*

*当我们将`lead`和`coLead`的值记录到控制台时，我们看到它们现在是相同的(显然 Paul McCartney 既是主角又是副主角。)怎么回事？*

*   *首先，在计算机内存的某个地方创建了一个具有“约翰·列侬”的`name`属性的对象*
*   *变量`lead`被赋予一个**引用**，或者内存地址，指向新创建对象。*
*   *在第二行，创建了一个名为`coLead`的新变量，并赋予它`lead`的内容。但是请记住`lead`没有被设置为直接值——它只是一个参考值。所以`coLead`现在包含了对第一行中创建的同一个对象的第二个引用。*
*   *第三线`coLead`是变异了。由于`lead`和`coLead`引用同一个对象，两个变量似乎都被改变了。*

*这种事情往往是 JavaScript 程序中的问题。*

*如果你想创建一个**新的**对象，而不是同一个对象的第二个引用(正如我们上面所做的)，你应该使用`Object.assign`来代替:*

```
*let lead = {
  name: 'John Lennon',
  group: 'The Beatles'
}
let coLead = Object.assign({}, lead, {
  name: 'Paul McCartney'
})
console.log(lead, coLead)
// {name: "John Lennon", group: "The Beatles"}
// {name: "Paul McCartney", group: "The Beatles"}*
```

*`Object.assign`接受的第一个参数是目标对象。在此之后提供的任何参数都是源对象，它们的属性将被复制到目标对象上。*

*在上面的例子中，一个新的对象(`{}`)被创建为目标(第一个参数)，然后来自`lead`对象的每个属性被应用到它(第二个参数)，然后一个新的 name 属性(‘Paul McCartney’)也被应用(第三个参数)。*

*这种方法允许您从一个对象复制另一个对象，同时仍然保持原始对象不变。*

# *原始值和对象引用如何影响相等比较*

*JavaScript 程序经常使用*等式*运算符来检查两个值是否相同。*

*使用`==`操作器检查*宽松等式*，使用`===`操作器检查*严格等式*。*

*如果被比较的两个项目的值相同，松散相等运算符(`==`)将返回`true`。如果被比较的两个对象的值**和**类型相同，则严格相等运算符(`===`)返回`true`。*

*通常建议尽可能使用`===`而不是`==`，以避免可能不期望的行为(例如像`0 == ‘’`)。本教程将专门使用严格等式，但是讨论的概念也适用于宽松等式。*

*因为原语包含直接值，所以用它们进行的相等比较的执行方式可能与您预期的一样:*

```
*'Moe Szyslak' === 'Moe Szyslak' // true
false === false // true
1970 === 1970 // true
undefined === undefined // true*
```

*另一方面，对象引用不直接包含值，而是包含引用。这导致对象的相等性检查给出的结果对于新开发人员来说可能不是直观的:*

```
*{ name: 'Moe Szyslak' } === { name: 'Moe Szyslak' } // false
[] === [] // false
new Date(0) === new Date(0) // false
NaN === NaN // false (NaN is another type of object)*
```

*在上面的所有四个对象比较检查中，在`===`操作符的左边和右边都创建了一个新对象。尽管左侧的内容与右侧的内容相同，但存储内容的引用或内存地址是不同的。这就是为什么在每种情况下比较都是`false`。*

*如果您创建了对同一对象的重复引用，您将会看到等式检查现在为真:*

```
*let lead = {
  name: 'John Lennon',
  group: 'The Beatles'
}
let coLead = lead
console.log(lead === coLead) // true*
```

*为了检查两个对象的*内容*(不是参考)是否相同，您需要:*

*   *遍历对象，检查每个键和值是否匹配。这可能很棘手，因为对象的属性本身可能是一个对象。*
*   *在进行相等性检查之前，将对象转换为合适的原语。*

*下面是一个例子，说明如何将对象文字转换成字符串进行比较。这应该被认为是一种快速和肮脏的方法，但我经常发现它很方便。*

```
*const moeA = { name: 'Moe Szyslak' }
const moeB = { name: 'Moe Szyslak' }
moeA === moeB // false
JSON.stringify(moeA) === JSON.stringify(moeB) // true*
```

*例如，下面是如何将日期转换成数字进行比较:*

```
*const dateA = new Date(0)
const dateB = new Date(0)
dateA === dateB // false
dateA.getTime() === dateB.getTime() // true*
```

# *摘要*

*JavaScript 目前支持八种数据类型。除了**对象引用**之外，所有这些数据类型(`Booleans`、`Null`、`Undefined`、`Number`、`BigInt`、`String`、`Symbol`)都是**原始值**。*

*许多常见的数据类型，如数组、函数和日期，都是幕后的对象引用。*

*原始值可以直接存储在变量中。另一方面，对象存储为引用。被赋予对象的变量并不直接存储该对象，而是存储该对象所在位置的内存地址。*

*原始值是不可变的 T21——它们在被创建后不能被改变。然而，对象引用是可变的，可以被改变。*

*因为对象是作为引用存储的，所以在复制对象和对对象执行相等检查时必须特别小心。*

*对于 JavaScript 新手来说，理解这些操作是如何工作的可能会很困惑，但是一旦你理解了语言的类型系统是如何工作的，它们就有意义了。*

*深入掌握**原始值**、**对象引用**和**可变性**是超越 JavaScript 编程初级阶段的关键一步。*

*这些知识将帮助您识别 bug，理解编程范例中的关键差异，并帮助您在更深的层次上理解您的代码。*