# 您可以学习的 9 个有用的函数式编程解决方案(在 TypeScript 和 Node 中)

> 原文：<https://betterprogramming.pub/9-useful-functional-programming-solutions-you-can-learn-with-typescript-and-node-2eab5ba097f0>

## web 开发人员易于理解的编码示例

![](img/f6c3205eeb0c3222da0a6c00e2347030.png)

由[杰斯温·托马斯](https://unsplash.com/@jeswinthomas?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/math-board?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片。

*对于葡萄牙语读者:感谢*[*Alison Miazaki*](https://medium.com/u/716f61682dba?source=post_page-----2eab5ba097f0--------------------------------)*将我的故事翻译成葡萄牙语:*[*9 Solu es práticas em Programa ao funcial para VOCêsaber(em TypeScript e Node)*](https://alisonmiazaki.medium.com/9-solu%C3%A7%C3%B5es-pr%C3%A1ticas-em-programa%C3%A7%C3%A3o-funcional-para-voc%C3%AA-saber-em-typescript-e-node-b69af9ef86ea)*。*

作为软件开发人员，我们都经历过这种情况:我们正在编写或审查一段代码。然后不知从哪里，我们的直觉告诉我们可以进一步简化代码。或者，如果我们熟悉命令式编程，似乎有一种“函数式”方法可以解决问题。“功能性”解决方案唾手可得，但我们却无法将它写下来。

我写这篇文章是为了帮助自己摆脱这种情况。毕竟，我们可以从任何地方获得灵感。这是一个找到正确的解决方法的提示的问题。提示越接近我们正在寻找的解决方案，效果越好。

我们将按照问题的简单性来完成一系列常见的编码任务。我对 Node 10 使用了 TypeScript 来测试每个解决方案。

# 1.单元测试中的模仿

## 问题

测试异步函数时，您希望在本地运行测试时使用模拟数据。

## 非功能方法

使用一个`if-else`语句来决定运行哪个函数，是异步的还是模拟的。

## 功能方法

将异步和模拟函数赋给它们的变量。使用三元运算符根据当前环境确定使用哪个函数。

作者的代码片段。

# 2.A/B 测试或实验

## 问题

您想要显示 web 应用程序的两个或更多不同的变体:变体 A 和 b。

## 非功能方法

使用一个`if-else`语句来决定显示哪个变量。

## 功能方法

将显示函数分配给它们的变量。创建一个对象，将函数变量映射到它们对应的变量。使用变量作为对象键，使用函数变量作为对象值。使用对象 hashmap 显示变量。

作者的代码片段。

# 3.对一段数据执行一系列函数

## 问题

有许多函数接受一个对象，对该对象执行一系列更新，然后返回一个更新或转换后的对象:

作者的代码片段。

## 非功能方法

您可以逐行执行该函数。例如，用一个`id`初始化一个`employee`对象。然后一系列函数将更新`employee`对象并返回更新后的`employee`对象。

作者的代码片段。代码从第 17 行开始执行函数。

上面的代码完成了这项工作。你一行一行地将每个函数应用到变异的`employee`对象上。想象一下，如果你需要增加一个新的功能。您可以添加新的一行来调用该函数，并将`employee`对象作为参数传递。不错，但我们可以做得更好。

## 功能方法

函数方式是在一个数组中分配函数，使用`forEach`遍历该数组，并将每个函数应用于参数。注意，在应用数组中的第一个函数后传递的`employee`参数将是前一个函数的返回值。函数的使用顺序与它们在数组中的放置顺序相同。

作者的代码片段。第 17 行指定了数组中的函数。

如果需要添加另一个函数来执行，则在`personFunctionsArray`数组中添加另一个条目。您不需要将返回值重新分配给`employee`，并将其作为参数传递给下一个函数。

# 4.简单计算器

## 问题

您希望实现一个接受两个数字的简单计算器。

## 非功能方法

面向对象的方法是创建一个包含计算操作的`Calculator`类作为该类中的方法，即`add`和`subtract`。如果您想要添加更多的操作，您将更新该类以添加每个操作的相应方法。另一种方法是为每个操作编写单独的函数。例如，实现`calculateSum()`和`calculateDifference()`功能。

## 功能方法

创建一个`calculate()`函数，该函数接受一个操作函数(如`add()`)作为参数来执行您想要的计算。计算的函数也是独立的，您可以使用它，而无需将其作为参数传递。

作者的代码片段。将运算函数作为参数传递给 calculate 函数。

# 5.转换计算器

## 问题

将`meter`转换成英制单位`mile`、`yard`、`foot`、`inch`。这些单位转换函数采用`meter`中的值`x`，并返回转换后的值，该值采用英制单位，并以其对应的符号进行格式化。

## 非功能方法

为每个转换写一个函数。

作者的代码片段。转换从第 6 行开始。

在上面的例子中，转换函数中的重复代码很明显。每个函数中的乘法和字符串格式化都是重复的。

## 功能方法

尝试通过创建一个处理不同转换的函数来解决这个问题，同时最大化行为略有不同的函数之间的代码重用。

下面的`createConverter()`函数接受转换值`v`和符号`s`参数。它返回一个采用`x`参数的函数，这是您想要转换的值。

```
const createConverter = (v: number, s: string) => 
 (x: number) => `${x * v} ${s}`
```

重复使用上面的`createConverter`函数，形成转换函数。

```
const meterToMile = createConverter(0.0006213689, 'mi')
console.log(meterToMile(1000))
```

您在保持代码可维护性的同时最大化了重用。

# 6.验证函数参数

## 问题

您有一个执行减法运算的函数，给定一个`minuend`和一个`subtrahend`作为它的参数，并返回`difference`。返回值只能是非负数。因此，您需要验证您的参数，使得`minuend`应该总是大于或等于`subtrahend` : `minuend >= subtrahend`。

```
const minus = (minuend: number, subtrahend: number): number => 
    minuend - subtrahend
```

据[级高手](https://classace.io/learn/math/3rdgrade/completing-three-digit-subtraction-equations):

> -"`minuend`——这是最大的数，或者说是整体，从中要拿走一部分。
> 
> - `subtrahend` —这是从`minuend`上取下的零件。
> 
> -差——这是减法后剩下的部分。"

## 非功能方法

验证参数的典型方法是添加一个有条件的提前返回或退出。

上面的代码是有效的。然而，这个函数做的不止一件事。它不仅返回差异，还验证参数。违反了[固体](https://en.wikipedia.org/wiki/SOLID)中的单一责任原则。

## 功能方法

首先，创建一个单独的函数来验证参数。

```
const minus = (minuend: number, subtrahend: number): number => 
    minuend - subtrahend
```

创建调用`minuendGreaterThanSubtrahend()`验证函数的`minusPositiveDifference()`函数，并将`minus()`函数作为其参数。然后将调用这个`minusPositiveDifference()`来确保在执行减法之前首先验证您的参数。

```
const minusPositiveDifference = minuendGreaterThanSubtrahend(minus)
```

每个功能在解决问题的同时都在做一件事情。

作者的代码片段。在保持代码符合单一责任原则的同时验证参数的函数式方法。

# 7.单词计数器

## 问题

你把一串单词或一首诗赋给了一个变量。您希望计算一个单词在字符串中出现的次数。这个问题类似于拥有一个数组并想计算数组中某些元素出现的次数。

```
const poemInput: string = 
`Hold fast to dreams
For if dreams die
Life is a broken-winged bird
That cannot fly
Hold fast to dreams
For when dreams go
Life is a barren field
Frozen with snow`.replace(/(\r\n|\n|\r)/gm, ' ').toLowerCase()
```

若要简化字符串，请用空格替换换行符，并将其转换为小写。

## 非功能方法

使用散列表来记录单词出现的次数。

## 功能方法

使用带有`wordCounter`散列表的`reduce`作为累加器，使用`word`作为元素进行处理。

使用 spread 操作符来提取`wordCounter`散列表的键和值。

使用三元运算符检查单词是否已经在散列表中。如果它在 hashmap 中，就增加这个值。否则赋值`1`，表示该词第一次出现。

作者的代码片段。如果您已经熟悉了函数式编程，那么函数式解决方案会更简洁、更易于阅读。

# 8:寻找字谜

## 问题

> 变位词是通过重新排列不同单词或短语的字母而形成的单词或短语，通常使用所有原始字母一次— [维基百科](https://en.wikipedia.org/wiki/Anagram)

给定一个单词，您希望从提供的单词数组中找到它的变位词。

例如，在注释中有一组单词及其相应的变位词。

```
const words: Array<string> = [
  'rat',      // tar
  'car',      // arc
  'below',    // elbow
  'taste',    // state
  'cried',    // cider
  'study',    // dusty
  'thing',    // night
  'chin',     // inch
  'grab',     // brag
  'act',      // cat
  'robed',    // bored
  'vase',     // save
  'glean',    // angel
  'desserts', // stressed
]
```

输入可以是可能的变位词之一。

```
const input: string = 'save'
```

## 非功能方法

网上有很多这种问题的非功能性解决方案的例子，这里就不一一赘述了。

## 功能方法

您可以结合使用 JavaScript 内置的`filter`和`reduce`函数来解决这个问题。改变示例 7 中使用`reduce`的同一个`countWords`函数的用途，但是不是计算单词，而是计算单词中的字母。

```
const countLetters = (word: Array<string>) =>
  word.reduce((letterCounter: Map<string, number>, letter: string) => ({
    ...letterCounter,
    [letter]: letterCounter[letter] ? letterCounter[letter] + 1 : 1,
  }), new Map<string, number>());
```

使用`filter`功能过滤与您的`input`值具有相同字母和长度的单词。

```
const findAnagrams = (word: string, words: Array<string>): Array<string> => {
  return words
    .filter(entry => hasSameLetterCount(word, entry))
    .filter(anagram => anagram !== word);
}
```

最终的解决方案如下:

作者的代码片段。该解决方案结合使用了 filter()和 reduce()函数。

# 9.表单验证

## 问题

您有一个接受用户输入的表单，您需要验证输入。一些简单的验证标准可以包括至少三个字符长的用户名，或者用户需要输入有效的电子邮件地址。

```
const currentInputValues: Object = {
  username: 'ar',
  email: 'me@',
}
```

## 非功能方法

网上有很多这种问题的非功能性解决方案的例子，我就不在这里赘述了。

## 功能方法

从输入标准的 hashmap 开始，其中键是字段，值是输入标准的数组。例如:

`hasMinCharacters`和`hasValidEmail`功能检查输入并返回错误信息。

使用`reduce`和`map`的组合，通过根据相应的验证标准测试每个输入，积累返回的错误信息。使用`filter`排除空字符串。

作者的代码片段。该解决方案使用 reduce、map 和 filter。

# 结论

概括一下:

*   示例 1、2 和 3 是如何将函数视为数据的好例子。
*   示例 4 展示了您可以使用函数作为参数。
*   示例 5 展示了您可以使用函数作为返回值。
*   示例 6 展示了如何使用高阶函数。
*   示例 7-9 结合了前面的概念和内置函数，如`map`、`reduce`和`filter`。

我并不是建议您在遇到同样的问题时，应该总是使用上面概述的功能性解决方案。有时这是偏好或编码风格的问题。如果你在写下你的解决方案之前考虑其他因素也会有所帮助，比如你现有的代码库、团队成员和编码指南。

我希望您的函数式编程之旅到目前为止都很有趣！

您可以在下面找到本文中使用的代码示例:

[](https://github.com/ardydedase/fp-node-ts) [## ardydedase/fp-node-ts

### 在 GitHub 上创建一个帐户，为 ardydedase/fp-node-ts 开发做贡献。

github.com](https://github.com/ardydedase/fp-node-ts)