# 用 Rails 7 的刺激显示和隐藏元素

> 原文：<https://betterprogramming.pub/show-and-hide-elements-with-rails-7s-stimulus-e988c35fbfb3>

## 不用考虑 JavaScript，就知道注释元素

![](img/a71cd87794dd2b4c9cc278e42e452d06.png)

照片由[泰龙·费尔南德斯](https://www.pexels.com/@johnpaultyrone?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)从[佩克斯](https://www.pexels.com/photo/person-holding-lighted-sparkler-450301/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)拍摄

当我可以避免编写 JavaScript 时，我会这样做。不是我不行。当‘香草’和‘前端’多余地描述了所有 Javascript 时，我写了香草前端 JS。然而，我讨厌上下文切换。

显示和隐藏元素是迫使我编写(简单的)JavaScript 的常见任务之一。

Rails 7 的[刺激](https://stimulus.hotwired.dev/)让我只需编写一次 show-hide 控制器，就可以通过注释 DOM 永远重用它。这不是一个巨大的飞跃，但却是朝着正确方向迈出的一步。

我们注释我们的标记来显示或隐藏这样的元素。

这个可读标记的数据注释说:

*   父元素`div`连接到名为`visibility`控制器的控制器。在这个`div`中，我们可以利用那个控制器的动作。
*   有三个按钮。每一个都触发控制器中不同的动作，显示、隐藏和切换可见性。刺激知道动作发生在点击上，因为这是按钮的“隐含动作”，但是如果我们想的话，我们可以明确地指定一个触发而不是点击。
*   有两个目标:我们的段落。后者最初是隐藏的，但两者都是上面所有按钮的目标。

我们的控制器定义了这些动作。它非常简洁。

如果你是刺激计划的新手，这个目标清单有解释。

简而言之，我们声明一个可以引用的静态目标名列表。我用`“hideable”`。任何在控制器元素中声明自己为`“hideable”`目标(`data-visibility-target=“hideable”`)的 DOM 元素都将通过`hideableTargets`变量在 JavaScript 控制器中可用。

如果我们期望在控制器中只有一个这样的目标元素，我们也可以使用单数`hideableTarget`变量。如果存在多个`“hideable”` DOM 元素，那么单数`hideableTarget`是该数组的第一个元素。在这种情况下，我选择将我的控制器推广到任意数量的目标。关于目标的[刺激文件](https://stimulus.hotwired.dev/reference/targets)进一步阐述。

我们的小拼图的最后一块是将这个控制器绑定到我们部署的资产包的胶水。

就是这样！在我们的包中定义并注册了控制器之后，我们可以在 DOM 中的任何地方使用`VisibilityController`。

不用考虑 JavaScript，我现在可以注释我想要显示、隐藏或切换其他元素的元素，以及这些操作应该影响哪些元素。

编码快乐！

PS。你愿意和我一起在网上卖宠物药品吗？[考拉健康](https://www.koala.health/)正在[招聘](https://jobs.lever.co/koalahealth)。这里有一整篇文章[关于你为什么应该和我们一起工作。](https://medium.com/@nickfrancisci/why-you-a-software-engineer-should-sell-pet-drugs-on-the-internet-with-me-f14088a27c6)