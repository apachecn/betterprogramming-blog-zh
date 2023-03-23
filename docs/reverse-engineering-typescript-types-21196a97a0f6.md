# 逆向工程打字稿类型

> 原文：<https://betterprogramming.pub/reverse-engineering-typescript-types-21196a97a0f6>

## 看看`typeof`和`keyof`是如何协同工作的

![](img/11e5521ad2b5eea35086a9a22aa26800.png)

作者照片

他们说，打字很容易。它只是说你希望你的变量是什么类型/函数是什么类型/等等。，他们说。但是“他们”忽略的是，作为开发人员，我们花在阅读代码上的时间比写代码的时间要多，并且理解你的变量应该在别人的代码中使用什么类型可能是一个整天的项目，特别是如果你试图使用的代码被设计成灵活的。

虽然互联网上有大量关于编写灵活类型的参考资料，但很少有资料告诉您如何识别被编写为灵活的类型，以及如何准确地给出该类型所需要的内容。

我特别发现`typeof`和`keyof`非常令人困惑。我曾经说过“typeof 意味着它拥有它所拥有的类型，keyof 意味着它拥有它所拥有的键。”这主要是因为我是从试图理解在某个地方给变量什么类型的角度来看待它的，而不是从试图构造一个灵活类型的角度来看待它的。

我的顿悟来自于我自己尝试构建越来越多的灵活类型，并看到`typeof`和`keyof`如何一起工作使这成为可能。

因此，我认为这将有助于浏览我发现非常粗糙的开源类型，并解构它是如何组合在一起的，以及我认为作者在每一步试图完成的内容。请注意，我只是将这种类型作为一个例子，这个过程并不特定于包含在 react-testing-library 中的代码，也不真正与测试直接相关。

# 反应-测试-库呈现结果

