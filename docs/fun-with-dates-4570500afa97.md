# 约会的乐趣

> 原文：<https://betterprogramming.pub/fun-with-dates-4570500afa97>

## 让 JavaScript 日期更像 Ruby

![](img/827f3e0549726101bd2e99bc3b78987b.png)

柯蒂斯·麦克牛顿在 [Unsplash](https://unsplash.com/s/photos/calendar?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

本帖首发于 [***TK 的博客***](https://leandrotk.github.io/tk/2020/04/fun-with-dates/index.html) 。

作为一名前 Ruby 专家，我一直喜欢使用 Ruby 日期(注意:不是时区部分)。我喜欢 Ruby 和 Rails 提供方法来处理 Date 对象的人性化方式。

在 Ruby 中，我们可以通过执行以下操作来获取当前日期:

这太酷了。我可以通过调用`today`方法向日期对象发送一条简单的消息“嘿，给我提供`today date`”。

或者我可以简单地得到`year`、`month`和`day`。

使用 Rails，还可以调用`yesterday`方法:

Rails 还提供了其他有趣的 API:`beginning_of_month`、`minutes.ago`和`days.ago`。

在长时间使用 Ruby 和 Rails 之后，我开始越来越多地使用 JavaScript。但是 JavaScript Date 对象对我来说真的很奇怪。我想使用所有的 Ruby/Rails 日期 API，但是使用 JavaScript 和 TypeScript。

我不想在 JavaScript 日期对象中乱打补丁或构建新方法。我可以只提供一些简单的功能，并在内部处理数据。

# 约会日期

首先:我想更好地理解日期对象。我们如何创造它？

我们通过简单地实例化 Date 对象来创建它。我们得到了`now`(当前日期)的表示。

我需要尝试的其他 API 是`getDate`、`getMonth`和`getFullYear`。这些都是处理日期的方法。

我们可以在这里试验一大堆其他方法，但我认为我们可以进入下一部分。

# 约会的乐趣

在这一部分，我们将构建函数！我想尝试创建这个 API:

*   `day`
*   `month`
*   `year`
*   `today`
*   `yesterday`
*   `beginningOfDay`
*   `beginningOfMonth`
*   `beginningOfYear`
*   `get(1).dayAgo`
*   `get(2).daysAgo`
*   `get(1).monthAgo`
*   `get(2).monthsAgo`
*   `get(1).yearAgo`
*   `get(2).yearsAgo`

# 日、月和年

在这种情况下，我们提供一个日期，它将返回我们提供的日期的日期。

我们可以这样使用它:

# “今天”和“昨天”

使用`today`函数，我们只需返回`new Date()`就可以了。但是这会返回包含“时间”的`now`的表示。

但是如果能回到那天的开始就太好了。我们可以简单地将日期、月份和年份传递给`Date`，它将为我们生成以下内容:

太好了。`yesterday`函数的工作方式非常相似。减去这一天，我们就可以开始了:

但是当我们减去这一天，如果这一天是这个月的第一天，会发生什么呢？

如果是一年的第一天会发生什么？

是的，JavaScript 也可以很聪明！

有了这两个新函数，我们还可以重构逻辑，将分离的日期放入一个单独的函数中。

让我们改进这一点。这个返回的类型可能是一个类型脚本`type`:

现在不那么冗长了:

在这种情况下，我们总是返回当前日期的`day`、`month`和`year`属性。但是如果我们想传递不同的日期呢？拯救行动的新论据:

现在我们有了一个可以接收新日期的函数。但是如果没有，它就使用`now`的表示作为默认值。

我们的函数`today`和`yesterday`现在看起来怎么样？

这两个函数都使用`getSeparatedDate`函数来获取日期属性并返回适当的日期。

# 一切的开始

为了构建`beginningOfDay`，它看起来与`today`函数完全一样，因为我们想要当前日期，但在一天的开始。

这里没什么特别的。

如果你没注意到的话，只是一个小评论。起初，我构建这个函数是为了获取当天的开始时间。但我想让它足够灵活，以便在其他日子也能开始一天的工作。

所以是“争论”，对吧？现在该函数接收一个日期，但是它足够灵活，可以不接收它。我只是用当前日期的默认值来处理它。

对于`beginningOfMonth`，它看起来几乎一样，但是我们没有使用`day`，而是将它设置为`1`。

你猜对了，`beginningOfYear`也差不多。但是你也改变了`month`属性。

# 回到过去

现在是`get(1).dayAgo` API。我们可以构建一个接收`number`并返回一个对象的`get`函数，如下所示:

对于这个对象的每个属性，它将是我们期望的返回值。

一个`DateAgo`型怎么样？

现在使用新的类型:

正如我们所知，我们主要通过处理日期对象来构建每个属性(`dayAgo`、`monthAgo`和`yearAgo`)。

但是现在我们还需要实现复数形式的对象:`daysAgo`、`monthsAgo`和`yearsAgo`，但是只针对大于 1 的数字。

对于这些新属性，我们不需要再次创建一个全新的日期。我们可以从单个属性中使用相同的值。

我们还需要处理收到的`number`。

*   如果大于 1，则返回具有多个属性的对象。
*   否则，返回具有单一属性的对象。

*   在这种情况下，我还创建了`DatesAgo`类型并使用了 TypeScript `Union Type`特性。
*   我们重用奇异值。
*   我们做一个简单的三元组来处理收到的数字。

但是如果我们传递一个`0`或者负值呢？我们可以抛出一个错误:

约会也可以很有趣。学习基本的概念，然后玩一玩，你会喜欢的！我希望这篇文章对你有价值。

# 资源

*   [日期— JavaScript | MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date)
*   [Ruby on Rails 日期 API](https://api.rubyonrails.org/classes/Date.html)
*   [红宝石日期 API](https://ruby-doc.org/stdlib-2.7.1/libdoc/date/rdoc/Date.html)
*   [约会图书馆](https://github.com/leandrotk/dating)
*   [打字稿学习 001:对象析构](https://leandrotk.github.io/tk/2020/04/typescript-learnings/001-object-destructuring.html)
*   [理解 JavaScript 中的日期和时间](https://www.digitalocean.com/community/tutorials/understanding-date-and-time-in-javascript)