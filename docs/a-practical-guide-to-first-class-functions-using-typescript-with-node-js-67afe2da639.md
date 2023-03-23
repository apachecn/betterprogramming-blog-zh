# 使用 TypeScript 和 Node.js 的一级函数实用指南

> 原文：<https://betterprogramming.pub/a-practical-guide-to-first-class-functions-using-typescript-with-node-js-67afe2da639>

## *使用 TypeScript* 学习并实现函数式编程的核心概念

![](img/3d6d41055dab1cee1273da6411282084.png)

照片由 [Antoine Dautry](https://unsplash.com/@antoine1003?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/math-function?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

# *为什么是一流的功能？*

函数式编程作为一种编程范式越来越受欢迎。LISP 和 Haskell 领先。然而，我们这些全栈开发者很幸运，JavaScript 原生支持它的特性。

函数式编程的核心概念之一是*一级函数。*意思是，我们将职能部门视为一等公民。它通过像对待任何其他值(对象、字符串和数字)一样对待它们，将它们作为参数传递，或者从代码中的其他函数返回它们，为我们实现函数提供了灵活性。理解一流的函数是学习和实现函数式编程范例的重要基础。最终目标是让我们编写可维护的、灵活的和可伸缩的代码。

在本文中，我将带您了解一个一级函数的不同特性。我会分享一些例子，样本问题，以及解决方案。注意:我用 TypeScript 编写了我所有的例子，并用 Node 10 进行了测试。

# 箭头功能—速记

在本文的其余部分，我将默认使用在 ES6 中表达函数的简写方式。为了使我们的代码更加简洁，让我们通过回顾和理解如何在 ES6 中编写箭头函数来确定基调。如果您已经熟悉箭头功能，可以跳过这一部分。

当我们在 ES6 之前编写函数时，我们通常会使用`function`关键字，例如，如果我们想编写一个返回胜率的函数。我们编写函数并给它命名，或者通过使用`const`、`var`或`let`将它赋给一个变量。

TypeScript 中的命名函数

从 ES6 开始，我们可以将函数重写为箭头函数，如下例所示。

ES6 中引入的箭头功能

我们不需要写`function`关键字。箭头`=>`指向我们函数中用花括号括起来的指令集。在 TypeScript 中，如果我们愿意，我们也可以指定一个变量是一个`Function`类型。

下面的例子直接返回结果，而没有保存在变量中。这进一步缩短了我们的代码。

带有箭头功能的一行程序

注意上面的箭头`=>`是如何告诉解释器我们想要返回它所指向的语句的值的，这是我们用`matches`除以`wins`的结果，只要我们没有用花括号将语句括起来。

如果指定类型，我们需要在 TypeScript 中将参数括在括号中，这与使用 JavaScript 箭头函数不同。如果我们只需要传递一个参数，我们可以省略括号。

带有一个类型化参数的箭头函数

如果没有传递参数，请使用空括号。

不带参数的箭头函数

如果我们想返回一个对象，用括号把它括起来。如果我们没有用括号把它括起来，我们就告诉解释器我们在用括号来定义函数体。

如果要返回一个对象，请用括号将对象括起来。

以这种速记形式编写函数允许我们“压缩”我们的代码，节省我们打字时的额外击键和阅读时的额外字符。以速记形式编写函数在开始时可能会有点混乱；然而，从长远来看，这里有复利可得。除了让我们免于额外的击键和对单词 *function* 的多余使用之外，它还方便了我们以函数式编程风格编写我们的类型代码。怎么会？箭头函数类似于数学方程的组成部分，这使得我们的代码简洁明了。

# 将函数视为数据

我们可以像对待其他类型脚本数据类型一样对待函数，比如数字、字符串、数组和对象。我们可以将函数存储在变量中，并像对待任何其他 TypeScript 数据类型一样对待它，这为我们提供了一些有用的选项来处理我们的函数。

这是一个将函数赋给另一个变量的例子。

创建一个函数，并将其赋给另一个变量。

像对待其他数据类型一样对待函数也允许我们动态地改变函数的行为。

使用三元运算符动态更改函数行为。

下面是该功能的一些典型的实际应用。

## 示例用法 1。单元测试中的模仿

该特性的一个常见应用是模拟我们的异步函数在本地运行单元测试时返回的内容。

## 示例用法 2。A/B 测试或实验

我们还可以将我们的函数分配给一个对象，以形成一个哈希映射或函数字典，我们可以用它来进行 A/B 测试。

## 示例用法 3。对一个数据执行一系列功能

这是另一个有趣的应用。我们有许多函数接受一个对象，对该对象执行一系列更新，然后返回一个更新后的对象。

```
const addName = (person: object): object => (
    {...person, name: 'John Doe'})
const addGender = (person: object): object => (
    {...person, gender: 'Male'})
const addJobTitle = (person: object): object => (
    {...person, title: 'Software Engineer'})
const addMonthlySalary = (person: object): object => (
    {...person, monthlySalary: 1000})
const addAnnualSalary = (person: object): object => (
    {...person, annualSalary: person['monthlySalary'] * 12})
```

程序性的方法是逐行应用函数。例如，我们有一个用`id`初始化的`employee`对象。然后一系列函数将更新`employee`对象并返回更新后的`employee`对象。请参见下面的示例代码。

获取 person 对象并更新它。

上面的代码完成了这项工作。我们一行一行地将每个函数应用到变异的`employee`对象上。想象一下，如果我们需要添加一个新函数:添加一个新行来调用函数，并将`employee`对象作为参数传递。不错，但我们可以做得更好。

函数方式是在一个数组中分配函数，使用`forEach`遍历该数组，并将每个函数应用于参数。注意在应用数组中的第一个函数后要传递的`employee`参数将是前一个函数的返回值。我们按照函数在数组中的排列顺序来使用它们。

获取 person 对象，并使用一组函数更新它。

如果我们需要添加另一个函数来执行，我们只需要在数组中添加另一个条目。我们不需要将返回值重新分配给`employee`，并将其作为参数传递给下一个函数。

# 作为参数的函数

回到动态改变函数行为的主题，让我们看看如何通过传递函数作为参数来实现。

例如，我们想实现一个接受两个数字的简单计算器。面向对象的方法是创建一个包含计算操作的`Calculator`类作为该类中的方法，即`add`和`subtract`。如果我们需要添加更多的操作，我们将更新类来添加每个操作的相应方法。另一种方法是为每个操作编写单独的函数。例如，实现`calculateSum()`和`calculateDifference()`功能。

这里实现计算器的“一级函数”或“函数式”方法是创建一个接受运算函数的`calculate()`函数，例如`add()`，作为执行我们想要的计算的参数。计算的函数也是独立的，我们可以使用它们，而不用将它们作为参数传递。

将运算函数作为参数传递给 calculate 函数。

# 用作返回值

一开始，看到从一个函数返回另一个函数的代码可能会感到困惑。把它想象成一个功能工厂会有所帮助。

理解这个概念的一个更简单的方法是从返回另一个将文本打印到控制台的函数开始。下面的函数返回一个将“hello”打印到控制台的函数。

```
const createGreeting = () => () => console.log('hello')
```

如果您是新手，可能需要一些时间来习惯阅读这种类型的函数。请记住，这个函数相当于下面的函数。

```
const createGreeting = function() {
    return function() {
        console.log('hello')
    }
}
```

让我们看一个例子。我们有一个带有转换功能的程序，可以将`meter`转换成英制单位`mile`、`yard`、`foot`和`inch`。这些单位转换函数采用以米为单位的值`x`，并返回以相应符号格式化的英制单位转换后的值。

具有重复代码的转换函数

在上面的例子中，我们的转换函数中的重复代码非常明显。每个函数中的乘法和字符串格式化都是重复的。

让我们尝试通过创建一个函数来解决这个问题，该函数将为我们处理不同的转换，同时最大化行为略有不同的函数之间的代码重用。

下面的`createConverter()`函数接受转换值`v`和符号`s`参数。它返回一个带有`x`参数的函数，这是我们想要转换的值。

```
const createConverter = (v: number, s: string) => 
    (x: number) => `${x * v} ${s}`
```

然后我们将重用上面的`createConverter`函数来构成我们的转换函数。

```
const meterToMile = createConverter(0.0006213689, 'mi')
console.log(meterToMile(1000))
```

下面是我们的完整解决方案。我们在保持代码可维护性的同时最大化重用。

具有可重用代码的转换函数。我们利用函数作为返回值。

# 关闭

既然我们已经讨论了从另一个函数返回一个函数，我们应该理解返回一个函数的函数中变量的作用域。返回函数的变量可以在返回函数中访问。

下面`createGreeting()`函数中的变量`_name`可从打印到控制台的返回函数中访问。当我们运行它时，它会打印出`Hello John Doe`。

带闭包的函数

# 私有变量

这一节将结合我们在前面几节中学到的关于返回函数和闭包的知识。通常，在 JavaScript 中，我们会在私有变量后添加一个下划线，以表明我们不应该在类之外的任何地方使用该变量。有时这可能被忽略或误用。我们可以通过使用函数方法来强制执行面向对象编程中的类如何处理私有变量。我们将把它实现为一个函数，而不是在我们的例子中使用`class`。

看看下面的例子。我们有一个接受`name`、`jobTitle`和`monthlySalary`参数的`Employee`函数。该函数将这些参数赋给私有变量，并附加一个下划线以表示它们是私有的。我们只能通过 getters 来访问这些变量:`getName()`、`getJobTitle()`和`getEmployee()`。如果我们试图直接检索私有变量`employeeA._name`，我们将得到一个`Property ‘_name’ does not exist`错误。

带有私有变量和 getters 的雇员函数

我们还可以扩展我们的`Employee`函数来使用 setters。我们在下面的例子中添加了 setters。

带有设置器的私有变量

上面代码中的最后两行将输出我们员工更新后的职位和工资。

请注意:您并不总是需要像这样在 TypeScript 中编写您的“类”,因为 TypeScript 已经支持在类中实现私有变量。很高兴知道您可以选择以这种方式实现私有变量。

# 高阶函数

高阶函数是通过将任何函数作为参数或通过返回它们来对其他函数进行操作的函数。让我们来看一个例子，看看如何使用高阶函数来给我们的代码带来灵活性和可重用性。

为了用一个例子来说明这一点，让我们来看看我们作为程序员解决的一个常见问题:验证我们的函数参数。

我们有一个执行减法运算的函数，给定一个`minuend`和一个`subtrahend`作为它的参数，并返回`difference`。

```
const minus = (minuend: number, subtrahend: number): number => 
    minuend - subtrahend
```

> 被减数——这是一个最大的数，或者说是整个数，其中的一个**部分**将被去掉。
> 
> 减数——这是从被减数中去掉的**部分**。
> 
> 差异—这是**减去**后剩余的**部分**。[ [1](#36b0)

让我们试着限制它只返回非负数。因此，我们将需要我们的论点，使`minuend`应该总是大于或等于`subtrahend` : `minuend >= subtrahend`。

验证参数的典型方法是在函数的顶部添加一个早期返回或退出。

```
const minus = (minuend: number, subtrahend: number): number => {
    if (minuend < subtrahend) {
        console.log('minuend should be greater than the subtrahend')
        return null
    }
    return minuend - subtrahend
}
```

上面的代码是有效的。然而，这个函数做的不止一件事。它不仅返回差异，还验证参数。这违反了 S.O.L.I.D.[ [2](#c5e0) ]中的单一责任原则

让我们用高阶函数来解决这个问题。

首先，我们将创建一个单独的函数来验证参数。

```
const minuendGreaterThanSubtrahend = (func: Function) => 
    (...args) => {        
        const [num1, num2] = args
        if (num1 < num2) {
            console.log('minuend should be greater than the subtrahend')
            return null
        }
        return func(...args)
    }
```

函数`minuendGreaterThanSubtrahend()`接受作为参数传递的函数。然后，它验证将从传递的函数中调用的参数。

让我们删除我们的`minus()`函数中的验证，让它回到符合单一责任原则的原始形式。

```
const minus = (minuend: number, subtrahend: number): number => 
    minuend - subtrahend
```

然后，我们将创建调用`minuendGreaterThanSubtrahend()`验证函数的`minusPositiveDifference()`函数，并将`minus()`函数作为其参数。然后将调用这个`minusPositiveDifference()`来确保在执行减法之前首先验证我们的参数。

```
const minusPositiveDifference = minuendGreaterThanSubtrahend(minus)
```

查看下面我们的完整解决方案。每个函数都在做一件事，同时仍然实现我们想要的参数验证。

一种函数式的方法来验证参数，同时保持我们的代码符合单一责任原则

# 结论

理解 JavaScript (TypeScript)中的一级函数对您的函数式编程之旅至关重要。它帮助我们在利用一流功能的强大功能和灵活性的同时，以令人兴奋的方式处理问题。您将会遇到与本文中的例子类似的模式问题。您在项目中使用这些一流的功能特性越多，您就越能掌握它们。

# 参考

1.  [减法方程式](https://classace.io/learn/math/3rdgrade/completing-three-digit-subtraction-equations)[回文](#7c40)
2.  [神盾局原则。](https://en.wikipedia.org/wiki/SOLID)[回文](#0c82)

如果您想运行本文中的示例代码，也可以在 [GitHub](https://github.com/ardydedase/fp-node-ts) 上找到。