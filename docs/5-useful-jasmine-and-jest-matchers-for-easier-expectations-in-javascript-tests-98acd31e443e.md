# 5 个有用的 Jasmine 和 Jest 匹配器，让 JavaScript 测试中的预期更加容易

> 原文：<https://betterprogramming.pub/5-useful-jasmine-and-jest-matchers-for-easier-expectations-in-javascript-tests-98acd31e443e>

## 写下能证实你所关心的事情的期望

![](img/43c9e6a4c8458000724f4560e42f303a.png)

由 [Pankaj Patel](https://unsplash.com/@pankajpatel?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄。

测试是现代软件开发的重要组成部分。在过去的几年里，工具已经有了很大的改进，可以帮助开发人员编写好的测试。毕竟，编写测试不应该感到乏味。相反，测试确保现有的功能按预期工作。

对于大多数常见的编程语言，如 Java、C#、Python 和 JavaScript，都有很好的测试框架。在 JavaScript 世界里， [Jest](https://jestjs.io) 和 [Jasmine](https://jasmine.github.io) 是流行的测试框架。不管任何特定的编程语言或框架，测试通常具有或多或少相同的结构:

*   测试框架由于特殊的功能(例如带有 Jest 的`describe`和`it`)或者特殊的注释(例如带有 JUnit 的`@Test`)而识别测试用例。
*   许多测试文件包含不止一个测试用例。因此，在一个地方进行测试设置通常是有意义的(例如，使用`beforeEach`和 Jest)。
*   运行一些测试后，您可能想要清理一下。例如，删除测试中创建的数据(例如，使用`afterAll`和 Jest)。
*   每个测试用例应该包含至少一个期望/断言(例如`expect(addNumbers(1, 2)).toEqual(3)` with Jest)。期望是关键的部分，因为它们会让我们知道被测试的功能是否如预期的那样工作。最后，它们作为最新的文档。

显然，有些功能比其他功能更容易测试。例如，一个[纯函数](https://en.wikipedia.org/wiki/Pure_function)可以很容易地被测试，因为它的返回值对于相同的参数是相同的，并且它的求值没有副作用。另一方面，一个 UI 组件(例如一个`Calendar`组件)可以有多个子组件、各种输入和输出，并且依赖于外部 API。有时候，很难说测试应该有多彻底。在某些情况下，验证快乐路径可能就足够了。在其他情况下，当它是关键软件部分时，您可能想要检查所有边缘情况。

在很多情况下，我们希望验证一个函数是用一组特定的参数调用的。例如，一个名为`getOrCreate(id)`的函数可能会调用一个名为`get(id)`的函数，我们可以使用期望来验证这个函数。不幸的是，事情并不总是那么简单:

*   函数可以有多个参数。
*   函数可以有深度嵌套对象的参数。
*   函数可以有不同类型的参数(如`parse(param: Date | string | number`)。

为了不使你的测试用例过于复杂，关注你真正关心的部分是一个好主意。幸运的是，许多测试框架提供了帮助编写好的测试期望的方法。在本文中，我将提到一些有用的测试期望工具，它们可以与 Jest 和 Jasmine 一起使用。

*有趣的事实:我是在用 JUnit(一个 Java 测试框架)编写测试之后才发现这些工具的，然后我想知道 Jest 和 Jasmine 是否也会有类似的东西。原来是这样的！*

# Jest 和 Jasmine Matchers 可简化测试预期

根据[文档](https://jasmine.github.io/2.5/introduction):

> `jasmine.any`将构造函数或“类”名作为期望值。如果构造函数匹配实际值的构造函数，它返回`true`。
> 
> 如果实际值不是`null`或`undefined`，则`jasmine.anything`返回`true`
> 
> `jasmine.stringMatching`用于当你不想精确匹配一个更大的对象中的字符串，或者匹配一个间谍期望中的字符串的一部分。
> 
> `jasmine.objectContaining`是针对那些期望只关心实际中的某些键/值对的时候。
> 
> `jasmine.arrayContaining`适用于期望值只关心数组中某些值的情况。

下面是一个展示所有这些功能的基本示例:

在上面的测试案例中，我们使用像`jasmine.objectContaining`这样的助手来编写测试期望，这些期望集中在我们实际想要测试的部分。这使得我们的测试更容易阅读和维护。

# 结论

感谢您阅读这篇短文。正如您所看到的，Jasmine 和 Jest 都提供了有用的工具来编写好的测试期望。你知道其他写合理的测试期望的技巧吗？请在评论中告诉我。