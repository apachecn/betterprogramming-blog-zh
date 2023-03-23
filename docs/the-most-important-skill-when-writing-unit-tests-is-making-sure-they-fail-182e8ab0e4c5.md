# 编写单元测试时最重要的技能是确保它们失败

> 原文：<https://betterprogramming.pub/the-most-important-skill-when-writing-unit-tests-is-making-sure-they-fail-182e8ab0e4c5>

## 是的，说真的…

![](img/17d1ab421f4fe559d97c1679c2e11d7e.png)

照片由[克里斯里德](https://unsplash.com/@cdr6934?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

大约一年前，我在 LinkedIn 上看到 Craig Livings 的帖子，他发表了一段有争议的话:

> 任何从未失败的测试都不会增加任何价值

这意味着任何从未失败的测试都没有价值，因此可以删除。为了让任何测试增值，它必须至少失败一次。

让我们看看为什么测试失败是如此重要的技能…

# 为什么我们的测试一定会失败？

假设我们已经编写了一些代码和一些支持单元测试。我们做得很好，对吗？测试从未失败过，但是我们现在有了支持我们代码的单元测试！或者至少我们*认为*我们拥有…

让我们看一些例子。一个测试没有失败，另一个失败了。

## 从未失败的测试

这个例子是一个常见的场景，我们只是专注于编写一个通过的测试。我们无意让它失败。

我们将使用以下 JavaScript 函数:

```
function sum(a, b) {
  return a + b;
}module.exports = sum;
```

我们编写了下面的 [Jest](https://jestjs.io/) 测试来验证我们的`sum`函数将两个数相加:

```
const sum = require('./sum');

test('two plus two is four', () => {
  expect(2 + 2).toBe(4);
});
```

我们运行测试，它通过了✅

就是这样。我们很高兴测试通过了，所以我们继续前进。

## 失败的测试

在这个例子中，我们使用完全相同的代码。然而，这一次我们确保测试没有证明它的价值。

使用相同的功能:

```
function sum(a, b) {
  return a + b;
}module.exports = sum;
```

使用同样的测试:

```
const sum = require('./sum');test('two plus two is four', () => {
  expect(2 + 2).toBe(4);
});
```

我们运行测试，它通过了✅

但这一次我们要确保测试失败。所以我们决定修改代码:

```
function sum(a, b) {
  return 0;
}module.exports = sum;
```

我们重新运行测试，我们得到了另一个通过✅

但是等等…这个应该失败了。我们删除了将数字相加的代码。

我们可以看到测试仍然通过的原因是测试本身是不正确的。测试没有调用`sum`。

所以我们更新了测试:

```
const sum = require('./sum');test('two plus two is four', () => {
  expect(sum(2, 2)).toBe(4);
});
```

我们重新进行了测试，❌没有通过

好的，太好了！现在让我们把代码放回去:

```
function sum(a, b) {
  return a + b;
}module.exports = sum;
```

我们重新运行测试，它通过了✅

很好，现在我们的测试有了价值，因为我们确保了测试验证了正确的东西。

这个例子很简单。然而，当测试更复杂的代码时，很难发现这些错误。这就是为什么我们在通过测试之前确保测试失败是如此重要——因此我们对我们正在验证的东西有信心。

# 外卖

当编写测试时，我们必须知道它们失败了，所以我们知道它们增加了价值。

你同意吗？