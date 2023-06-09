# 使用测试驱动开发来测试功能和反应组件

> 原文：<https://betterprogramming.pub/using-test-driven-development-for-testing-functions-and-react-components-c438b5f16408>

## 好的代码是经过测试的代码

![](img/5fbbc1390769eb3e0eabc7bfb2025301.png)

照片由 [Esther Jiao](https://unsplash.com/@estherrj?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

这篇文章最初发表在 TK 的博客 上。

这是我研究如何构建可持续和一致的软件的一部分。在本文中，我们将讨论测试驱动开发(TDD)背后的思想，以及如何将这些知识应用于简单的功能、web 可访问性和 React 组件，主要是通过 Jest 和 [React 测试库](https://github.com/testing-library/react-testing-library)。

自动化测试是软件开发的一大部分。作为开发人员，它们给了我们发布代码的信心。我们增加了软件正常运行和工作的信心。

我在 Ruby 社区开始了我的软件生涯，从我学习语言的第一天起就开始编写测试。Ruby(和 Rails)社区在测试自动化领域一直很强大。它帮助我形成了如何写好软件的心态。

所以使用 Ruby 和 Rails，我做了很多后端工作，比如后台工作、数据结构建模、API 构建等等。在这个范围内，用户始终是一个:开发人员用户。如果构建一个 API，用户就是使用该 API 的开发者。如果构建模型，用户将是使用该模型的开发人员。

现在我也在做很多前端工作，在主要使用 React 和 Redux 构建 pwa 一年之后，一些想法浮现在脑海中:

*   在构建 UI 的时候，TDD 是不可能的。我如何知道某个东西是`div`还是`span`？
*   测试可能很复杂。我应该浅尝辄止还是继续努力？测试一切？确保每个`div`都在正确的位置？

所以我开始重新思考这些测试实践，思考如何让它们更有效率。

TDD 是可能的。如果我想知道我应该期待一个`div`还是一个`span`，我可能测试错了。记住:测试应该给我们发货的信心，但是它们不一定需要覆盖实现细节的每一点。稍后我们将深入探讨这个话题！

我想建立这样的测试:

*   确保软件正常工作
*   给予将代码交付生产所需的信心
*   让我们思考软件设计

以及制作软件的测试:

*   易于维护
*   易于重构

# 测试驱动开发

TDD 不应该很复杂。这只是一个三步走的过程:

*   做个测试
*   让它跑起来
*   做正确的事

我们开始编写一个简单的测试来涵盖我们期望软件如何工作。然后，我们进行代码的第一次实现(类、函数、脚本等)。现在，软件正在运行。它像预期的那样工作。是时候弥补了。是时候做得更好了。

目标是一个干净的可以工作的代码*。*我们先解决了“*有效”*的问题，然后使代码变得干净。

这很简单。也应该如此。我没说这很容易。但这很简单明了——只需三个步骤。每次你练习这个先写测试，后写代码，然后重构的过程，你都会感到更加自信。

首先编写测试时，一个好的技巧是考虑用例，并模拟它应该如何使用(作为一个功能、一个组件，或者由一个真实用户使用)。

# 功能

让我们将 TDD 应用到简单的函数中。

前段时间，我正在为一个不动产登记流程实现一个草案特性。该功能的一部分是显示一个模型，如果用户有一个未完成的房地产。我们将实现的函数是一个回答用户是否至少有一个房地产草案的函数。

所以第一步是编写测试。让我们想想这个函数的用例。它总是用布尔值:`true`或`false`来响应。

*   没有未保存的房产草稿:`false`
*   至少有一个未保存的房地产草稿:`true`

让我们编写代表这种行为的测试:

我们写了测试。但是当运行它时，它显示红色:我们有两个失败的测试，因为我们还没有实现函数。

第二步是让它运行起来。在这种情况下，非常简单。我们需要接收这个数组对象，并返回它是否至少有一个房地产草案。

太好了！功能简单。简单的测试。我们可以进入第三步——把它做好！—但在这种情况下，我们的函数真的很简单，而且我们已经做对了。

现在，我们需要该函数来获取房地产草案，并将其传递给`hasRealEstateDraft`。

我们能想到哪个用例？

*   一份空的房地产清单
*   仅保存不动产
*   只有未保存的不动产
*   混合:保存和未保存的房地产

让我们编写测试来表示它:

太好了！我们做了测试，但是没用...还没！现在，实现函数。

我们只需根据房地产状况进行筛选，然后返回。太好了，测试通过了——栏是绿色的。软件运行良好，但我们可以做得更好:第三步！

提取`filter`函数中的匿名函数并让`'UNSAVED'`用一个枚举来表示怎么样？

测试仍然通过，我们有一个更好的解决方案。

这里需要记住一件事:我将数据源与逻辑隔离开来。这是什么意思？我们从本地存储(数据源)获取数据，但是我们只测试逻辑中负责获取草稿的函数，并查看它是否至少有一个草稿。我们确保逻辑功能正常工作，并且一切都是干净的代码。

如果我们把`localStorage`放在函数内部，测试就会变得很困难。因此，我们将责任分开，并使测试易于编写。纯函数更容易维护，编写测试也更简单。

# 反应组分

现在我们来谈谈 React 组件。回到引言，我们讨论了编写测试实现细节的测试。现在，我们将看看如何让它们变得更好、更可持续、更有信心。

几天前，我计划为房地产所有者建立新的入职信息。它基本上是一堆具有相同设计的页面，但是它改变了页面的图标、标题和描述。

我只想构建一个组件——`Content`——并且我想传递呈现正确的图标、标题和描述所需的信息。我将传递`businessContext`和`step`作为道具，它会将正确的内容呈现给 onboarding 页面。

我们不想知道我们是否会呈现一个 div 或段落标签。我们的测试需要确保对于给定的业务上下文和步骤，正确的内容将会存在。所以我带来了这些用例:

*   租赁业务环境的第一步
*   租赁业务环境的最后一步
*   销售业务环境的第一步
*   销售业务环境的最后一步

让我们看看测试:

我们为每个业务上下文准备了一个`describe`块，为每个步骤准备了一个`it`块。我还创建了一个可访问性测试来确保我们正在构建的组件是可访问的。

现在，我们需要让它运行起来。基本上，这个组件的 UI 部分只是图标、标题和描述。类似于:

我们只需要构建正确获取所有数据的逻辑。因为我在这个组件中有了`businessContext`和`step`，所以我想做的事情是这样的:

它得到正确的内容。所以我建立了一个数据结构来这样工作。

它只是一个对象，第一个键作为业务上下文数据，对于每个业务上下文，它都有代表入职每一步的键。我们的组成部分是:

有用！现在让我们做得更好。我想让`get`的内容更有弹性。例如，如果它接收到一个不存在的步骤呢？这些是使用案例:

*   租赁业务环境的第一步
*   租赁业务环境的最后一步
*   销售业务环境的第一步
*   销售业务环境的最后一步
*   租赁业务环境中不存在的一步
*   销售业务环境中不存在的一步

让我们看看测试:

太好了！现在让我们构建我们的`getOnboardingStepContent`函数来处理这个逻辑。

我们试图得到满足。如果我们有它，它就返回它。如果我们没有它，它将返回入职的第一步。

整洁！但是我们可以改进它。使用`||`操作符怎么样？不需要把它赋给一个变量，也不需要使用三元组。

如果它找到了内容，它就会返回。如果没有找到，它将返回给定业务上下文的第一步。

现在，我们的组件只是 UI。

# 最后的想法

我喜欢深入思考我正在编写的测试。我认为所有的开发者也应该这样做。他们确实需要给我们信心来发布更多的代码，并对我们正在努力的市场产生更大的影响。

像所有的代码一样，当我们编写糟糕的测试时，它会影响其他开发人员遵循我们的模式。越大的公司情况越糟。它伸缩性很差。但是我们总是能够停下来，反思现状，并采取行动让它变得更好。

我分享了一些我觉得有趣的阅读和学习资源。如果你想对 TDD 有一个很好的介绍，我真的推荐 Kent Beck 的《测试驱动开发:通过例子》。

我将写更多关于测试、TDD 和 React 的内容——以及我们如何使我们的软件更加一致，让我们在将代码交付给产品时感到安全。

# 资源

## 书籍、博客等。

*   [《测试驱动开发:举例子》](https://www.amazon.com/Test-Driven-Development-Kent-Beck/dp/0321146530)
*   [【可测试的 Javascript】](https://www.amazon.com/Testable-JavaScript-Ensuring-Reliable-Code/dp/1449323391/ref=sr_1_8?dchild=1&keywords=testing+javascript&qid=1585274935&s=books&sr=1-8)
*   [博文源代码](https://github.com/tk-notes/blog/tree/master/tdd-functions-and-react-components)
*   [“使用 jest、jest-axe 和 react-testing-library](https://medium.com/hackernoon/testing-react-with-jest-axe-and-react-testing-library-accessibility-34b952240f53) 测试 React 应用程序”
*   [“现代反应测试，第 3 部分:Jest 和反应测试库](https://blog.sapegin.me/all/react-testing-3-jest-and-react-testing-library/)”
*   [“我们在世界上最难访问的网页上测试工具时发现了什么](https://accessibility.blog.gov.uk/2017/02/24/what-we-found-when-we-tested-tools-on-the-worlds-least-accessible-webpage/)”
*   [“测试实现细节](https://kentcdodds.com/blog/testing-implementation-details)

## 属国

*   [jest-axe](https://github.com/nickcolley/jest-axe) :测试可访问性的 jest 匹配器
*   [Testing-library/react-Testing-library](https://github.com/testing-library/react-testing-library):帮助测试 React 的测试工具
*   testing-library/jest-dom :测试 dom 状态的 jest 匹配器