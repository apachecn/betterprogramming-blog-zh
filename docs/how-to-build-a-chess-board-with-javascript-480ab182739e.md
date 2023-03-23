# 如何用 JavaScript 构建棋盘

> 原文：<https://betterprogramming.pub/how-to-build-a-chess-board-with-javascript-480ab182739e>

## 将死！

![](img/c5824320f9e02210345ba4a370db8ff7.png)

[sk](https://unsplash.com/@rollelflex_graphy726?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/chess-board?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍照

我的朋友兼会计师乔恩·萨罗威茨最近在国际象棋上击败了我，所以我很自然地受到启发，开发了一个国际象棋应用程序。

虽然后勤需要我一段时间来实现，但我能够生成普通的游戏板。为了渲染棋盘，我需要考虑普通文件系统中涉及的参数。

*   方块数:64。
*   方形颜色:白色、黑色。
*   颜色组织:交替*。

我在交替旁边加了星号，因为没有每次交替那么简单。

例如，第一行以白色方块开始，以黑色方块结束。下一行以黑色方块开始，以白色方块结束。如果你要用纯交替的方式来渲染方块，你最终会用相同的行填充棋盘。

那么，我们如何正确地渲染棋盘上的方块呢？我们可以用两种方法之一来做这件事。

# 选项 1:按行

一种方法是改变渲染发生的方向。第一行，从左到右渲染。下一行，从右向左渲染，等等。

这个渲染方向可以由 CSS 属性`flex-direction`决定。如果该行的索引是奇数，则不应用`flex-direction`。如果是，应用`flex-direction: row-reverse;`。

我们可以通过使用模运算符来确定哪一行是奇数/偶数行。如果`i % 2 === 0`那么这是一个偶数行。不然就很奇怪了。见下文。

虽然这确实使用了嵌套循环(转换为 On ),并且是一个[指数回归](https://www.varsitytutors.com/hotmath/hotmath_help/topics/exponential-regression) **，**的示例，但是由于您正在遍历一个包含 8 项的数据集，并且对于其中的每一项，您正在执行另一个包含 8 次迭代的循环，因此这可以被视为 8 次迭代乘以 8 次迭代。

这相当于总共 64 次迭代；下一个选项的迭代次数相同。

# 选项 2:按正方形

通过初始化一个变量(我们称之为`change`，我们可以决定何时改变初始颜色，然后从那个颜色开始继续交替颜色分配。

所以，对于第一行，`change = false`的初始颜色是白色。当我们向下移动方块时，我们在黑色和白色之间交替，直到我们到达最后一个方块。

在继续这个模式之前，我们在这里将`change`的值改为`true`。重复这个过程，直到你完成了所有 64 个方块。见下文。

通过初始化一个变量，我们可以决定何时改变开始颜色。起始颜色决定下一种颜色，依此类推。我们可以通过检查`i % 8 === 0 || i === 0`来决定什么时候改变初始颜色。

该条件检查 index 除以 8 的值是否为 0，这意味着它是 8 的倍数，并指示行的结尾。 `i === 0`检查是针对第一个指标的。

在函数结束时，我们将`change`改回`false`，这样就不会意外地重新初始化起始颜色并丢弃图案。

这两个选项包含相同数量的代码行，并且执行相同数量的迭代。

有人可能会说第二个选项比第一个选项声明了更多的变量，但是在这种情况下，它在性能上并没有太大的区别(如果有的话)。两个选项都会呈现以下结果。

![](img/2e87eb779e172d7fbc7a96f1407c1f37.png)

为了好玩，我加了一点动画

你有它！一个快速的文件和排名游戏板生成器。请继续关注我的下一篇博客，我将继续使用 React 构建我自己的象棋游戏。这个帖子的代码可以在这个[代码笔](https://codepen.io/macro6461/pen/xxGzydR)找到。

[*在此处将您的免费媒体会员升级为付费会员*](https://matt-croak.medium.com/membership) *，每月只需 5 美元，您就可以收到各种出版物上数千名作家的无限量无广告故事。这是一个附属链接，你的会员资格的一部分帮助我为我创造的内容获得奖励。谢谢大家！*

# 参考

 [## 指数回归

### 指数回归是寻找最适合一组变量的指数函数方程的过程

www.varsitytutors.com](https://www.varsitytutors.com/hotmath/hotmath_help/topics/exponential-regression)