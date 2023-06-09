# 用 TypeScript 编写可读和可维护的代码

> 原文：<https://betterprogramming.pub/writing-readable-and-maintainable-code-in-typescript-237accdd8b05>

## 如何让其他人更容易理解你的代码

![](img/4634738ec2497dd4fbbbc23554738d2b.png)

由[卡莱斯·拉巴达](https://unsplash.com/@carlesrgm?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

我最不喜欢的编程部分是不得不阅读那些不能立即理解的代码。如果你在团队中工作过，你可能会明白我的意思。例如:

这段代码并不容易阅读——你需要集中注意力，并且可能需要阅读多次才能完全理解它。还有零输入，所以不清楚使用该函数时应该发生什么。现在看看这段代码:

您可以立即看到这是一个将数组分割成块的函数。如果您要使用这个函数，您的 IDE 将准确地显示哪些参数是被请求的，以及您期望的输出是什么。就实现而言，逻辑命名的变量和空白使代码易于理解。

您可能会指出，第二个示例比第一个示例需要更长的时间来编写代码。这可能是真的，但是第二个例子将通过使函数更容易使用来节省时间。不用查就知道函数会做什么。打字使使用这个函数更容易，因为你的 IDE 的智能感知会告诉你如何使用它。您也可以更容易地定位错误，因为它更容易阅读。你理解得更快更深刻。

chunk array 函数可能是一个简单的例子，但是对于更复杂的代码，同样的逻辑也适用。

# 改善你和你的团队代码的 5 个技巧

我将分享我作为团队的一员在从事 TypeScript 项目时学到的一些东西。遵循这些提示使得阅读和维护我们的代码变得更加容易，并且减少了新团队成员变得高效的时间。

> 不要给自己写代码，要给别人写代码。

换句话说，记住:其他人会阅读你的代码。也许不是今天，甚至不是下周，但它会发生。

# 1.使您的 tsconfig 尽可能严格

这可能是我在这篇文章中给你的最重要的提示。让 TypeScript 做它应该做的事情:防止人为错误。最好的方法是启用强制您遵循某些规则的 TypeScript 选项。

但是不要仅仅启用所有的 TypeScript 特性。仔细考虑哪些选项会对您的团队有所改进。在某些情况下，启用某些功能可能会适得其反。

## 严格选项

我强烈建议您使用 TypeScript 中的几个严格选项:

`--noImplicitAny`
在隐含 any 类型的表达式和声明中引发错误。

`--noImplicitThis` 使用隐含的 any 类型在“this”表达式上引发错误。

`--alwaysStrict`
在严格模式下解析，对每个源文件发出“use strict”。

`--strictNullChecks`
在严格的空值检查模式下，空值和未定义值不在每个类型的定义域内，只能赋给自身和 any(唯一的例外是 undefined 也可以赋给 void)。

`--strictFunctionTypes`
禁用函数类型的双变量参数检查。

我称这些选项为严格选项，因为它们都是在使用`--strict` 标志时设置的。还有一些选项需要设置，但是根据您使用的框架，使用起来可能会很麻烦。

例如，使用带有角度冲突的`--strictPropertyInitialization`与`@Input`、`@Output`和`@ViewChild(ren)`装饰器有点冲突。

## 非严格选项

在 Typescript 中有更多的选项可以提高代码质量，我强烈建议您去探索它们。你可以在[打字手册](https://www.typescriptlang.org/docs/handbook/compiler-options.html)中找到所有可用的选项。

# 2.可读性比性能更重要

## 变量名和函数名

不要总是使用单字母变量。您可能会节省几毫秒——甚至几行——但这是以牺牲可读性为代价的。

总是问:“有人会在第一次读这段代码时理解它吗？”如果答案是“是”，就给自己一个鼓励。如果答案是“不”,想一个好名字，使变量的目的清楚——它使你的代码更容易理解。

你的函数名也是如此。如果你看到一个函数的名字，它的参数和它所属的类，你应该知道这个函数是做什么的。如果你不知道这个函数是做什么的，它需要一个更好的名字。

## 表演

这取决于您正在进行的项目的类型，但是在大多数情况下，为了节省一毫秒的时间而使您的代码变得更难理解是不值得的。难以理解的代码更容易导致 bug。

> 首先关注正确性，然后是清晰性，最后，如果需要，关注性能。

如果你确实需要表演，确保你使用正确的命名，并添加清晰的注释。这样你就可以避免你的队友浪费时间去理解复杂的代码。

# 3.林挺

如果你不强制一种代码风格，每个人都将按照他们喜欢的方式编码。有些会使用尾随逗号，有些会使用 4 个空格缩进，有些会用下划线作为私有变量的前缀。简而言之，每个文件看起来都不一样。

使用像 [TSLint](https://palantir.github.io/tslint/) 和/或[pretty](https://prettier.io/)这样的工具，你可以创建你想要的代码外观规则。试着让你的林挺尽可能的严格——当然，如果你遗漏了一个逗号或者分号会很烦人，但是阅读别人的代码会更容易，因为你知道会发生什么。

# 4.项目结构

没有意义的项目结构是很费时间的。当您试图解决一个 bug 时，却找不到处理该特定逻辑的文件，这是很烦人的。

我不能为你建议一个项目结构，但是我可以建议你在随机创建文件夹和文件之前思考一下。甚至可以和你的团队坐下来创建一个项目结构，让每个人都能找到他们想要的东西。否则，谁知道你最终会得到什么？

# 5.结对编程

有时当你在解决复杂的问题时，两人一组会很有用。结对工作比单独工作有几个优点:两个人会更好地理解复杂的问题，四只眼睛比两只眼睛更快地发现 bug。

第二个人也可能得出不同的解决方案。有时，将两种解决方案结合起来会比任何一个人单独解决问题都要好得多。

然而，不要总是两人一组。你还必须能够独立编程，不能总是依赖别人。

# TL；速度三角形定位法(dead reckoning)

*   让你的`tsconfig`尽可能严格。
*   首先关注正确性，然后是清晰性，最后，如果需要，关注性能。
*   使用 TSLint 和/或更漂亮的强制代码样式。
*   使在项目结构中查找相关文件变得容易。
*   成对编程对于解决复杂问题很有用。

# 最后一个音符

这些都是我从自己的经历中学到的。不要低估高质量代码库的好处。这会让你的生活更轻松。

[](https://medium.com/@freek_mencke/how-to-write-node-js-applications-in-typescript-a54af696f66) [## 如何用 Typescript 编写 Node.js 应用程序

### 并使它们更容易维护，同时产生更少的错误

medium.com](https://medium.com/@freek_mencke/how-to-write-node-js-applications-in-typescript-a54af696f66)