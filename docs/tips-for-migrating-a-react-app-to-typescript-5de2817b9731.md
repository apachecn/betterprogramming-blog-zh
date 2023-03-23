# 将 React 应用程序迁移到 TypeScript 的提示

> 原文：<https://betterprogramming.pub/tips-for-migrating-a-react-app-to-typescript-5de2817b9731>

## 我希望在开始迁移之前就知道的事情

![](img/a0471983b5e713a0620f4ceca5ded492.png)

[巴斯贝利](https://unsplash.com/@7bbbailey?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照。

所以，我现在已经做了快半年的公司项目了。在特性开发完成后，工作速度变慢了，我决定采取下一步——这是我六个月前不敢采取的——将项目“迁移”到 TypeScript。

我很幸运能够与一位思想非常开放的技术领导者和产品经理一起工作，所以在我与他们讨论了这一举措的可能优点后，我能够继续推进这一举措。

这些都是我学到的东西，现在回想起来，我希望我在开始迁移时就知道了。

# 放弃

*   这篇文章假设你是 ts 的初学者，但是它的目的不是教你 TS。它的目的是给下一个想尝试类似事情的人一些建议，让他们的生活更轻松。
*   这篇文章假设您正在从事一个 CRA 项目，并且大部分设置和配置已经为您做好了。然而，大多数建议可以应用于任何 React 项目。
*   这篇文章是基于我将一个相当大的代码库迁移到 TS 的经验。

# 开始之前

## 将 tsconfig 中的 allowJs 设置为 true

仅此而已。这将确保您在启用类型检查器时不会淹没在红色中，并允许您逐渐迁移，让您保留现有的`.js`文件，直到它们到来。

## 在 src 目录中创建一个 global.d.ts 文件

这将派上用场，因为你很可能想要[增加或扩展](https://www.typescriptlang.org/docs/handbook/declaration-merging.html)你正在使用的现有库的类型。

## 在 src 目录中创建一个 custom.d.ts 文件

为了能够[导入不是代码模块](https://webpack.js.org/guides/typescript/#importing-other-assets)的文件，例如图像文件、`.json`文件等，您将需要这个。为了帮助你开始，只需将这个添加到你的`.custom.d.ts`文件中:

瞧，现在你可以在你的代码模块中导入`.svg`和`.json`文件，而 TS 不会向你抛出错误。

## 如果您想启用 noImplicitAny 和 no-explicit-any，请在开始之前执行

`noImplicitAny`是一个[编译器选项](https://www.typescriptlang.org/docs/handbook/compiler-options.html)，它会在带有隐含`any`类型的表达式和声明中引发错误。

`no-explicit-any`是一个`[eslint](https://github.com/typescript-eslint/typescript-eslint/blob/master/packages/eslint-plugin/docs/rules/no-explicit-any.md)` [规则](https://github.com/typescript-eslint/typescript-eslint/blob/master/packages/eslint-plugin/docs/rules/no-explicit-any.md)，不允许你定义任何东西为`any`类型。

如果您启用了这些选项，`unknown`类型将成为您的朋友。

理想情况下，应该从一开始就启用这些选项。无论如何，不要在迁移过程中启用它们。我犯了那个错误，结果突然不得不花很多很多时间去解决我“强加”给自己的很多错误。当您还在迁移过程中时，可能会非常沮丧。

## 设置您的 eslint 配置以用于 TS

我碰巧对`eslint`的默认`no-unused-vars`规则和 TS 有问题。原来 TS 有一个特定的`[no-unused-vars](https://github.com/typescript-eslint/typescript-eslint/blob/master/packages/eslint-plugin/docs/rules/no-unused-vars.md)`规则。启用此选项，禁用默认选项。

## 确定你将如何定义你的类型

您将使用接口还是类型？您是将类型写在代码文件中还是作为一个单独的文件？我建议你在开始之前确定这些细节，因为你不想中途改变主意，不得不修改你已经处理过的所有文件。

我的建议是使用类型，除非接口是绝对必要的，并且将类型定义与组件文件本身分开。

我应用的系统如下:

*   如果该文件是一个 React 组件文件，在同一个目录中创建一个单独的`types.d.ts`文件，并将所有类型定义放在那里——除了`Props`类型定义(我将它保存在组件文件本身中，因为它便于随时获取 props 定义)。
*   如果文件不是 React 组件类型，声明将与代码一起出现。没有单独的`types.d.ts`文件。除非类型太多，导致代码文件非常混乱。然后在自己的文件中取出。

## 阅读文档

说真的。至少读一些重要的部分，比如:

*   [基本类型](https://www.typescriptlang.org/docs/handbook/basic-types.html)
*   [仿制药](https://www.typescriptlang.org/docs/handbook/generics.html)
*   [高级类型](https://www.typescriptlang.org/docs/handbook/advanced-types.html) —尤其是[交集](https://www.typescriptlang.org/docs/handbook/advanced-types.html#intersection-types)和[联合](https://www.typescriptlang.org/docs/handbook/advanced-types.html#union-types)的工作方式，因为它们并不与联合和交集的数学定义精确地 1:1 映射。
*   [类型兼容性](https://www.typescriptlang.org/docs/handbook/type-compatibility.html)
*   [实用工具类型](https://www.typescriptlang.org/docs/handbook/utility-types.html)——那些*非常*方便。给他们一个眼神，至少知道他们的存在。
*   发行说明——我发现有时新版本的发行说明中提到的东西在文档中并没有提到。例如，这个[非常方便的断言函数功能](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-3-7.html#assertion-functions)是在 3.7 版本中添加的，并且(据我所知)在文档中没有提到。

## 当你编码时，准备开始更彻底

TypeScript 最终会让你对编码时所做的一些假设更加透彻。在你看来，对你的代码来说正确的假设对 TS 来说是不够的。TypeScript 将总是要求通过编写更多的代码来验证这些假设，主要是以如下形式:

```
if (notCondition) { 
 throw new Error()
}// Otherwise all is well
```

你会习惯的，最终，它会自然而然的来到你身边。

## 准备好犯错吧

接受这个事实，99.999%的时候，TS 编译器是对的，而你是错的。

# 实际的迁移

## 从小处着手

当您从 JS 过渡到 TS 时，您希望从小处着手。看到你的实用函数目录了吗？到目前为止，它们是您可以开始迁移到 TS 的最简单的东西。

一旦你迁移了一个简单的函数，看看这个函数在哪里被使用，还有什么其他的文件导入了它(编译器可能会在这时抛出一些错误让你知道)。

继续迁移这些文件并重复这个过程，本质上是向上爬(向下？)依存关系树。如果您到达一个对于这个迁移阶段来说太复杂的文件，不要害怕在它的第一行加上一个`[@ts](http://twitter.com/ts)-nocheck`指令，以后再迁移它。

## 不要害怕做类型断言

TS 中的类型断言类似于其他语言中的类型转换。本质上，你是在告诉 TS 编译器你知道得更清楚，在这种情况下，一个特定的变量——即使它*可能*有许多类型——除了一个特定的类型之外不能是任何别的。有时候，事实上你更清楚。但只是有时候。

我发现这在使用提供一些[上下文](https://reactjs.org/docs/hooks-reference.html#usecontext)值的 React 挂钩时很有用。当您初始化`Context`时，上下文值以一个“缺省”值开始，它可能不总是与传递给提供者的实际值兼容。在本文的[中有更多相关信息。](https://dev.to/mitchkarajohn/working-with-react-context-providers-in-typescript-1fk0)

自定义[类型保护](https://www.typescriptlang.org/docs/handbook/advanced-types.html#type-guards-and-differentiating-types)和[断言函数](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-3-7.html#assertion-functions)也非常有助于你帮助 TS 做出正确的类型断言。

## 不要将提供的 FC 类型用于 React 组件

我强烈建议您不要像这样定义 React 组件:

```
const Comp = FC<Props>(props) => { …. }
```

我发现它的用法比什么都麻烦，因为它对你的组件做了一些相当武断的假设(比如总是有`children`道具，并且不能很好地与`PropTypes`一起工作)，并且通常会剥夺你对以你想要的方式定义组件的一些控制。请改用这个:

```
function Comp(props: Props) { …. }
```

TS 足够聪明，可以推断出返回类型本身，并且对于所有意图和目的，这是一个有效的 React 组件，您可以在编译器需要 React 组件的任何情况下使用它。

## 保持你的 PropTypes

虽然 TS 对于确保您在开发期间不犯任何类型相关的错误非常有用，但是`PropTypes`对于让您了解运行期间的类型相关错误非常有用。你会问，如果使用 TS 进行开发，在运行时怎么会有类型相关的错误呢？考虑这个场景:

*   您已经将 TS 中的 API 响应类型定义为带回一个应该是`number`的字段。您还将您的相关`PropTypes`字段定义成这样。一切都好。
*   现在，想象一下，如果您的 API 在那个字段中返回一个`string`而不是一个`number`。如果你去掉了`PropTypes`，你永远不会意识到这个错误，直到应用程序在某个时候崩溃。使用`PropTypes`，如果出现任何此类不匹配，您将在浏览器控制台中得到一个非常有用的警告。

如果你在一个被弹出的 CRA 项目上工作，或者可以访问`babelrc`文件，要知道有一个[插件](https://www.npmjs.com/package/babel-plugin-typescript-to-proptypes)可以自动将你的 ts 类型转换成`PropTypes`，这样你就不必手动更新两者。

## 导出您的所有类型

即使您最终没有将它们全部导入到其他文件中，也要养成导出它们的习惯，因为您永远不知道何时会需要在另一个模块中定义的类型。

## 不要害怕使用泛型

TS [泛型](https://www.typescriptlang.org/docs/handbook/generics.html)非常有用，你也可以拥有泛型 React 组件。例如:

根据我的经验，如果一个 React 组件是对某个东西的抽象，那么可以放心地假设这个组件的类型定义将是一个泛型。

## 阅读您使用的库的类型定义

如有疑问，请务必阅读您使用的库的类型定义。这也将帮助您了解如何定义自己的组件类型，以便与外部库中的组件一起使用。

## 您不必总是定义返回类型

TS(大部分)足够聪明，可以正确地推断出函数返回值的类型。就我个人而言，我也喜欢定义返回值，但那是因为我有某种强迫症。

请注意，有时您必须严格定义返回值才能正确工作。

## 为您的 API 响应进行类型定义

它们将极大地帮助你，因为你很可能会在你的应用程序中使用服务器提供的数据。

## 学习阅读 TS 编译器错误

TS 错误可能是令人生畏的。然而，如何阅读它们有一个“诀窍”。请务必阅读错误消息的第一行和最后几行。如果它仍然对你没有意义，阅读整个错误。通常，只要阅读这些行就能给你理解错误所需要的信息。

# 随机打字提示

## Do (TypeA | TypeB)[]，not TypeA[]| TypeB[]

当您有一个数组，其类型可以是数组`TypeA`或数组`TypeB`时，将其声明为:

```
const arr: (TypeA | TypeB)[]
```

而不是:

```
const arr: TypeA[] | TypeB[]
```

这两个声明之间有一个[微妙的区别](https://stackoverflow.com/a/49511416/4651083)，如果你试图在`arr`上`.map()`，第二个将导致错误。

# 结束语

事实上，你决定迁移你的应用程序，而不是从一开始就用 ts 编写，这可能掩盖了一开始就使用 TS 的好处，因为你的整个代码库已经准备好了。

然而，一旦你继续开发，你*就会*看到优点，现在你所有的新代码都必须遵守 ts 规则。

请记住，您的代码现在在处理错误情况方面更加彻底了。结合一些适当的测试，这将确保您的代码尽可能健壮。

感谢您的阅读！