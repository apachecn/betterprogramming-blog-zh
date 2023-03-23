# 谷歌面试问题:检查数组中的和

> 原文：<https://betterprogramming.pub/google-interview-question-check-sum-in-array-ba03c0c1a3e6>

## 如果你在谷歌面试，要做好准备

![](img/d5b7b6a92951cf0fa772bece262356a5.png)

[米切尔·罗](https://unsplash.com/@mitchel3uo?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

可怕的白板编码面试。我们都讨厌它，但我们都必须面对它。

这已经成为公司测试候选人编程能力的标准——尤其是对于初级到中级职位。所以，让我们提高技能，今天就尝试一个。

在几个不同的国家呆过一段时间后，我在伦敦、新德里、旧金山、芝加哥、新加坡、柏林和慕尼黑建立了一个强大的软件相关职位的朋友网络。

在这种情况下，我分享一些在我的网络中分享的东西——一个在谷歌采访中被问到的问题。

# 问题

给定一个名为`sum`的整数和一个名为`numbers`的整数数组，检查`numbers`数组中是否有任意两个元素的组合可以相加恰好等于`sum`。

这里有一个例子:

*   输入:`sum = 17`，`numbers = [1, 9, 2, 19, 14, 8]`
*   答:`true`自`9 + 8 = 17`

# 解决办法

我们可以很容易做到这一点。我们采用一个嵌套循环并开始遍历数组，尝试每个组合，直到我们或者到达等于`sum`的组合然后返回`true`，或者到达循环的结尾并返回`false`。然而，这不是一个好的解决方案，因为它的时间复杂度为 O(n)。

相反，让我们尝试通过创建一个简单的对象来创建一个可以在 JavaScript 中轻松完成的集合。当我们遍历数组时，我们可以将当前元素添加到我们的集合中。如果我们看到`sum — currentElement`存在于集合中，我们返回 true。如果我们遍历整个迭代并到达数组的末尾，我们返回 false。

这样我们就提高了时间复杂度。I 变成了`O(n)`，因为我们只遍历数组一次。我们还使用相同数量级的空间复杂度 O(n ),因为对象的大小直接取决于输入数组的大小。解决方案如下:

```
function checkSumInArray(sum, numbers) {
    const parsedNumbers = {}; for (let i = 0; i < numbers.length; i ++) {
        const diff = sum - numbers[i];
        if (parsedNumbers[diff]) {
            return true;
        } else {
            parsedNumbers[numbers[i]] = true;
        }
    }; return false;
}
```

您可以通过将该函数粘贴到浏览器的控制台中，然后给它一个输入来进行尝试，如下所示:

```
console.log(checkSumInArray(17, [1, 9, 2, 19, 14, 8]));
```

这是我们能做的最好的吗？

我至少还能想到一个解决方案——一个将时间复杂度降低到`O(n log(n))`的解决方案。我们可以使用二分搜索法在数组中搜索`sum — numbers[i]`——前提是我们先对`numbers`数组进行排序。这也将降低空间复杂度，因为我们将总是只需要恒定的空间，因此`O(1)`。

我已经给了你一个很大的提示，所以我会让你在实现这最后一个解决方案的过程中得到乐趣。我还打算发布更多这样的内容，所以请继续关注，磨练您的白板编码面试技巧！