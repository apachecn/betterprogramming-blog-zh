# 如何进行功能开发和解决问题

> 原文：<https://betterprogramming.pub/how-to-approach-feature-development-and-problem-solving-8559604eb8d9>

## 开发任何功能的 5 个步骤

![](img/63e5e1db00648d133a04e406f83542c8.png)

沃洛季米尔·赫里先科在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

开发一个新特性有很多方法。这个过程始于产品所有者带着一个想法来到开发团队。他们通常有一些用户研究和一些数据来支持这个想法，经过简短的讨论，每个人都同意构建这个功能。

然后，产品经理询问开发新功能所需的估计时间。工作人员/领导/首席工程师提供这些评估，项目开始:它被分成更小的任务，个人贡献者从实现开始。

设计出来了，代码写出来了，有时候，代码被扔掉了。而且，在很多场合，我们需要重新写一遍。这是处理项目的最糟糕的方法之一:从想法到估计，再到实现的自下而上的方法。

一个更好的方法包括正确地收集需求，并确保每个人都参与进来。今天，我们想从一个移动工程师的角度来说明一个以最大化成功概率为目标开发任何功能的 5 步过程。

# 1.收集需求

第一步也是最重要的一步是收集**所有的**需求。我重复一遍:收集所有的需求。他们所有人。

我们经常犯的错误是没有尽快让一些球队参与进来，这是很危险的。一小部分人只能想到可能性的子集:通常，我们可以推理出快乐的路径和一些不快乐的路径。

但我们生活在一个非常复杂的世界，不同背景的人可以帮助填补我们自己可以填补的空白。因此，我们应该与所有对任务感兴趣的人(通常称为**利益相关者)讨论任务。我们需要对期望的结果有尽可能清晰的理解，我们需要把它写下来:这有助于发现所有的需求和含义。**

以下是我们在开始开发该功能之前应该咨询的人员列表。

1.  **产品团队:**他们知道特性和目标。它们帮助我们理解快乐之路和特性的商业价值。
2.  **技术团队:**他们是系统专家，可以指出缺陷和可能的复杂情况。他们的经验对于创建评估和定义未知也很重要。
3.  **设计团队:**如果功能涉及到用户界面或 UX 的变化，确保与团队讨论设计，以揭示任何可能的问题。
4.  **QA 团队:**质保团队是最接近最终用户的团队。他们受过使用产品的训练，擅长尝试各种不愉快的途径。我们可以利用他们的经验来创建一组测试用例来驱动开发。
5.  **后端团队**:移动工程师和后端工程师可以在不同的团队工作，经常合作。如果一个特性需要一个新的端点，让后端团队参与进来是很重要的。他们需要计划和安排一些时间与您的团队合作，他们可以帮助设计一个可以很好地扩展的数据流。
6.  **法律/合规和安全团队:**当某项功能需要收集一些新数据时，我们必须让法律、合规和安全团队参与进来，以确保该功能可以开发，服务条款是否需要更新以及如何处理。

经常与这些团队交流，开会并公开讨论这个特性。记下讨论的内容，并与团队分享。在进入下一步之前，获得他们的认可。

# 2.样机研究

在这一步中，一两个人花一些时间探索技术，并对团队想要开发的内容进行概念验证(POC)。

这里的目标是快速前进:没有测试，没有干净的代码，没有错误处理。我们只需要检查它是否可行，以及可能存在的问题。原型应该是有时间限制的:时间应该投入最少的时间来回答最有说服力的问题。

一旦团队有了可以工作的东西，与涉众一起检查，以确认团队正在朝着正确的方向前进。

然后，我们必须避免一个常见的陷阱:将原型推向生产。原型的开发没有考虑到可扩展性和工程最佳实践。将其推广到生产中可能会带来短期收益，但从长期来看，这会产生技术债务和更高的成本。

# 3.设计

一旦需求明确，原型被接受，我们就可以把它扔掉了。现在是时候设计一个合适的解决方案了，用 UML 图表和合适的文档。

