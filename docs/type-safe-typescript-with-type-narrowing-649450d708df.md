# 具有类型收缩的类型安全类型脚本

> 原文：<https://betterprogramming.pub/type-safe-typescript-with-type-narrowing-649450d708df>

## 提高项目类型安全性的常用模式指南

![](img/e1febcfce7a2d31ec1edb4cd09c6ffd0.png)

[https://pix abay . com/illustrations/cubes-choice-one-yellow-light-2492010/](https://pixabay.com/illustrations/cubes-choice-one-yellow-light-2492010/)

本文展示了最大化 TypeScript 的类型安全代码潜力的常见模式。这些技术属于同一类，我们称之为类型收缩。

源代码可在 [GitHub](https://github.com/rainerhahnekamp/type-safe-typescript-with-type-narrowing) 上获得。本文的视频版本可从以下网址获得:

# 介绍

每当我们处理多种类型的变量时，比如一个`unknown`或一个联合类型，我们可以应用类型缩小来将其“缩小”到一个特定的类型。我们与 TypeScript 编译器一起工作，因为它理解我们代码的上下文，并保证这种缩小以完全类型安全的方式发生。

假设我们有一个参数类型为`Date | undefined`的函数。每次函数执行时，变量的类型可以是`Date`或`undefined`，但不能同时是两种类型。

```
function print(value: Date | undefined): void {}
```

如果我们应用一个`if`条件，检查那个变量是否不是`undefined`，TypeScript 理解它的意思，只把条件中的值当作一个`string`。这是类型收缩。

```
function print(input: Date | undefined): void {
  if (input !== undefined) {
    input.getTime(); // 👍 value is only Date
  } input.getTime(); // 💣 fails, value can be Date or undefined
}
```

还有一种非常类似的技术，叫做类型断言。乍一看，这似乎是更容易的选择。然而，就类型安全而言，它并不安全。我们手动设置类型，因此覆盖了编译器。

如果编译器可以和我们说话，它会说类似这样的话:“好吧，你知道你在那里做什么，但是如果出了问题，不要怪我。”

因此，我们应该避免类型断言，而总是支持类型收缩。(一般来说，试图比编译器聪明从来都不是一个好主意。)

```
function print(input: Date | undefined): void {
  (input as Date).getTime(); // type assertion - don't!
}
```

在这个简短的介绍之后，让我们设计一个主要类型收缩技术的例子。

这将是我们的“工作台”:

```
declare function diffInYears(input: Date): number;
declare function parse(input: string): Date;function calcAge(input: Date | null | undefined): number {
  return diffInYears(input); // will not work
}
```

`calcAge`，应该返回，顾名思义，`age`。

此外，我们使用两个实用函数`diffInYears`和`parse`。为了简单起见，这个片段没有显示它们的实现。

`calcAge`中`input`的类型有三种。因此，return 语句将无法编译。

# 平等缩小

一个明显的开始是检查`input`是否没有值`null`和`undefined`。如果是这样，那就只能是一个`Date`。

如果我们添加这个条件，TypeScript 理解它并将`if`块中的`input`视为`Date`，我们可以安全地调用`diffInYears`。

这种对 TypeScript 的“隐式理解”已经是我们的第一个类型保护，它被称为“等式收缩”

```
function calcAge(input: Date | null | undefined): number {
  if (input !== null && input !== undefined) {
    return diffInYears(input);
  }
}
```

请注意`if`条件不是对`undefined`或`null`类型的直接检查。它违背了价值观。同样，我们也不能针对`Date` : `value === Date`编写类型检查。`Date`是`input`的类型而不是它的值。

那么为什么我们可以用`undefined`和`null`呢？答案很明显。类型`undefined`只有一个值，即“未定义”而`null`也是如此。类型`null`只能有一个值，即“null”

所以我们的条件实际上排除了`undefined`或`null`的所有可能值。出于明显的原因，我们不想排除类型`Date`:)的所有可能值

# 类型 of

让我们试试另一种类型的后卫。JavaScript 提供了`typeof`，我们可以把它放在任何变量的前面。顾名思义，它将返回变量类型的名称作为`string` …嗯，不完全是。否则，我们早就读到这篇文章的结尾了:)。

在 JavaScript 中，我们有 7 个原语类型，剩下的只是类型`object`。基本类型有`boolean`、`string`、`number`、`undefined`、`null`、`bigint`和`symbol`。

`typeof`返回除了`null`之外的每个原始类型的名称。对于`null`，它返回‘对象’所以我们可以假设对于`null`和任何非原始类型，我们会得到‘object’但是还有第二个例外。如果变量实际上是一个`function`或者如果你传递了一个`class`的名字，`typeof`也返回‘函数’。严格来说，函数不是实型；它是一个可调用的对象。

有了这些知识，下面的代码会起作用吗？

