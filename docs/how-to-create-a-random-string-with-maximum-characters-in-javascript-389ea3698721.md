# 如何在 JavaScript 中创建最大字符数的随机字符串

> 原文：<https://betterprogramming.pub/how-to-create-a-random-string-with-maximum-characters-in-javascript-389ea3698721>

## 用 JavaScript 创建你自己的随机数据生成器！

![](img/56591f2a33034f6a66f17f2ed843a5f4.png)

[Aditya Saxena](https://unsplash.com/@adityaries?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

您可以使用一个库在 JavaScript 中生成随机数据，但我认为自己动手会更酷。如果你想增加你的 JavaScript 技能，这些函数是最好的练习。

所以在这篇文章中，我想解释一下如何用最大数量的字符生成随机字符串。这篇文章是我上一篇文章[如何用 JavaScript](https://hasnode.byrayray.dev/how-to-create-a-random-string-with-javascript) 创建随机字符串的后续。

如果你没看过我之前的帖子，我强烈推荐你去看看。在这种情况下，我们将继续该职位的最后一个功能。

```
function randomString() {
    return [...Array(5)].map((value) => (Math.random() * 1000000).toString(36).replace('.', '')).join('');
}
```

这个函数现在生成一个长度可变的随机字符串。

返回值总是不同的。

```
randomString();
'63lklfirmmc2baknxlzou171jyq7q7wnd8ag53r6kv95buvp1qme8ou'
randomString();
'j9qfsxifdugz5bgfmqfwg3c2jefsidxikhl2c4qjwti6i0zm5y5x5'
```

但是现在，我们希望确保它有一定的长度，这样我们就可以更好地控制我们生成的数据。

我们只需要确定它开始的数组的长度。

# 最小长度

为了确保我们至少拥有函数参数中给定的字符数，我们将数组做得稍长一些。

```
[...Array(length + 10)]
```

# 固定长度

但是为了避免更长的时间，我们取一个子串，所以我们使用`substring`返回精确的字符数。

```
[...Array(length + 10)].map((value) => (Math.random() * 1000000).toString(36).replace('.', '')).join('').substring(0, length);
```

如果我们把所有这些变成一个函数，这就是我们的结果。

```
function randomString(length = 50) {
    return [...Array(length + 10)].map((value) => (Math.random() * 1000000).toString(36).replace('.', '')).join('').substring(0, length);
};
```

我建议尝试一下这个函数，并把它用在一些实际的事情上。

# 谢谢！

![](img/2bd571bb1ce9f466ae279fa98111b086.png)

读完这个故事后，我希望你学到了一些新的东西，或者受到启发去创造一些新的东西！🤗

如果我给你留下了问题或一些要说的话作为回应，向下滚动并给我键入一条消息。如果你想保密，请在 Twitter @DevByRayRay 上给我发一条 [DM。我的 DM 永远是开放的😁](https://twitter.com/@devbyrayray)

[**通过电子邮件获取我的文章点击这里**](https://byrayray.medium.com/subscribe) **|** [**购买 5 美元中等会员**](https://byrayray.medium.com/membership)