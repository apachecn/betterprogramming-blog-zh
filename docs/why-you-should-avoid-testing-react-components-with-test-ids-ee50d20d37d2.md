# 为什么应该避免用测试 id 测试 React 组件

> 原文：<https://betterprogramming.pub/why-you-should-avoid-testing-react-components-with-test-ids-ee50d20d37d2>

## 以及如何测试它们

![](img/f6992c6721d90373665df19a8fc231d9.png)

照片由 [Marten Newhall](https://unsplash.com/@laughayette?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/magnifying-glass?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

[测试库](https://testing-library.com/)是一个流行的库，通过不同的实用程序来方便前端组件测试。如果您熟悉它，那么您也熟悉`getByTestId`——这是一种在 DOM 中定位元素以断言其存在的非常方便的方法。当我第一次学习测试 React 组件时，`getByTestId`是我的首选。只需在元素中添加一个`data-testid`属性，并在测试中查询它。它可能看起来像这样:

## 成分

## 试验

但是，对于向用户显示的`title`，这到底告诉了我们什么呢？没什么。我们只是断言标题元素存在。这个值可以是任何值，当然不是用户期望的值。

在`Project`组件的父组件的上下文中，这个测试可能更有意义，在父组件中，我们可能希望通过查询上面的标题来断言一个项目已经被加载。不管怎样，这个值可以是任何值，我们的测试仍然会通过。

当我们测试我们的 React 组件时，[我们应该以用户与它们交互的相同方式测试它们，从而检查它们将看到的值或它们将执行的动作。用户对数据属性或`classNames`不感兴趣。测试库的创建者自己提倡用除了`data-testid`之外的各种方式查询元素，如果可能的话:](https://twitter.com/kentcdodds/status/977018512689455106)

> 根据[指导原则](https://testing-library.com/docs/guiding-principles)的精神，建议仅在**其他查询**对您的用例不起作用后使用。使用 data-testid 属性与软件的使用方式并不相似，如果可能的话应该避免使用。
> [测试库](https://testing-library.com/docs/queries/bytestid/)

假设一个`Project`组件的消费者将项目的描述作为它的`title`道具传入。上面的测试仍然会通过，因为有`data-testid`存在。

但是这个问题与`data-testid`并不相似。通过使用`className`或自定义属性进行测试，您可能会发现自己处于类似的情况。

# "其他查询"

这个测试库文档提到使用`data-testid`应该是最后的手段。但是这里需要注意的是，使用它并不总是一件坏事，它只是取决于你使用它的目的。例如，运行基于`data-testid`的存在显示标题的断言并不理想，但是假设您只想获取元素，然后对其执行某个操作，可能是一个点击事件。这没有任何风险。与上面的例子不同，您的测试并不是基于此而通过或失败的。您将在下面看到一个例子，说明这个属性特别有用并且没有风险。

对于所有其他场景，我的目标是使用文档中提到的那些[其他查询](https://testing-library.com/docs/queries/about/#priority)进行测试——从每个人都可以访问的查询开始，其中包括`getByRole`、`getByLabelText`、`getByText`。

# getByRole

对`button`元素特别有用的是，还有[许多其他的元素](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/ARIA_Techniques#roles)你可以通过它们的角色来查询，heading 就是其中之一。根据测试库，这应该是你的首选，因为你可以用它来查询任何东西。

# getByLabelText

对表单特别有用，但是它们也拾取`aria-label`属性。

# getByText

在查找非交互式元素(如 div、跨度和段落)时非常有用。

`byText`在上面的例子中是一个很好的选择。这将表明用户看到的是想要的标题。

# 你能走多低？

我前面提到过，当实际数据是错误的时候，基于测试通过的`data-testid`的不必要行为与这个属性并不相似。您认为这也可能发生在通过`aria-label`查询时(如果您为它提供一个固定的硬编码值)。

假设我们想要测试一个颜色选择器(想象一下类似于`Pages`或`Microsoft Word`中的内容，其中我们有一个不同颜色的列表，但是我们在选择器中的不同位置还有一个“收藏夹”或“最近使用的”颜色部分。我们想测试当从选择器中选择一种颜色时，它会被添加到最近使用的部分。为了实现这一点，我们自动假设在选取器中相同的颜色会出现两次(一次在所有颜色的总列表中，一次在最近使用的部分)。因此，确保我们查询最近使用的部分本身来检查颜色的存在可能是值得的。

这就是我最喜欢的问题——`within`

`[within](https://testing-library.com/docs/dom-testing-library/api-within/)`允许您检查某些元素*是否存在于*其他元素中。这可能看起来像这样:

通过使用`within`,我们提高了精确度，并模拟了用户的行为。这给错误留下了很小的空间。

我之前也提到过，有些情况下`getByTestId`并不那么危险。上面的测试就是其中之一。我通过它的`data-testid`获取一个元素，然后更深入地搜索它的子元素，但是我的测试并没有根据这个部分本身通过或失败。

# 摘要

以用户与组件交互的方式测试我们的组件是很重要的，这包括检查预期的数据，并避免使用它们的`data-testid`属性。虽然在某些情况下这是不可避免的，但是如果我们碰巧传入了不正确的信息，那么使用这些属性测试数据的存在性可能会导致错误的通过。

测试库的文档建议大多数时候使用`getByRole`,因为如果它们是可访问的，这将挑选出大多数元素，其中`getByText`和`getByLabelText`非常有用。如果我们想非常精确地检查某些项目是否嵌套在某些元素中，我们可以利用`within`助手来提高精确度。