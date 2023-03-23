# 你应该知道的 7 个 JavaScript 技巧

> 原文：<https://betterprogramming.pub/7-javascript-tricks-you-should-be-aware-of-6bead729c418>

## 通过这些技巧，变得更擅长 JavaScript

![](img/d5fd8db0b8538df37bb637a5bbfee0b4.png)

照片由[詹姆斯·哈里森](https://unsplash.com/@jstrippa?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

JavaScript 的受欢迎程度目前正在飙升。它是最流行的编程语言之一。就像任何其他编程语言一样，JavaScript 有许多您可能还没有意识到的巧妙的小技巧。

这些技巧中的每一个都将帮助您完成大多数开发人员日常需要完成的任务。如果您有一些 JavaScript 经验，您可能已经知道其中的一些技巧，而其他技巧可能会让您大吃一惊。

本文将介绍七个技巧，它们将使你成为更好的开发人员，并帮助你编写更好的代码。

# 1.交换值

交换两个变量的值，你可能已经做过很多次了。最常见的方法是使用第三个临时变量，如下所示:

```
let nameA = "Pete"
let nameB = "John"
let temp = nameAnameA = nameB
nameB = tempconsole.log(nameA) // "John"
console.log(nameB) // "Pete"
```

但是您知道在 JavaScript 中交换两个变量的值有一个更简单的解决方案吗？看看下面几行代码:

```
let nameA = "Pete"
let nameB = "John"[nameA, nameB] = [nameB, nameA]console.log(nameA) // "John"
console.log(nameB) // "Pete"
```

看起来干净多了，不是吗？

# 2.经营者

JavaScript 有很多操作符，但是我想让你知道其中的两个，三元操作符和 nullish 合并操作符。从名字看，它们似乎很复杂；然而，没有什么是不真实的。

这两个运算符都可以用来设置变量。让我们从三元运算符开始，它基本上是一个简写的 if 语句。三元运算符由三部分组成。一个条件，后跟一个问号，然后是一个表达式，如果条件为 true，则执行该表达式，然后是一个冒号，最后是一个表达式，如果条件为 falsy，则执行该表达式。

看起来是这样的:

```
const price = isMember ? '$5.50' : '$15.00'
```

如果`isMember`的值为真，则价格为 5.50 美元，否则价格为 15.00 美元。

三元运算符的另一个例子:

```
const name = person ? person.name : 'Unknown'
```

我想让你们知道的另一个运算符是零化合并运算符。Mozilla 是这样描述 nullish 合并运算符的:

> **nullish 合并运算符(** `**??**` **)** 是一个逻辑运算符，当其左侧操作数为`null`或`undefined`时，返回其右侧操作数，否则返回其左侧操作数。

这是它实际运行的样子:

```
const name = person.name ?? "Unknown"
const age = getAge(person) ?? 21
const greeting = greeting ?? "Hello!"
```

# 3.可选链接

可选链接是我最喜欢的列表之一。可选链接实际上是一个操作符。

根据 Mozilla web 文档，这就是可选链接操作符的作用:

> 可选的链接操作符允许读取位于连接对象链深处的属性值，而不必明确验证链中的每个引用是否有效。

简而言之，可选的链接操作符极大地简化了访问嵌套值的方式。

让我们直接进入一个例子，帮助您更好地理解可选链接操作符的作用。

假设我们有一个名为*的人*的对象，看起来像这样:

通常，我们会通过以下方式访问该对象的属性:

```
const name = person.name
const age = person.age
const partner = person.parner.name
```

这些是从对象中读取值的基础。但是当这个人没有伴侣时会发生什么呢？最后一行代码会触发一个错误，因为我们无法读取未定义的属性*名称*。

要解决这个问题，您应该这样做:

```
const partner = person.partner ? person.partner.name : null
```

这个使用三元运算符的例子非常好。虽然这种实现方式的缺点是非常冗长，如果您必须检查一个或两个更深层次的属性，代码会变得非常不可读——因为您必须检查每个属性是否存在。

例如，检查州名如下:

```
const state = person.address && person.address.state ? person.address.state.name : null
```

如你所见，这段代码太长了，很快就变得不可读了。

这就是可选的链接操作符(写为`?.`)发挥作用的地方。有了这个操作符，就不需要检查某些属性是否存在，这将使您的代码保持整洁和易于理解。如果一个属性不存在，将返回*未定义的*。这是操作者在工作时的样子:

```
const city = person?.address?.city // "Miami"
const nonExisting = person?.gender // Undefined
const houseNumber = person?.address?.number // Undefined
const state = person?.address?.state?.abbreviation // "FL"
```

# 4.使用对象传递参数

您知道可以将参数作为对象传递给函数吗？它实现起来非常简单，并且使你的代码可读性更好。

要做到这一点，你需要把你的函数的参数用花括号括起来。

```
const createUser = ({ username, email, password }) => {
   // Here comes the logic
}
```

现在，您可以调用这个函数并使用一个对象来传递所有参数。

```
createUser({
   username: 'legendaryJohn',
   email: 'john@example.com',
   password: 'secret'
})
```

# 5.传播算子

spread 操作符(`...`)可以用于许多不同的技巧。可以使用 spread 运算符的技巧之一是合并数组。

```
const luckyNumbers = [1, 3, 7, 8]
const moreNumbers = [2, 9, 3]
const allNumbers = [...luckyNumbers, ...moreNumbers]console.log(allNumbers) // [1, 3, 7, 8, 2, 9, 3]
```

这是在 JavaScript 中合并数组的最简单、最干净的方法

# 6.有条件地设置变量

有条件地设置变量既简单又让你的代码看起来更优雅。应用这个技巧时不需要写 if 语句——这是我个人最喜欢的 JavaScript 技巧之一。

那么如何有条件地设置一个变量呢？

```
const timezone = user.preferred_timezone || "America/New_York"
```

在上面的例子中，我们检查用户是否有首选时区。如果用户有首选时区，我们就使用那个时区。如果用户没有首选时区，我们使用默认时区，即“美国/纽约”

这段代码看起来比使用 if 语句时干净多了。

```
let timezone = "America/New_York"if (user.preferred_timezone) {
    timezone = user.preferred_timezone
}
```

# 7.动态设置对象的密钥

有时候你可能需要动态设置一个对象的键。我经常看到的一种实现方式是使用方括号设置属性:

```
const dynamicKey = 'favoriteDrink'

let person = {
  name: "John",
}
person[dynamicKey] = '🍺'
```

这是实现动态密钥的老方法，需要两个步骤。对于您想要设置的每个其他动态键，您将需要另一行代码，这很快就会变得一团糟。

动态设置密钥的更好的解决方案如下所示:

```
const dynamicKey = 'favoriteDrink'let person = {
  name: "John",
  [dynamicKey]: '🍺',
}
```

这允许您在声明变量时设置动态键。

暂时就这样了。

感谢阅读！