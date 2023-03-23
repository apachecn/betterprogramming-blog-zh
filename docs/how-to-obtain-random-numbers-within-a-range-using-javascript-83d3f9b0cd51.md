# 如何使用 JavaScript 获得一个范围内的随机数

> 原文：<https://betterprogramming.pub/how-to-obtain-random-numbers-within-a-range-using-javascript-83d3f9b0cd51>

## 快速简单的随机化

![](img/5d80eea03a4f689a3de0e59fd138803f.png)

[Edge2Edge Media](https://unsplash.com/@edge2edgemedia?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照。

最近有一个功能请求，要求选择一个随机数来表示一个对象数组的索引。我们需要这个逻辑来从原始数组中创建一个新的、更小的随机选择的项目集合。

在本文中，我将讨论如何使用`Math.random()`在两个数字之间生成一个随机数。

> `**Math.random()**`函数返回一个范围为 0 到小于 1(包括 0，但不包括 1)的浮点伪随机数，在该范围内近似均匀分布，然后您可以将其缩放到所需的范围— [MDN 网络文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math/random)

下面是一个在两个参数(`min`和`max`)之间查找随机数的函数:

```
const randomNumber = (min, max) => { 
    //Use below if final number doesn't need to be whole number
    //return Math.random() * (max - min) + min;
    return Math.floor(Math.random() * (max - min) + min);
}
```

假设`min`和`max`是整数，我们需要做的就是将随机数乘以它们之间的差加上`min`。如果希望返回的数字是一个整数，只需使用`Math.floor()`对生成的数字进行舍入。由于`Math.random`生成一个介于`0`和`1`之间的数，如果不四舍五入，最终结果可能不是整数。

# 最小和最大可能的结果是什么？

在我的例子中，我需要`min`和`max`才有资格进行随机化。截至目前，我们的职能不包括他们。你需要做的就是在`max — min`上加 1。

```
Math.floor(Math.random() * (max - min **+ 1**) + min);
```

# 如果论点是混合的呢？

为了弥补第一个参数大于第二个参数的情况，可以简单地重新分配 final 函数中使用的值:

如果第一个和第二个参数的升序不一致，这可能会提供更多的灵活性。

在此 *将你的免费中级会员升级为付费会员，每月只需 5 美元，你就可以获得数以千计作家的无限量无广告故事。这是一个附属链接，你的会员资格的一部分帮助我为我创造的内容获得奖励。谢谢大家！*

# 参考

[](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math/random) [## Math.random()

### Math.random()函数返回一个浮点伪随机数，范围为 0 到小于 1(包括…

developer.mozilla.org](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math/random) [](https://stackoverflow.com/questions/1527803/generating-random-whole-numbers-in-javascript-in-a-specific-range) [## 在 JavaScript 中生成特定范围内的随机整数？

### Mozilla 开发者网络页面上有一些例子:/** *返回一个介于 min(含)之间的随机数…

stackoverflow.com](https://stackoverflow.com/questions/1527803/generating-random-whole-numbers-in-javascript-in-a-specific-range)