```
function calcAge(input: Date | null | undefined): number {
  if (typeof input === "object") {
    return diffInYears(input);
  }
}
```

不会。因为`typeof`也为`null`返回“object ”,所以编译会失败。所以，我们对`typeof`型守卫没有任何用处，但是让我们记住它。我们以后可能需要它。

# 真实性变窄

JavaScript 发明了两个新的英语术语。虚伪和真实。如果我们将一个假值放入一个条件中，它将为真值返回`false`和`true`。JavaScript 中有一个详尽的 falsy 值列表。分别是`false`、`0`、`0n`、`''`、`null`、`NaN`和`undefined`。

我们可以利用这一点。如果我们只将`input`放入`if`条件中，如果值不是`undefined`或`null`，它将是`true`。在我们的例子中，这与等式运算符非常相似，只是更短一些。

```
function calcAge(input: Date | null | undefined): number {
  if (input) {
    return diffInYears(input);
  }
}
```

我们漏了一点点。如果该值是一个空字符串，它也将是 false，我们将不会在`if`-条件中结束。但是只要我们知道，这对我们的用例来说是可以的。

让我们使我们的例子更有趣。我们添加了第四种可能的类型，即`string`。现在是`typeof`进入游戏的时间。

首先，我们通过 truthiness narrowing 消除了值为`null`或`undefined`的可能性，并返回值 0(我们将在后面进行改进)。

那么就只有在`Date`和`string`之间了。而在这里，我们可以用`typeof`来检查`input`是否是`string`。如果没有，那只能是`Date`。完美！

```
function calcAge(input: Date | null | undefined | string): number {
  if (!input) {
    return 0;
  } else if (typeof input === "string") {
    return diffInYears(parse(input));
  } else {
    return diffInYears(input);
  }
}
```

# `instanceof`

当我们处理类时，也有可能进行类型收缩。为此，让我们添加一个类`Person`，它有一个类型为`Date`的属性`birthday`。

显然，`else`子句中的最后一个返回将失败，因为`input`可以是类型`Date`或`Person`。好消息是两者实际上都是类实例，我们可以使用`instanceof`。

如果值是特定类的实例，则`instanceof`返回`true`。因此，如果我们为类`Date`添加一个带有检查的条件，我们再次处于类型安全的一侧:

要知道`instanceof`为整个类继承链返回`true`。因此，如果`Person`从`Entity`类扩展而来，`instanceof Entity`也将返回 true。

# 歧视工会

我们经常使用类，但是我们经常处理对象文字或者——用 TypeScript 行话来说——接口或类型。

看看这个稍加修改的例子:

哎哟，又是一个编译错误。我们不能在这里也使用`instanceof`吗？编号`Person`和`Car`都只是一个`type`，是一个仅存在于 TypeScript 中的元素。当它被转换成 JavaScript 时，`Person`和`Car`的定义就不再存在了。另一方面，类也确实存在于 JavaScript 中。这就是为什么`instanceof`为他们工作。

好吧，那我们能做什么？

我们处于一种幸运的地位。`Person`和`Car`共享同一个属性`category`，并且各自有不同的值。通过验证`category`是否有值“car”，TypeScript 足够聪明地理解它只能是类型`Car`。对于`Person`,该值显然是“人”。这种类型的防护装置的名称是“歧视性联合”让我们再次修正我们的代码:

我们不需要为鉴别器`category`调用属性。我们可以选择任何我们想要的名字。

# `"in"`型防护罩

考虑这样一种情况，我们没有那么幸运地拥有一个可以用作鉴别器值的属性:

在这种情况下，我们可以使用`in`型防护罩。用`in`我们可以请求一个对象是否有某种属性。所以如果它在`Person`和`Car`之间，并且属性`birthday`存在。类型只能是`Person`。

# 类型谓词

我们的下一个类型后卫不是真正意义上的类型后卫。这是某种妥协。

首先，让我们用类型`PersonJson`替换类型`Car`。它也有一个属性`birthday`，但是它的类型是`string`。

我们可以通过`typeof value.birthday === 'string'`逃脱。这是`typeof`和区别联管节型护罩的组合:

这段代码编译好了，一切看起来都没问题，但并不完美。如果我们将`input`赋给最后一个`else if`语句中的一个新变量，我们会看到 TypeScript 将类型标识为`Person | PersonJson`，而不是`PersonJson`。

这是我们达到 TypeScript 极限的时间点。幸运的是，这并不意味着游戏结束。

每当 TypeScript 用尽选项时，它允许我们提出一个包含特定类型验证代码的函数。

某种程度上，这是一种妥协。只要我们返回`true`或`false`，我们就可以在那个函数中写任何我们想写的东西。TypeScript 会信任我们。

这个特殊的函数被称为“类型谓词”,对于`Person`,它看起来是这样的:

```
function isPerson(value: Person | PersonJson): value is Person {
  return value.birthday instanceof Date;
}
```

