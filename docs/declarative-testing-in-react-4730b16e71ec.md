# React 中的声明式测试

> 原文：<https://betterprogramming.pub/declarative-testing-in-react-4730b16e71ec>

## 如何为未来编写防重构测试

![](img/427444199d1c33eb57e5c07acf14015e.png)

[杰森·登特](https://unsplash.com/@jdent?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/testing?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

你有没有开始重构你的 React 代码，并且在那些测试运行之前你就知道哪些测试会崩溃？

如果是这样，很有可能你一直在强制性地编写你的测试。相反，通过以声明方式编写测试，可以确保测试仅在行为(而不是特定的实现)发生变化时才会失败，从而改进测试。

简而言之，命令式编程范式归结为指定*你想如何*完成某事，而声明式编程范式归结为指定*你想完成什么*。

在现实世界中，这将是给你的出租车司机一个接一个的方向指示和仅仅给他们你想去的地方的地址之间的区别。

这两种范式都没有内在的更好或更正确，它们都有各自的优点和缺点。

然而，当使用 React 时，在编写命令式代码时，尤其是在编写命令式测试时，会出现许多问题。毕竟，React 是一个声明式框架。 [React 文档](https://reactjs.org/docs/faq-internals.html#what-is-the-virtual-dom)解释道:

> 这种方法[协调虚拟 DOM 和真实 DOM]支持 React 的声明性 API:你告诉 React**你希望 UI 处于什么状态，它确保 DOM 匹配那个状态。**
> 
> 这抽象出了属性操作、事件处理和手动 DOM 更新，否则您将不得不使用它们来构建您的应用程序。

当试图重构代码时，为声明性框架编写命令式测试的一个主要问题变得非常明显。根据定义，重构包括改变代码的编写方式，而不是改变行为的 T21。

但是命令式测试断言代码是如何编写的，而不是行为是什么。通常，这意味着您的测试断言反对将`props`传递给特定的组件，或者确保组件包含给定的`state`。

他们甚至可能调用你的类组件的实例方法。每当你重构时，结合这些对立的范例必然会导致测试失败。

当你知道预测失败时，这看起来并不是什么大不了的事情，但是这种测试真的会让你和你的团队慢下来。今天，你可能确切地知道哪些测试会出错以及如何修复它们，但是随着应用程序的增长以及团队中不同成员对应用程序的工作，你可能会失去这种直觉。

不熟悉您的测试的开发人员永远不会知道如何预测这些失败，所以每次重构都需要花费宝贵的时间来修改或删除失败的测试。在最坏的情况下，命令式测试可能会完全阻碍重构。

如果您的测试不能让您确信您没有破坏任何东西，您将会像没有测试一样盲目地重构和添加新特性。

遵循下面的步骤不会自动让你的测试变得简单。它们仍然为编写脆弱或难以理解的测试留有足够的空间。

但是这些步骤将引导您编写测试，鼓励对 React 的最新特性进行实验，同时提供您的用户体验没有改变的信心。

# 使用反应测试库代替酶

冒着被视为赞助帖子的风险，只要切换到 [React 测试库](https://testing-library.com/docs/react-testing-library/intro)，你就已经遵循了下面的大部分建议。

但你不必相信我的话。 [React 官方推荐 React 检测库过酶](https://reactjs.org/docs/test-utils.html#overview)。

[酶](https://airbnb.io/enzyme/)几乎能做反应测试库能做的一切，但反应测试库做不了酶能做的那么多。

这听起来可能有些违反直觉，但这正是 React 测试库鼓励您编写更加健壮的测试的方式。他们的[文档](https://testing-library.com/docs/dom-testing-library/intro)充分抓住了强制性测试的问题:

> 您希望为 React 组件编写可维护的测试。作为这一目标的一部分，您希望您的测试避免包含组件的实现细节，而是专注于使您的测试给您预期的信心。
> 
> 作为其中的一部分，您希望您的测试库在长期内是可维护的，这样您的组件的重构(对实现而不是功能的更改)就不会中断您的测试并减慢您和您的团队的速度。

另一方面，Enzyme 强大的 API 经常鼓励编写重实现的测试。使用 Enzyme，您可以[“操纵、遍历，并以某种方式模拟给定输出的运行时”](https://airbnb.io/enzyme/)。

但是酶可以让你以非常必要的方式做这些事情。有了像`[children](https://airbnb.io/enzyme/docs/api/ShallowWrapper/children.html)`和`[state](https://airbnb.io/enzyme/docs/api/ShallowWrapper/state.html)`这样的方法，你可以窥视组件，看看它们到底是如何编写的。

像`[invoke](https://airbnb.io/enzyme/docs/api/ShallowWrapper/invoke.html)`和`[setState](https://airbnb.io/enzyme/docs/api/ShallowWrapper/setState.html)`这样的方法允许你以一种终端用户永远不会的方式与组件交互。这种类型的互动恰恰突出了两个图书馆之间的核心哲学差异。

React 测试库努力让你的测试和你的代码交互，就像你的用户那样。它强大的查询 API 让你看到你的测试，就像你的用户看到你的网站在他们的浏览器上运行一样。

例如，您的测试可以找到一个输入字段`[byLabelText](https://testing-library.com/docs/dom-testing-library/api-queries#bylabeltext)`并且[触发一个变更事件](https://github.com/testing-library/dom-testing-library/blob/master/src/events.js#L59)来模拟在那个字段中的输入。这种方法将被测组件视为一个黑盒。

只要这个黑盒将预期的内容输出到 DOM 和用户的屏幕上，开发人员就可以自由地试验新的设计模式或修补 React 的最新特性，而不会遇到测试失败。

当他们遇到失败时，他们可以肯定他们实际上破坏了应用程序的一个功能。

# 如果我选择不使用 React 测试库怎么办？

无论您不从 Enzyme 转换的理由或限制是什么，您仍然可以使用该库编写很棒的测试。

Enzyme 的 API 不会阻止你编写基于用户交互的声明性测试。但是，为了做到这一点，你需要避免使用很多酶的特性。

## 总是“挂载”而不是“浅层”渲染

Enzyme 为您提供了单独渲染正在测试的组件(通过`shallow`渲染)或深度渲染该组件及其所有子/孙组件(通过`mount`渲染)的选项。

[Enzyme 文档](https://airbnb.io/enzyme/docs/api/shallow.html)将`shallow`渲染的动机解释为“有助于约束自己将组件作为一个单元进行测试，并确保您的测试不会间接断言子组件的行为。”

这种动机看起来很合理，但是我已经遇到了这种方法的很多缺点，所以我不再使用它了。

首先，`shallow`渲染已经被证明与 React 的一些最新功能不兼容。例如，[它阻止你准确地测试使用钩子](https://github.com/airbnb/enzyme/issues/2011)的组件。

虽然`shallow`渲染很有可能有一天会支持钩子，但是没有说它是否会支持下一组新功能。转移到`mount`有助于增强您的信心，相信您已经正确地将这些功能应用到您现有的应用中。

更重要的是，`shallow` rendering 通过引入可重用组件使重构变得很麻烦。假设您已经创建了下面的表单:

一些相当简单的 Jasmine 测试可能看起来像:

在创建了足够多的文本字段并注意到一些重复之后，您可能会决定创建一个可重用的`TextField`组件:

引入新组件后，您的表单看起来会像这样:

在这个重构之后，所有三个测试都会失败，因为浅层呈现的酶包装器不会呈现任何`TextField`子节点的内容。

但是你的应用程序的行为并没有改变。对于最终用户来说，重构前后的 DOM 看起来完全一样！

浅层呈现的包装器*确实*正确地显示了`SomeForm`已经呈现了三个`TextField`组件，这个有限的组件蓝图可能会鼓励你`find`组件包装器并断言它们接收了正确的`labelText`属性。

我强烈建议不要这样做，下面会有更详细的介绍。但是，即使我建议了这种替代方法，在重构时，您仍然需要重新访问和编辑失败的测试。相反，通过`mount` ing，您的测试将继续看到输出到 DOM 上的叶节点，并且您可以避免重新访问这些测试。

从测试的角度来看，`shallow`和`mount`分别鼓励编写单元测试和集成测试。由于完全不鼓励`shallow`呈现，很自然地，我也不鼓励编写单元测试。

希望，它开始变得更清楚为什么。

# 编写更少的单元测试，默认为集成测试

在我继续之前，有必要简要回顾一下[测试金字塔](https://martinfowler.com/articles/practical-test-pyramid.html)，它为开发人员应该编写的测试种类提供了指南。

在最基础的层面上，单元测试是小的和孤立的，所以编写和运行它们相对来说是便宜的。服务测试(通常称为*集成测试*)确保两个或更多的单元放在一起时行为正确。

它们比单元测试更脆弱，因为多个单元可以包含测试失败的原因。最后，UI 测试(有时称为端到端测试)确保您的整个系统正常工作。

这类测试通常会在浏览器上运行您的站点，发出实际的 HTTP 请求，甚至可能会碰到一个真实的数据库(尽管是一个专用于测试的数据库)。

毫无疑问，UI 测试是编写和维护成本最高的测试类型。它们也是最无助于准确定位错误来源的。

与不同类型的测试相关联的成本/收益比在历史上激励了金字塔形的测试套件。

![](img/af63b4e7a413636459fc09e85c6faeb8.png)

[https://Martin fowler . com/articles/practical-test-pyramid . html](https://martinfowler.com/articles/practical-test-pyramid.html)

我在 React 中进行单元测试的最大问题是，对于什么是 React 单元还没有达成共识。视图组件是一个单元吗？如果它变得太大，你决定把它一分为二，它们现在被认为是两个单元吗？

[高阶组件](https://reactjs.org/docs/higher-order-components.html)或通过[渲染道具](https://reactjs.org/docs/render-props.html)的组件本身是一个单元吗？如果他们用[钩子](https://reactjs.org/docs/hooks-reference.html)怎么办？钩子是一个单位吗？

我知道这可能会被视为对这些非常有效的问题的全盘否定，但是如果您默认编写集成测试，这些问题并不重要。

如果您决定将一个被测试的组件分成两个(或者更多)组件，那么您的默认集成测试将不会关心或者必须改变。如果您决定用[上下文 API](https://reactjs.org/docs/context.html) 替换渲染道具，以使您的代码不那么冗长，那么您的默认集成测试将不会关心或者必须改变。

当你最终引入 Formik 时，因为你意识到在 React 中维护你自己的表单是多么令人沮丧，你的默认集成测试不会在意或者必须改变。

只要您的集成测试总是指定您想在 DOM 中看到什么，那么您如何到达那里并不重要。这正是 React 应该被编写的方式，并且我坚信大多数 React 测试应该被编写成这样。

当然，单元测试仍然有一个重要的位置。然而，我喜欢将单元测试局限于可重用的组件和任何非反应式的业务逻辑。

通过对可重用组件进行单元测试，您可以向该组件的任何消费者提供信心，让他们相信它会按预期工作。

因为那些单元测试直接与可重用组件的 API 交互，所以它们也记录了那些组件应该如何使用以及它们应该如何表现。

Kent C. Dodds 将这种形式的测试称为测试奖杯。

# 不要断言反作用组件

你可能会觉得奇怪，一个旨在测试 React 的库不应该断言 React 组件。但是通过 React 组件的显示名或构造函数将您的测试分别绑定到该组件在您的目录中的具体名称和位置。

即使使用 IDE 工具可以快速查找和替换文件中的文本，并自动更新导入，这些更改也会在拉请求中产生大量噪声。

作为对 React 组件进行`find`操作和断言的替代方法，您可以用类似于 Enzyme 的查询 API 的方式在 DOM 中查找和断言组件。

如果您发现自己实现这些太难了(或者如果您正在寻找一个快速的解决方案)，您可以从向您断言的元素添加`data-testid`属性并`find`操作这些元素开始。

以这种方式找到您的组件将使以后引入 React 测试库变得容易，它允许您通过 `[data-testid](https://testing-library.com/docs/dom-testing-library/api-queries#bytestid)`进行[查询，作为一种逃生出口。](https://testing-library.com/docs/dom-testing-library/api-queries#bytestid)

在你期望设计和复制经常变化的项目中，`data-testid`属性使你的测试对变化更有弹性。随着项目的稳定，您可能希望迁移到用户可以实际看到的查询。

尽管这种迁移可能会使你的测试更加脆弱，但它们有助于确保你的用户能够以一种合理的方式与你的应用程序进行交互。

# 不要断言反对道具

断言反对 props 会让您的测试套件出现很多假阳性。我经常看到对组件进行单元测试的尝试落入这个陷阱。

当您断言 props 时，您几乎肯定会这样做，因为您的被测组件包含对另一个组件的依赖。为了隔离测试中的组件，您可能决定断言您自己的组件将正确的属性传递给依赖组件。

尽管这种策略确实实现了隔离，但它是以牺牲测试信心为代价的。

为了扩展上面的例子，这样的测试文件可能看起来像下面这样。为了简单起见，他们使用`[at](https://airbnb.io/enzyme/docs/api/ShallowWrapper/at.html)`方法来寻找第一个`TextField`节点。

开发人员可能会决定改变`TextField`，用输入中的占位符文本替换标签标记。

在缺乏强大的集成和功能测试的情况下，进行这种更改的开发人员可能不会遇到任何测试失败，因此可以放心地推动更改。

我认为，在这种情况下，您应该希望您的测试失败，因为您的最终用户的体验已经发生了变化:他们不知道应该在输入字段中键入什么！

# 不要反对或强迫国家

让您的测试了解组件的内部状态几乎肯定会阻止您保持 React 代码最新。

截至 [React 16.8](https://github.com/facebook/react/blob/master/CHANGELOG.md#1680-february-6-2019) ，多亏了[钩子](https://reactjs.org/docs/hooks-intro.html)，几乎可以完全用功能组件来编写 React。功能组件不能像类组件那样使用状态。相反，它们必须使用 [useState](https://reactjs.org/docs/hooks-state.html) 钩子。

当我开始开发一个特性时，我喜欢先把我遇到的任何类组件转换成功能组件。当我这样做时，我依靠我的测试来指出我是否在转换中犯了错误。

但是当我不得不在这个过程中解决误导性的测试失败时，这个策略就变得非常慢了。

就像最终用户不关心您的实现一样，使用您的可重用组件的开发人员不应该关心它是作为类还是作为功能组件来编写的。

# 不要测试实例方法

不应该测试私有方法的信念已经变得相当没有争议。毕竟，这些方法没有公开，它们只是实现细节。

然而，这些相同的考虑并不总是扩展到 React 类组件的实例。Enzyme 为您提供了一个`[#instance](https://airbnb.io/enzyme/docs/api/ReactWrapper/instance.html)`函数，充分利用该方法调用实例方法的测试并不少见。

但是，即使您自信地测试实例方法的行为，这样的测试也不能确保方法实际上被正确地使用。它甚至根本不能确保实例方法正在被使用，这种差距可能会导致死代码的保存。

如果您的代码库最终停止使用实例方法，删除它会导致测试失败。无论是谁遇到这种失败，都可以保持这种方法，以防万一。很快，您的类组件可能会变得难以管理。

通过在实例方法中包含业务逻辑，类组件也可以变得很大。如果您对业务逻辑的单元测试有强烈的感觉，那么您可以而且应该以一种功能性的、反应不可知的方式将逻辑抽象出来。

通过使这些方法具有功能性，您可以使它们可被其他组件重用。通过使它们与 React 无关，您还可以将您的业务逻辑从 UI 框架中抽象出来，从而更容易最终迁移到 React 的新版本甚至新的 UI 库。

如上所述，不测试实例方法还可以将类组件重构为功能组件(功能组件不能有实例方法),而不会破坏任何测试。

# 缺点

尽管它们给你的测试套件带来了很多好处，但是我上面概述的策略并不是没有任何缺点的。

## 越来越依赖集成测试隐藏了测试失败的根源

随着您的测试套件中单元测试的减少，您的测试失败将不可避免地涉及到许多相连的组件，很可能掩盖了失败的来源。这是一个完全合理的担忧。

但是——写得正确——您的测试不会误导您错误可能出现在哪里。当一个单元测试失败时，你会知道错误*必定*出现在被测试的单元中。

当集成测试失败时，您将会知道错误*必定*出现在 DOM 的某个地方，从您的测试呈现的组件开始。

脸书还创建了一套令人惊叹的[开发工具](https://chrome.google.com/webstore/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi)来检查你的 React 组件，这让调试变得异常愉快。

虽然它们永远不会取代测试，但是这些工具确实可以让您快速地看到 props 和上下文值是如何渗透到组件树中的，或者您的组件是如何随着其内部状态的改变而改变的。

您可以使用这个快速反馈来帮助您确定测试失败的原因。

## 失去测试作为文档的好处

作为一个积极的副作用，测试经常为您和您的团队提供与 API 交互的文档。但是如果你的测试开始关注用户行为，他们就开始失去这种优势。

然而，你可以通过一些成本不同的策略获得同样的好处。您可以使用轻量级的 [PropTypes](https://reactjs.org/docs/typechecking-with-proptypes.html) 库来强制传递到组件中的属性的类型。

你也可以使用 [Flow](https://flow.org/en/docs/react/) 或 [TypeScript](https://www.typescriptlang.org/docs/handbook/react-&-webpack.html) 强类型化你的整个应用。这两种方法都允许您通过快速查看类型定义或 PropType 签名来记录如何使用组件。

对于一个更加设计者友好的方法， [Storybook](https://storybook.js.org) 生动地记录了你的 UI 组件，允许开发者动态地学习如何使用你的组件。

引入这些库确实需要时间和精力。然而，我会权衡维护另一个库所增加的时间和处理脆弱测试所减少的时间。

# 结论

您可能已经知道 React 变化极快。我今天写的组件对于我在 2017 年末学到的 React 来说非常难以理解。

如果你想尽可能轻松地充分利用 React 的最新功能，你就必须停止测试 React 的当前功能。React 也不会永远是最受欢迎的前端框架。

很难预测几年后您将使用哪个新框架，但是您的测试对 React 了解得越少，以后就越容易移植到另一个测试框架。