我更喜欢的绘制 UML 图的工具是[diagrams.net](https://www.diagrams.net/)(以前称为 draw.io)。它允许我们快速绘制类图和序列图，以理解如何组成系统以及它应该如何表现。

设计解决方案时，不需要考虑最微小的细节。一个高层次的设计，有了组件它们的连接就足够了。当所有团队在设计上协作，并在实现上达成一致时，可以获得最佳结果。

采用迭代方法很重要。涉众应该在设计解决方案的特定部分时参与进来:他们可以仔细检查他们的需求是否得到了尊重。例如，如果安全团队不相信敏感信息是如何存储的，该团队可以重复解决方案的这一部分，直到每个人都满意为止。

这一步的结果是一个包含解决方案架构的文档，它有助于定义评估。它还记录了整个过程中所做的决策。

# 4.测试驱动的实现

在这个阶段，设计被分解成更小的任务，分配给不同的团队成员。行业标准是用[测试驱动开发](https://en.wikipedia.org/wiki/Test-driven_development) (TDD)方法实现一个特性。

TDD 有几个好处可以加速整个开发周期:

1.  我们致力于测试用例(由 QA 团队定义)，以确保该特性符合所有需求。
2.  我们没有添加超过需要的东西(T2 YAGNI T3 原则)。
3.  我们编写本质上可测试的代码。否则，我们无法首先编写测试。
4.  我们不会浪费时间手工测试我们的代码。在移动开发中，手动测试意味着:构建应用程序、启动模拟器、加载应用程序、启动应用程序、导航到正确的屏幕并测试它。这个过程可能需要几分钟。单元测试需要几毫秒。
5.  我们获得了一个测试套件来防止代码回归。
6.  我们获取测试，作为我们代码的文档。

通常，使用 TDD 可以让我们开发一个软件，在我们第一次运行它的时候就可以工作。在应用 TDD 之前，这种情况几乎从未发生在我身上。

另一种典型的方法是在编写代码之后测试它。这通常发生在原型或设计阶段出错，我们不知道从哪里开始定义我们的测试的时候。当这种情况发生时，后退一步，重新思考原型或设计。

# 5.验收和放行

至此，该特性已经开发并构建完成。我们需要将它提交给质量保证以及产品和设计团队，看看它是否通过了人工验收标准。

质量保证团队想要测试最关键的路径和新特性的行为。如果他们从一开始就参与了规划和开发，那么在这一步就不会有太大的意外:我们会考虑他们的关注和建议来测试实现。绝大多数验收标准也必须通过某种自动测试来检查。

类似地，设计和产品团队想要再次检查用户体验和需求是否符合他们的想法。如果他们在开发的所有阶段都保持在循环中，就不会有很多惊喜了。

到那时，这个特性就可以发布给最终用户了。

# 结论

在今天的文章中，我们举例说明了开发一个新特性的 5 个步骤。我们建议不要立即投入编码，而是后退一步，清楚地理解我们需要做什么。

突出显示的五个步骤是:

1.  **需求收集**:听取所有利益相关者(产品负责人、前端和后端技术团队、质量保证、设计、法律、合规和安全)陈述并记下所有需求。
2.  **原型制作**:在特定的时间范围内开发一个一次性的概念验证(POC)来检查功能的可行性。
3.  **设计:**与所有利益相关者一起为特性创建一个可扩展和可维护的解决方案。这产生了文档和估计。
4.  **TDD 实现:**从测试开始测试我们的实现，并在测试之后添加代码。我们获得可测试的和经过测试的代码，只有相关的组件和关于特性行为的活文档。
5.  **接受和发布:**与涉众一起检查特性是否已经成功实现。如果所有的人都适当地参与进来，这一步应该是平稳而快速的。

当我们单独开发时，这个过程也可以用于个人项目。我们应该经常检查对特性的期望，验证假设，设计合适的解决方案，测试并发布它。

这个过程可以节省很多时间，也可以最大限度地提高人们的幸福感:当他们参与到项目中时，他们会感到被倾听和重要。他们很容易合作，他们支持项目的成功。这也是我们的愿望。