请注意我们通常使用返回类型的特殊符号。

我们使用谓词作为任何其他函数，并将其放入最后一个`else if`条件:

# 针对 U 型收缩`nknown`

有了类型谓词，我们甚至可以处理`unknown`类型的变量。所要做的就是用类型谓词应用一个`if`条件，瞧，问题就解决了。

但我们必须小心，不应该欺骗自己。类型收缩和我们的验证逻辑一样好。

如果我们有一个类型为`unknown`的值，并且我们想要验证它是否具有`Person`的“形状”，我们必须想出一个比我们之前使用的代码更好的代码。对于“形状”，我们指的是任何具有属性`birthday: Date`的对象文字或实例。

## 9.1.手动验证

类型安全的类型谓词如下所示:

```
function isPerson(value: unknown): value is Person {
  return (
    typeof value === "object" &&
    value !== null &&
    "birthday" in value &&
    (value as { birthday: unknown }).birthday instanceof Date
  );
}
```

多么庞大的代码量啊！不幸的是，如果我们想要完全类型安全，这是必要的。

函数显示了 TypeScript 功能的另一个限制。尽管我们检查了`birthday`是`value`的一个属性，我们仍然必须在最后一个条件中应用类型断言来检查`birthday`是否属于类型`Date`。

我们可以预计随着时间的推移，TypeScript 的局限性会变得越来越小，但目前，它就是这样。

## 9.2.自动验证:zod

我们继续使用针对`unknown`类型的类型收缩。根据应用程序的类型，我们可能经常不得不处理`unknown`类型。显然，我们不想一直自己编写这些庞大的类型谓词。它耗费了相当多的宝贵时间。

幸运的是，不一定非要那样。有一些特殊的库可以自动进行验证。

其中最受欢迎的是“佐德”其他类似的库是“快速检查”或“是”

对于每一种类型，我们必须首先提出一个模式。这意味着我们以编程方式定义类型并将其存储到一个变量中。所以模式信息在运行时也是存在的。

然后，在类型谓词中使用生成的模式来验证值是否属于该类型。

有了 zod，我们的`isPerson`看起来会像这样:

```
const personSchema = z.object({
  birthday: z.date(),
});type Person = z.infer<typeof personSchema>;function isPerson(value: unknown): value is Person {
  return personSchema.safeParse(value).success;
}
```

首先，我们定义模式并将其存储在`personSchema`下。有了现有的模式，我们可以让 zod 为我们自动生成类型。这与`z.infer`一起工作。这简化了事情，并确保我们在更改`Person`类型时不会重复工作。

最后，我们使用方法`safeParse`，如果值不属于类型`Person`，它不会抛出错误。它通过`success`属性返回结果。

这比一直手动编写验证代码要好得多。

# 断言函数

断言函数是最后一个特性，它不是类型保护，但是非常方便。

当类型为`unknown`、`null`或空字符串时，我们返回第一个条件中的数字`0`。

返回数字`0`是一种方法。另一种方法是抛出一个错误。

如果我们抛出一个错误，TypeScript 的类型收缩将把`undefined`或`null`从函数的其余代码中排除。

断言函数就是这样一个特殊的函数。如果我们调用它，它将——类似于类型 guard——缩小参数范围，但它不返回布尔值。它向我们保证，如果值不是特定的类型，它会抛出一个错误。

处理不应该是`undefined`或`null`的类型是如此常见，以至于 TypeScript 甚至提供了一个特殊的类型实用程序。它被命名为`NonNullable<T>`。该类型实用程序意味着，无论`T`是什么类型，它都不是`undefined`或`null`。让我们看看`NonNullable<T>`和断言函数是如何工作的:

必须允许最后一个问题:当我们抛出一个错误时，为什么不首先从参数的联合类型中删除它们？比如`function calcAge(value: Date | string | Person | PersonJson): number {}`？

因为打电话的人可能会强迫我们加入。例如，在 Angular 中，禁用时，表单的输入值为`undefined`。因此，我们当然可以说，我们的程序逻辑不允许使用`undefined`，因为我们没有提供禁用功能。然而，我们的表单库的类型有它，这就是为什么它在参数的类型中。

# 摘要

本文展示了处理联合类型的各种技术，以及如何将它们缩小到一种特定的类型。TypeScript 可以验证这些模式，这意味着我们并不是要比编译器更聪明，而是要生成尽可能类型安全的代码。

相对于类型断言，我们应该总是倾向于类型收缩。这意味着更多的努力，但我们不必牺牲类型安全。

类型安全是我们使用 TypeScript 而不是 JavaScript 的主要原因。我们希望获得尽可能多的类型安全。从这个角度来看，正确使用类型收缩是应用程序开发人员最重要的类型脚本技能。

*原载于*【www.rainerhahnekamp.com】