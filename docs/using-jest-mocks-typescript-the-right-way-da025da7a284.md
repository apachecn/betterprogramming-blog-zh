# 正确使用 Jest Mocks + TypeScript

> 原文：<https://betterprogramming.pub/using-jest-mocks-typescript-the-right-way-da025da7a284>

## 编写更好的单元测试

![](img/d83ff41a12f577c611186a022b8aebb3.png)

托尼·昆卡在 [Unsplash](https://unsplash.com/s/photos/plastic-flamingo?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片。

*注意:本文假设读者对 Jest 和 React 测试库有基本的了解。如果你不熟悉 React 的一般测试结构，我推荐从* [*这个系列*](https://medium.com/javascript-in-plain-english/testing-in-react-part-1-types-tools-244107abf0c6) *开始。*

模仿是对 React 应用程序进行单元测试的核心原则。它是遵循 RTL 不测试实现细节的最佳实践的关键工具，也是所有单元测试中隔离的基本思想。

Jest 文档涵盖了各种嘲弄的方法和几个非常有用的用例。但是它缺少的是对*你在嘲笑什么东西的时候*在做什么的基本概述。我发现这是将他们概述的方法成功应用到您自己的应用程序中的必要条件。

唉，我们今天的主题是:如何正确模拟生产 React 应用程序所需的东西？我们将在哲学层面上讨论模仿，并浏览本地`Config`文件的基本模仿以及 [Auth0 的 React SDK](https://github.com/auth0/auth0-react) 。

我之所以选择 Auth0 SDK，是因为它有非常具体的用于在浏览器中运行的用例，并且通常是您想要在代码中测试的核心。换句话说，这是一个完美的用例，用于需要被嘲笑的东西，并且需要被适当地嘲笑*以便测试。*

![](img/985651411d6b38aff3a8a6b33b672b37.png)

由[马库斯·温克勒](https://unsplash.com/@markuswinkler?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/goal?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片。

# 单元测试实现了什么？

单元测试的目标是确认您(或您团队中的某个人)编写的代码块按预期工作。就是这样。

单元测试*不是*想要做的，并且*也不应该*测试从第三方依赖项导入的函数和模块是否工作，或者代码获取的 API 的状态——或者任何特定于浏览器的东西，比如网络或安全问题。

为了正确地进行单元测试，我们需要将被测试的代码单元与所有这些其他关注点隔离开来。这允许我们自信地断言代码块的结果。

![](img/47fd5faf8d55c63f9c8038b6b105d999.png)

照片由 [Fabian Mardi](https://unsplash.com/@fabianmardi?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/isolation?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄。

# 寻求与世隔绝

如果你正在开发一个 React 应用程序，这个应用程序不可避免地会用到我刚刚提到的所有东西——第三方依赖、浏览器、API 等等。我们不希望这些东西破坏我们的测试*。*

一个明显的例子是一个依赖于从 API 获取成功返回的代码单元。在测试环境中，它不能从这个 API 获取数据，因此每次都会失败。它失败是因为 API 的失败。然而，正如所讨论的，API 不是我们想要测试的*。*

这就是嘲讽的由来。模拟允许您接管这些东西中的任何一个的功能，并出于测试的目的控制它。您可以伪造从 API 调用返回的数据集，规避浏览器安全要求，模拟环境变量，等等。

![](img/df593bf2b8616279a75c77f343febe22.png)

照片由[蒂姆·阿特伯里](https://unsplash.com/@tim_arterbury?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/mock?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄。

# 准备模拟练习

如果你还没有浏览过 [Jest 的嘲讽文档](https://jestjs.io/docs/en/mock-functions)，请花点时间浏览一下。您将大致了解到，我们使用`jest.mock()`,将路径或依赖项名称作为参数传入。有时会有一个回调作为第二个参数传入。有时候没有。有时看起来我们只是直接将`jest.fn()`存储在一个变量中，然后调用它。正如文档所述，这里的例子是人为设计的，所以我们将使用我们自己的例子。

## `jest.mock()`

这个函数是一切开始的地方——至少就我们的模拟来说是这样。

当您将相对路径或第三方模块的名称传递给这个函数时，您是在对 Jest 测试运行人员说，“嘿，在我们现在测试的代码中，您会发现我们使用了来自`<relative path or third-party module name>`的代码。但是我们实际上并不想在这里使用这段代码，因为它在测试环境中是行不通的。此外，我们正在争取隔离。所以当你运行*我的*代码，并从`<relative path or third-party module name>`得到这个其他代码时，不要使用你将在`<relative path or third-party module name>`中找到的实际代码。我们将为测试伪造代码。”

对于依赖关系，这看起来像这样:

```
jest.mock('@auth0/auth0-react');
```

对于相对路径，它应该是:

```
jest.mock('../Config');
```

单单这一行就得到了“嘿，笑话。不要使用实际的代码。我们要假装“任务完成。然而，它没有告诉笑话*你如何*想要伪造它。

为了告诉 Jest 如何伪造它，我们传递一个回调作为第二个参数。回调应该返回与实际代码形状相同的东西，因为这是您编写的代码所期望的。`@auth0/auth0-react`包返回一个`useAuth0`钩子、一个用于上下文的`Auth0Provider`组件，以及像`withAuthenticationRequired`这样的助手函数，等等。假设我们的代码使用了这些，我们可能会这样模仿这个包:

假设我们的配置文件返回了一个环境变量的对象，比如`NODE_ENV`、`API_KEY`和`API_SECRET`，我们将会:

现在，当 Jest 运行您的代码并到达`@auth0/auth0-react`和`'../Config'`代码时，它将分别实现从模拟代码而不是实际代码的返回。

## jest.fn()

你会注意到上面我们在`@auth0/auth0-react`模拟中使用了`jest.fn()`。这是因为，在这个包的实际实现中，这个包将每个`useAuth0`、`Auth0Provider`和`withAuthenticationRequired`作为可调用函数返回。对这些属性使用`jest.fn()`允许我们进一步模拟从我们的包返回的函数的实现。

类似于`jest.mock()`，`jest.fn()`简单地说，“我们要模仿这个函数做什么”，但是它没有告诉 Jest *我们想要如何模仿它。但是将它分配给`jest.fn()`允许我们使用 Jest 的功能，比如`.mockResolvedValue()`、`.toHaveBeenCalled()`、`.toHaveBeenCalledWith()`等等。*

在某些情况下，简单地模仿函数——使其可调用，以便您的代码可以继续运行——就足够了。但通常，我们希望控制返回或确认它已被调用，这就是这些助手的作用。但是…我们怎么称呼它们呢？

# 模仿实现

坚持使用`@auth0/auth0-react`，我们现在已经告诉 Jest 去模拟模块，但是没有可以操作的模块。我们不能访问`useAuth0`、`Auth0Provider`和`withAuthenticationRequired`来告诉他们我们希望他们如何行动。

我们通常首先将模块或函数导入到文件中，这样我们就有了要操作的函数的实例:

这个导入，连同下面的 mock，现在给了我们`useAuth0`、`Auth0Provider`和`withAuthenticationRequired`作为模仿的 Jest 函数。尽管从语法上来说，我们是从*实际的第三方依赖关系*中导入的，但就 Jest 环境而言，这些导入实际上是模拟的。

![](img/12427f6017113978b6d9c819905011e0.png)

J. Kelly Brito 在 [Unsplash](https://unsplash.com/s/photos/coloring-book?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片。

# 利用模拟函数进行测试

从根本上说，模拟为我们的测试提供了两个绝佳的机会。通过模拟，我们可以:

1.控制退货、已解决或已拒绝的值。

2.“观察”mock 是如何在我们的代码中使用的。

TypeScript 使这个问题稍微复杂了一些，但是我们将讨论如何避开 TypeScript 的类型检查。

## 。mockReturnValue()和。mockReturnValueOnce()

这是事情变得真正有趣的地方。现在我们已经用 Jest 嘲笑了我们的函数，我们获得了对它们返回的内容的控制，允许我们在不陷入实现细节的情况下做出断言。顾名思义，`.mockReturnValue()`为函数设置一个默认的模拟返回，而`.mockReturnValueOnce()`只模拟一次函数的返回。这很有帮助，因为您经常想要基于不同的返回值进行测试和断言。

如果没有 TypeScript 的保护，您可以这样做:

```
useAuth0.mockReturnValueOnce({
  isAuthenticated: true,
  loginWithRedirect: jest.fn(),
  logout: jest.fn(),
});
```

同样，该返回值的形状必须与模块函数的实际返回值*的形状相匹配。这是当务之急。它不需要包括一切。例如，`useAuth0()`除了我们嘲笑的属性和函数之外，还返回了许多其他的属性和函数。但是如果我们的代码没有实现其他属性，那么我们不需要模仿它们。*

换句话说，我们在这里假设我们的代码只使用了来自`useAuth0`钩子的`isAuthenticated`、`loginWithRedirect`和`logout`。

对于 TypeScript，情况稍微复杂一些，因为我们会遇到类型错误。TypeScript 看不到我们已经模仿了`useAuth0`——它仍然认为我们在使用实际的实现而不是模仿的实现。因此，您会看到如下错误:

```
Error: Property mockReturnValueOnce does not exist on type useAuth0.
```

为了解决这个问题，我们使用 Jest 提供给我们的类型，让 TypeScript 知道这个模块现在是一个被模仿的函数:

```
(useAuth0 as jest.MockedFunction<any>).mockReturnValueOnce({
  isAuthenticated: true,
  loginWithRedirect: jest.fn(),
  logout: jest.fn(),
});
```

简单说一下:我对在我的代码库中不使用类型`any`非常严格。然而，我个人认为担心使测试类型安全是不值得的，所以我允许在我的测试中相当自由地使用`any`。

现在，当 Jest 到达您的代码中调用`useAuth0`的部分时，它将简单地返回以下内容，而不是实际调用它，这正是您的代码所期望的:

```
{
  isAuthenticated: true,
  loginWithRedirect: jest.fn(),
  logout: jest.fn(),
};
```

## 。toHaveBeenCalled()和。toHaveBeenCalledWith()

例如，为了让我们的代码单元运行，我们不一定需要特定的返回值，但是我们只想确保我们的代码正确地调用一个函数，我们可以使用`.toHaveBeenCalled()`和`.toHaveBeenCalledWith()`断言。前者只是检查它是否被调用，而后者检查它是否被调用并传递了特定的参数。

这里没有进一步模仿函数，这些只是特殊的断言*只能在模仿函数上使用。*它们的用法如下:

```
expect(withAuthenticationRequired).toHaveBeenCalled();
```

# 结论

仅此而已。一旦你完全理解了什么是玩笑，这就相当简单了。在正确的地方模仿正确的属性/模块/函数对于在测试中利用模仿是至关重要的，并且大部分归结为正确的语法。一旦你进入这种状态，嘲笑将成为你新的最好的朋友。

有了这样的理解，我现在建议你研究一下 [Jest 关于嘲讽的文档](https://jestjs.io/docs/en/mock-functions)。现在你明白了背景中发生的事情，希望事情会变得更清楚。