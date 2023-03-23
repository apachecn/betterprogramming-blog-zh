# JavaScript 高阶函数实用指南

> 原文：<https://betterprogramming.pub/higher-order-functions-in-javascript-4c9b40119ba6>

## 通过一个骰子游戏理解 JavaScript 函数

![](img/06df467668b183a375ff42f5c01a4f76.png)

由[埃里克·麦克莱恩](https://unsplash.com/@introspectivedsgn?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 何必呢？

第一个伟大的“啊哈！”学习编码的时刻是循环结构的引入。它们给程序员提供了对代码块重复任意次数的控制，这种控制非常强大，一旦完全掌握，就很容易变得依赖。这可能是高阶函数看起来复杂的一个原因。事实上，它们并没有给程序员提供任何他们用一个简单的循环已经无法实现的东西，所以学习它们的动力充其量是微弱的。然而，代码的清晰性弥补了它们在附加功能上的不足。

> “的确，花在阅读和写作上的时间比远远超过 10 比 1。作为编写新代码工作的一部分，我们不断地阅读旧代码。…[因此，]让它易于阅读会让它更易于书写。”
> 
> *―罗伯特·c·马丁，干净的代码:敏捷软件工艺手册*

如上所述，在编写代码时，对读者来说清晰是最重要的——即使读者很可能是你未来的自己。

当使用高阶函数而不是更“简单”的循环表亲时，读者真正获得了多少清晰度？这要由个人来决定，但希望在阅读完本指南后，你会觉得他们提高了代码的可读性，从而提高了代码的可维护性。

# 掷骰子

对于我们的实际例子，我们将使用[游艇](https://en.wikipedia.org/wiki/Yacht_(dice_game))游戏，这是一个骰子游戏，后来成为更著名的 Yahtzee 的基础。在 Yacht 游戏中，一名玩家掷出 5 个骰子，并可以根据自己的选择保留任意多或少的骰子，同时最多再掷两次。第三次掷骰子的目的是在保留的骰子和刚刚掷出的骰子之间有一个点数组合。得分的方式有很多，但很大程度上它们类似于人们可能更熟悉的扑克手牌:满堂彩、四张同花顺、顺子等。“游艇”得分最高，即所有 5 个骰子显示相同的数字。

JavaScript 的高阶函数的任务是帮助我们发现玩家有资格采用哪些得分组合(如果有的话)。

这 5 个骰子将被称为一个`hand`，并被编码为一个由`rollDice()`函数生成的数字数组(我们将在后面讨论)。

## [array . prototype . foreach()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/forEach)

我们希望能够做的第一件事是将手中的每个骰子记录到控制台(或者理想情况下在窗口中以吸引人的方式呈现它们)。

使用熟悉的 for 循环，这可以很容易地实现。

对于这样一个简单的例子，作者的意图是显而易见的。然而，即使在如此简单的情况下，我们的第一个高阶函数也可以提高代码的清晰度。

这个函数的行为与上面的 for 循环非常相似。它遍历一个数组，并依次对每个元素应用一些代码。

上面的 for 循环可以用 forEach 调用重写，如下所示。

这里 forEach 函数是针对我们的手数组调用的。然后通过名称`die`访问每个元素(尽管任何合法的变量名都可以)，然后为每个`die`执行`=>`之后的代码。

这段代码的意图更加明确。它读起来几乎像一个英语句子。但也许你还没有被说服。

## [Array.prototype.map()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map)

然后让我们回到我们在上面使用的`rollDice()`函数，但是没有检查和看看它是如何编写的。

也许是这样的？

这很有效，所以你可能会想就此打住。然而，那些看下面的人肯定会看到一个更优雅的解决方案。

优雅。

创建一个我们需要的大小的数组，然后用值' null '填充它，即`const arr = [null, null, null, null, null]`，然后应用我们的映射函数。在这种情况下，它获取每个元素(这里没有命名，因为我们不使用它),然后在`=>`返回一个新数组后将代码映射到它。

旁注:我们不是先用`null`填充数组，然后简单地使用`fill`吗，如下所示:

这可能不是你想要的。

我们会返回相同随机数的 5 个副本。有时这可能是想要的行为。但是，对于任何可变的数据类型(即对象和数组)，都要特别小心，因为返回的数组将填充 5 个对同一对象或数组的引用。变异一个会产生变异另一个的效果。

## [array . prototype . filter()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter)

让我们想象一下，现在我们有一个单独的数组，其中包含布尔值，指示我们的`hand`数组中的等效位置是否要保留用于下一次掷骰子，即`const retain = [false, true, false, false, true]`，我们需要编写一个函数来查看要保留哪些位置。

使用 for 循环查看要保留哪些数字。

这乍一看，并不清楚。当然，可以包含一些注释来说明发生了什么以及为什么会发生，但是，由于高阶函数可读性很强，所以对于简单的函数，我们几乎不需要它们。

像我们希望写的任何评论一样清楚。

我们对我们的`hand`数组应用一个过滤器，它接受每个元素(按照惯例命名为`_`，因为我们不会使用它)及其索引，并返回一个元素数组，这些元素在`retain`中的等价索引是`true`。

一旦你开始对高阶函数的语法越来越熟悉，它们就会变得清晰得多。

## [Array.prototype.reduce()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reduce)

《游艇》中的一个得分类别被命名为“选择”。为此，玩家得分等于所持骰子总数。

对数组中的值求和对于可靠的 for 循环来说是小菜一碟。

这很好，但是当一些更简单的东西要写(更重要的是要读)的时候，没有必要把它分成几行。)可以取代它。

易于阅读=易于维护

这里要减少的第一个参数是累加器的(程序员选择的)名称，第二个参数是每个元素的(程序员选择的)名称。在`=>`之后是一个表达式，当下一个元素被调用时(或者当操作最后一个元素时返回)，该表达式将计算出用于累加器的值。显然，可以执行比简单求和更复杂的运算，但是，即使在这种简单的情况下，这种高阶函数的效用也应该是清楚的。

## [array . prototype . every()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/every)

游艇的最高得分类别被恰当地称为“游艇”,幸运的玩家可以获得高达 50 分的分数。每个骰子必须具有相同的值。

对于 for 循环来说微不足道。

对我来说，这读起来很糟糕。要返回 50 分，需要满足什么条件一目了然。

另一方面，对于高阶函数(双关语)，我们只需解析一个有点生硬的英语句子。

加奖金[三元运算符](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Conditional_Operator)。

它不仅相当短，而且希望你开始同意它也更容易阅读。如果你在两周后回到这篇文章时已经忘记了如何给“游艇”打分，如果你和我一样的话，你会发现高阶函数比 for 循环提供的解释更简洁明了。

## [Array.prototype.some()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/some)

这个函数的行为与`Array.Prototype.every()`非常相似，唯一的区别是，如果有任何元素满足条件，它将返回`true`(如果没有元素满足条件，则返回`false`)。

对于我们的游艇示例，它没有明显的用例，但是希望通过理解`Array.Prototype.every()`函数，用法应该是清楚的。

## [array . prototype . find()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/find)T20[array . prototype . find index()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/findIndex)

*我将这两个功能组合在一起，因为它们的操作相同；第一个提供值，第二个提供索引。*

这些函数将返回满足传递给它的函数的数组的第一个元素或第一个元素的索引。如果没有找到，那么`undefined`(在`find()`的情况下)或`-1`(在`findIndex()`的情况下)。

这些函数有助于根据设定的标准检查一个元素的存在，但不会给出满足它的元素的完整列表(为此，请使用`filter()`)。所以，事实上，我们在当前的程序中也没有这个函数的用例。不过，希望潜在的用例是显而易见的。任何时候你想用一个`break`来缩短一个循环，并在循环中使用这个迭代的值(或索引),都可以用这些函数中的一个来代替。

# 从此一帆风顺

现在我们已经探讨了这些功能，希望您能够在您可能参与的任何项目中找到它们的位置。相对于 for 循环所提供的内容，我认为您可以体会到语法的简单性。

然而，高阶函数并非没有反对者。一个主要的批评，也是一个有效的批评，是它们在技术上不如 for 循环的等效物有效。上面给出的所有例子在语法更加冗长和不清楚的情况下会运行得更快。因此，在性能和可读性之间有一个折衷。我向您推荐的策略如下:尽可能使用高阶函数，只有当性能开始下降并且需要进行优化时，您才应该重构使用 for 循环。在项目的早期阶段，当您可能会多次浏览相同的区域时，这提供了可读性的所有好处。此外，如果编写了一套全面的测试，当任何函数被转换为 for-loop 语法时，测试应该会继续通过。

最后，我们浏览了高阶函数，以及它们如何应用于现实生活中的例子。对于任何跟随并对最终结果感兴趣的人来说，Yacht 的一个可玩版本使用了上面描述的功能(稍微修改的版本),在这里可以得到。