我们将要看到的类型是[反应-测试-库呈现结果](https://github.com/testing-library/react-testing-library/blob/main/types/index.d.ts)。尽情欣赏这个宝贝吧:

让我们花点时间讨论一下这是什么，以及我们为什么关心(或者我为什么关心——大多数人可能永远不会关心这种特殊类型)。当您从 react-testing-library 调用 render()时，返回的就是这个类型。换句话说，它是您想象中的 DOM 的一种表示，带有一些您可以在测试中使用的附加功能。

大多数使用 render 的[示例](https://testing-library.com/docs/react-testing-library/api#render-result)显示通过[析构](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment#object_destructuring)将结果直接转储到变量中，这允许 Typescript 推断各个部分的类型，例如[查询](https://testing-library.com/docs/queries/about)。

如果你是一个初出茅庐的人，并且你还没有被[强迫使用`beforeEach`和`afterEach`来保持你的测试不被打扰](https://kentcdodds.com/blog/avoid-nesting-when-youre-testing)，你可能只是想存储一个对类似`findByTestId`或`queryByText`的引用。

为了做到这一点，你必须在`beforeEach`之外声明变量，这样就可以从你的测试中访问它。这意味着你必须知道那个变量应该是什么类型(除非你是一个彻头彻尾的野蛮人，并且使用了`any`，在这种情况下，你为什么还要读这篇文章)。

这就是原因。现在让我们把它拆开，看看我们能从中得到什么。

# 泛型、类型和扩展(哦，我的天)

由于缺乏更好的系统，我通常会首先尝试从左到右、从上到下阅读一种类型，尽管这通常不是理解它的最佳方式。让我们从这个开始:

```
export type RenderResult< Q extends Queries = typeof queries, Container extends Element | DocumentFragment = HTMLElement,>
```

这是创建我们的类型`RenderResult`，使用一个[通用](https://www.typescriptlang.org/docs/handbook/2/generics.html)输入类型`Q`，它扩展了`Queries`并默认为`typeof queries`。我们就此打住，因为我已经像什么了？？？

为了弄清楚查询意味着什么，我们需要进入 [dom-testing-library](https://github.com/testing-library/dom-testing-library/blob/main/types/get-queries-for-element.d.ts) 。我可以写一整篇关于如何找到引用类型的文章，但不是今天。那种类型看起来像这样。

那是什么意思？这意味着 Queries 描述了一个 hash，它的每个属性都是一个函数，以一个`HTMLElement`作为它的第一个参数，然后是一些其他的参数，它将返回给你一个`HTMLElement`(或者别的什么——你自己阅读潜在返回列表的其余部分)。

这里的默认值是描述这种散列的类型。一种思考`typeof`的方式是想象你去一家汽车经销商那里看到这辆可爱的红色汽车。“那是什么型号的车？”

“这是一个 Ferarri。”

您有一个汽车的*实例*，您可以从该实例向后工作，计算出您拥有的汽车的*类型*。或者是 Typescript 编译器。让我们看看是否能够理解 Typescript 在这个实例中“看到”了什么。

在`RenderResult`的情况下，您有一个导入的“变量”`queries`，并且[类型的操作符](https://www.typescriptlang.org/docs/handbook/2/typeof-types.html)检查它以发现它的属性是什么。我把变量放在引号中，因为它比那稍微复杂一些，但是这已经是一篇很长的文章了，所以我不打算继续下去了🐰洞。

让我们看看`queries`，看看它告诉了我们什么关于我们的类型。

我们在这里可以看到，[查询](https://github.com/testing-library/dom-testing-library/blob/main/types/queries.d.ts)文件的导出实际上包含了我们正在寻找的两个键，`queryByText`和`findByTestId`。然后我们能不能只输入这些变量中的一个(或者上面不太具体的`Query`)。很遗憾，不，我们还没完。

如果查看这些类型，您会发现这两个查询的第一个参数都是一个`HTMLElement`。与本例中用于`getByText`的参数相比，[从 react-testing-library API 文档中复制了](https://testing-library.com/docs/react-testing-library/api#asfragment)(第 17 行，作为参数传递给 click)。

那是正则表达式，不是`HTMLElement`。这是怎么回事？长话短说，有许多不同的方法来访问查询，其中一种是从 react-testing-library 导入它们(它在内部支持 dom-testing-library)。

当您以这种方式导入查询时，它们是“[纯](/what-is-a-pure-function-3b4af9352f6f)”函数，因此它们需要对要查询的元素的引用。当你从`RenderResult`中析构它们时，它们实际上是被渲染的根元素的方法。他们不需要引用那个元素。不言自明。

# 打开这个的钥匙

感觉这并没有让我们前进多少，不是吗？一个更聪明的人会在那里切断它，只使用进口版本或只使用[屏幕](https://testing-library.com/docs/queries/about/#screen)。但那样你就不会看到这篇文章了，所以就这样了。让我们回头看看`RenderResult` type，看看对我们有没有帮助。提醒一下，`RenderResult`看起来是这样的:

我们刚刚发现`queries`导出包含了我们正在寻找的键，只是类型与我们想要存储为变量的函数不完全匹配。看看能不能找到一些参照`queries`按键的东西。哦，看哪！在第 18 行。`typeof queries`是通用类型 Q 的默认类型，这里我们要说一些东西`keyof Q`。

在此之前，我们先来讨论一下第 4 行的“=”和第 18 行开头的花括号之间的所有代码。这基本上是一个普通的 Typescript 类型，它定义了一个`RenderResult`拥有的所有*和其他*属性。这些属性都不是我们要寻找的难以捉摸的`findByTestId`或`queryByText`，所以我们将忽略整个部分。

如果您看一下“&”的右边，您会看到在大括号内的整行。如果你稍微眯着眼睛，你可以看到这是一个[交叉类型](https://www.typescriptlang.org/docs/handbook/2/objects.html#intersection-types)。说白了(对不起国际读者)，这是一个*新的*类型，它包含左边类型的所有属性*和右边类型的所有属性*。哪些是…什么…？

让我们稍微思考一下，假设这不是一个类型，而是一个 Javascript 函数。该函数的目的是设置一个属性，该属性的名称将作为参数传入，该属性的值也将被指定为参数:

这是因为，在 JavaScript 中，我们可以用字符串指定对象属性，就像这样:

```
car[‘color’] = ‘red’;
```

Typescript 可以做大致相同的事情，使用[索引访问类型](https://www.typescriptlang.org/docs/handbook/2/indexed-access-types.html)。如果我们再次查看我们的查询导出

`typeof queries[“queryByText”]`应该是一个函数，它接受的参数看起来像`QueryByText`接受的参数，并根据`QueryByText`的返回类型返回某种类型的`HTMLElement`。接下来，如果我们可以访问特定键的类型，我们就可以编写代码来“循环”查询的所有导出，并引用这些键中的每一个。

事实证明，这正是`{[P in keyof Q]: BoundFunction<Q[P]>}`正在发生的事情。

那么这些`BoundFunction`是什么？让我们暂时忽略`BoundFunction`本身(我们会回来的，我保证)，只看`<Q[P]>`。这看起来是不是很像我们的 JS 函数访问一个对象的可变属性？我会替你回答，是的，是的(如果你要回答不，很抱歉)。这就是它正在做的事情。我们的“对象”`Q`，实际上是我们从类型定义的第一行看到的泛型类型，默认为`queries`的导出类型。这听起来很拗口，但是跳过其中的任何区别都会让这个问题变得更加难以理解。

我认为`[P in keyof Q]`有点像为`Q`的每个属性创建一个“变量”`P`的循环。然后在冒号的右边，我们说`BoundFunction`想要用作其泛型类型的类型是`queries[‘whatever is in P’]`的实际类型。我们想出了在上面追踪的方法。

这意味着在我们的交集类型中`findByTestId`的键应该是`BoundFunction<typeof queries[‘findByTestId’]>`。我将把它作为一个练习留给读者去查看 [BoundFunction](https://github.com/testing-library/dom-testing-library/blob/main/types/get-queries-for-element.d.ts#L3-L8) 以确切了解它是如何修改类型的。

# 但是为什么呢？

如果你受不了了，只想回去工作，我理解。我们已经完成了帖子的“实质性”部分，现在我们进入了我对为什么要这样做的猜测。享受你剩下的一天。

对于其他人来说，让我们想想是什么可能促使某人这样对自己(和我们！)

我的第一个想法是，如果您想创建一个自我维护的类型，可以向库中添加新的查询，而无需手动更新类型，这将是一种方法。然而，同样的文件中有对`BoundFunction`的定义，也有一个很长的类型，即[显式地重复查询](https://github.com/testing-library/dom-testing-library/blob/main/types/get-queries-for-element.d.ts#L12-L155)的所有键，所以这看起来不太可能。

最后，看起来这一切都是为了允许库的用户使用他们自己的查询来扩展库。

您可以看到,`BoundFunctions`查看是否获得了`queries`的所有键，如果是，它在最后附加任何用户提供的查询之前，用去掉容器参数的新定义手动覆盖每个键。如果它没有得到类似于`queries`的类型，它只是从用户提供的查询中动态创建绑定查询。我想知道这是否是在添加可扩展性之前就存在的代码的结果，或者是否有一些限制使得使用手动方法比使用动态方法更好，会很有趣。世人可能永远不会知道。

# 走捷径

尽管如此，我最终还是认识到，经常重复自己是在 TypeScript 中做事的最快方式，同时通过跳过创建临时变量的步骤来创建完全难以辨认的代码，然后我必须研究该变量的类型。所以我给你的建议是开门见山，做 TS 想让你做的事情，即使你之前学到的每个原则都要求不要那样做。当然，除非你碰巧想理解我们刚刚经历的那种含义。那就自便吧。

如果你喜欢这个，考虑阅读

[](https://amy-blankenship.medium.com/why-typescript-wont-save-your-project-704c1dfdf8f1) [## 为什么 Typescript 不会保存您的项目

### 如果您必须在 Typescript 和 TDD 之间做出选择，请选择 TDD

amy-blankenship.medium.com](https://amy-blankenship.medium.com/why-typescript-wont-save-your-project-704c1dfdf8f1) 

如果你正在考虑订阅 Medium，并且想直接支持我订阅，你可以在这里做

[](https://amy-blankenship.medium.com/membership) [## 加入我的介绍链接媒体-艾米布兰肯希普

### 阅读艾米·布兰肯希普(以及媒体上成千上万的其他作家)的每一个故事。您的会员费直接支持…

amy-blankenship.medium.com](https://amy-blankenship.medium.com/membership)