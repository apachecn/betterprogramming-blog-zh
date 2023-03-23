# 动态规划面试问题:如何实现股票利润最大化

> 原文：<https://betterprogramming.pub/dynamic-programming-interview-questions-how-to-maximize-stock-profits-8ed4966c2206>

## 给定股票价格列表，找出最大利润

![](img/6c2ed705a3873e712589972ce5975687.png)

马库斯·斯皮斯克在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

假设你有一个数组，其中第*个*元素是一只股票在第 *i* 天的价格。

如果你只被允许完成最多一笔交易(即买一股卖一股)，设计一个算法来找出最大利润。

请注意，你不能在买入股票之前卖出。

示例 1:

输入:[7，1，5，3，6，4]

输出:5

解释:第 2 天买入(价格= 1)，第 5 天卖出(价格= 6)。利润= 6–1 = 5。不是 7–1 = 6，因为卖价需要大于买价。

示例 2:

输入:[7，6，4，3，1]

输出:0

说明:在这种情况下，没有交易完成，即最大利润= 0。

考虑到强力解决方案，我们需要考虑所有可能的事务，同样，这意味着自底向上的方法会更好。

> *老实说，我只是随机选择我认为有趣的问题，它们似乎都巧合地更适合自下而上的方法。如果有人知道一个自上而下的方法更好的问题，请随时留下问题的回答，我会在未来的帖子中尝试这个问题。*

# 最初的想法

有时候，我们应该如何制表的结构一开始并不清楚。深入了解这一点的一个方法是首先问自己，“有可能在线性时间(即 O(n))内解决这个问题吗？”如果是的话，那么你很幸运，因为有可能你只需要使用一个简单的 1D 阵列。否则，通常需要更复杂的结构。

所以在这个问题中，你认为有可能在线性时间内解决这个问题吗？给自己一分钟时间来尝试回答这个问题。现在，对于答案…这是一个*是*。为什么？因为我们真正要做的就是计算我们在任何一天的最大利润。我们如何记录我们的最大利润？嗯，这实际上非常类似于我们之前在最大子阵列 max_profit = [0 for i in range(len(prices))]

至于基本情况，由于我们需要至少 2 天来为所有长度为 1 的输入数组获利(即，在给定的一天买入，然后卖出)，我们的最大利润为 0(我们不想买入任何股票，因为没有第 2 天可以卖出；买股票只会是负盈利)，但是既然 max_profit[0]从我们初始化的时候就已经是 0 了，那么这里就没什么好改变的了。

现在，我们可以使用下面的结构从长度为 2 的输入数组开始，其中我们希望使用之前计算的 max_profit[i-1]的解来计算 max_profit[i]。那么我们如何做到这一点呢？

```
for i in range(1, len(prices)):
        max_profit[i] = ...
```

我希望这一点很清楚，如果 max_profit[i-1]大于 prices[i] — min(prices[:i])，我们可以设置 max_profit[i] = max_profit[i-1]，否则我们要设置 max _ profit[I]= prices[I]—min(prices[:I])。

> Python 切片的工作方式是 prices[:i]是从索引 0 到索引 i-1 的价格子数组

但是调用 min(prices[:i])会浪费不必要的计算时间，实际上我们想要的是达到索引 i-1 的最低价格。我们只需创建一个名为 min_price 的新变量，并在整个 while 循环中跟踪它，而不是调用 min()就可以做到这一点。

最后，我们可以返回 max _ profit[len(prices)-1]作为我们的最终解决方案，这样我们完成的解决方案将如下所示:

```
def solution(prices: List[int]) -> int:
    if len(prices) == 0:
        return 0 # initialization
    max_profit = [0 for i in range(len(prices))]
    min_price = prices[0] for i in range(1, len(prices)):
        if prices[i] < min_price:
            min_price = prices[i] max_profit[i] = max(prices[i] - min_price, max_profit[i-1]) return max_profit[len(prices)-1]
```

希望这些都有意义，在本系列的下一章再见。感谢阅读！

另外，如果你想知道，这绝不是最佳的解决方案。这只是解决这个问题的一种方法。本系列的目的是养成用 DP 方式处理和解决问题的习惯。更优的解决方案将具有 O(1)空间复杂度:

```
def solution(prices: List[int]) -> int:
    max_profit = 0
    min_price = prices[0]

    for i in range(1, len(prices)):
        min_price = min(min_price, prices[i])
        max_profit = max(max_profit, prices[i]-min_price)

    return max_profit
```