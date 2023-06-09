# 我在科特林的《2021 代码降临》中的尝试

> 原文：<https://betterprogramming.pub/my-attempt-at-advent-of-code-2021-in-kotlin-e26b9a0d394b>

## 科特林能在水下帮你吗，或者圣诞老人和他的潜水艇

![](img/c0348ec8c7288e58ed650d9d85517c8f.png)

托马斯·哈斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

有一个名为[代码降临](https://adventofcode.com/2021)的编码挑战正在进行中(实际上是中途)，由 JetBrains 发起，这家公司创造了 Kotlin 编程语言。

我在 JetBrains 的 Kotlin 博客上发现了它，并正在解决几个谜题——不是所有的谜题，因为它们有时很耗时，我这样做不是为了竞争，只是为了更好地记住 Kotlin，因为现在它不是我的主要工作语言，坦白地说，我很怀念它(而且有机会获得一些奖品——谁会传递一些免费的奖品，我说得对吗？).

将会有 25 个挑战，从 12 月 1 日到圣诞节每天一个，所以它就像一个降临节日历，但是用代码。所有的谜题(显然)都是以圣诞节为主题的，并且与圣诞精灵有关，他们在水下的潜水艇里游泳，大概是为了找回被丢进海里的圣诞老人的雪橇钥匙。

他们在途中遇到不同的障碍，并解决难题以营救潜艇并继续前进。

> 代码[的出现](https://adventofcode.com/2021/)问题可以用你喜欢的任何语言来解决——据我所知，Kotlin 不是必需的。但是如果你想有机会赢得一些 Kotlin 奖品，你需要满足几个条件，使用 Kotlin 语言是其中之一。

![](img/c3ec99dc3060345e58dcbceb5aa69acd.png)

塞巴斯蒂安·佩纳·兰巴里在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

今天，我想发布一个第 6 天的解决方案的例子，因为它已经通过了，而且是允许的，但也因为虽然足够简单，但它没有以最明显的方式解决。我们来看看为什么。

[此处对问题进行了全面描述](https://adventofcode.com/2021/day/6)。我将转贴部分描述，以便快速参考。

重申一下，有一群灯笼鱼，任务是计算它们繁殖的速度。每条鱼都用一个数字来代表，这个数字显示了它能繁殖的天数。每条鱼在繁殖日只生产一条鱼。有一个规则列表定义了每条鱼的过程:

*   一天后，它的内部计时器会变成`2`。
*   再过一天，它的内部计时器会变成`1`。
*   再过一天，它的内部计时器就会变成`0`。
*   又过了一天，它的内部计时器将重置为`6`，并且它将创建一个内部计时器为`8`的新 lanternfish。
*   又过了一天，第一个 lanternfish 将具有内部定时器`5`，第二个 lanternfish 将具有内部定时器`7`。

创建新鱼的 lanternfish 将其计时器重置为`6`，而不是`7`(因为`0`作为有效计时器值包含在内)。新的 lanternfish 启动了一个内部计时器`8`，直到第二天才开始倒计时。

输入是一个数字列表，代表初始鱼群及其各自的内部计时器。第一项任务是计算 80 天后会有多少鱼。我想到的显而易见的解决方案只是一个模拟:

因此有一个名为`Fish`的类，它只有一个名为`plusDay()`的方法，并在类`FishFarm`中用来创建 lanternfish 的列表。我们的结果就是添加指定天数后的列表大小，在我们的例子中是`80`。

确实有效，测试用例通过了。我在我的字谜输入上运行它，它也通过了，耶！

我的难题输入(比小测试大)

但是等等——还有第二部分。而第二个任务恰好相同，只是现在的天数是 256。你认为这个解决方案在全尺寸输入下还能工作吗？

剧透提醒:不会。鱼的数量增长如此之快，以至于超过了`Int`的范围，于是这个计划失败了。怎么办？我们是否应该找到一个可以容纳超过 **2，147，483，647** 个值**的列表？**

# 实际上，我们真的那么需要一份名单吗？

事实上我们没有。

为什么？因为我们对鱼的定义非常有限——只能有从 0 到 8 范围内的值。有没有让你想到什么合适的数据结构？

# 一个数组。

的确如此。我们只需要一个索引从 0 到 8 的数组来保存各自的计数器。数组中的每一个值都将保存当前在内部计时器上有这个数字的鱼的数量。的确，这个解决方案需要更多的改动，以确保我们得到正确的增量和减量。但是最后，它甚至比前一段代码还要短一点。这是调用代码的样子。

结果是一个`Long`值，该数组还保存类型为`Long`的计数器。然而，它们只有几个，一般来说，这个程序的内存占用应该比上一个程序少。而且见效很快。

有时候最好的解决方案有点不太明显，不是吗？

或者对你来说很明显，只是对我来说不明显。它发生了。尝试一些谜题，也许你会成为这方面的高手！

# 资源

*   [代码 2021 出现，第 6 天](https://adventofcode.com/2021/day/6)。
*   [kot Lin 博客上关于 2021 号代码及其规则的文章](https://blog.jetbrains.com/kotlin/2021/11/advent-of-code-2021-in-kotlin/)。