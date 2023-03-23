# 如何开始使用？地图()。过滤器()和。减少()

> 原文：<https://betterprogramming.pub/how-to-start-using-map-filter-and-reduce-e01edba0d81>

## 一个不假设你是开发者的解释

![](img/4e0dd9c0e59c248a5c5345d466a81b72.png)

照片由[hello queue](https://unsplash.com/@helloquence?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/sales-tax?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

当我学会如何使用`.map()`、`.filter()`和`.reduce()`时，我读到的、看到的、听到的一切听起来都很复杂。这些概念是作为独立的实现来教授的，我无法理解。

我听说这些是象征着进入启蒙的提升的门户概念。我希望有人告诉我真相:这三个方法是识别和实现，你循环遍历一个 iterable 的原因通常属于三个功能类别中的一个。

回顾我以前编写的代码，我意识到在 95%的时间里，当循环字符串或数组时，我会执行下列操作之一:**将一系列语句映射到每个值，**过滤满足特定标准的值，或者**将数据集缩减为单个聚合值。******

这是我的突破时刻。映射，过滤和减少每一个简单地执行这些任务之一！

为了实践，我用这些方法重构了我的旧代码。那非常有帮助。

所以事不宜迟，我们继续吧。我们将解释每个方法，然后将常见的`for`循环实现转换成它们各自的方法。

# 地图

`.map()`方法是在你想 1。用 iterable 中的每个值和 2 执行一组语句。返回(大概)修改后的值。

让我们用一个简单的例子来计算一系列价格的销售税。

```
const prices = [19.99, 4.95, 25, 3.50];
let new_prices = [];for(let i=0; i < prices.length; i++) {
   new_prices.push(prices[i] * 1.06);
}
```

我们可以使用`.map()`获得相同的结果:

```
const prices = [19.99, 4.95, 25, 3.50];let new_prices = prices.map(price => price * 1.06);
```

上面的语法是精简的，所以让我们浏览一下。`.map()`方法接受回调，这可以看作是一个函数。这就是括号中的内容。

变量`price`是用于标识每个值的名称。因为只有一个输入，我们可以省略参数周围通常的括号。

箭头`=>`后的语句是我们回调的主体。由于主体只有一条语句，我们可以省略花括号和关键字`return`。

以防这仍然令人困惑，让我们完整地写出来以供参考:

```
const prices = [19.99, 4.95, 25, 3.50];let new_prices = prices.map((price) => {
   return price * 1.06
});
```

# 过滤器

接下来是`.filter()`方法，当您想要从 iterable 中提取值的子集时会用到这个方法。当使用`.filter()`时，记住我们是在过滤值，而不是过滤掉。这意味着 iterable 中评估为 true 的每个项目都将包含在过滤器中。

让我们用一个只保存奇数的例子。我们使用模数运算符来计算除以 2 的余数。当余数等于 1 时，我们知道这个数是奇数。

```
const numbers = [1,2,3,4,5,6,7,8];
let odds = [];for(let i=0; i < numbers.length; i++) {
   if(numbers[i] % 2 == 1) {
      odds.push(numbers[i]);
   }
}
```

类似于`.map()`方法，`.filter()`接受一个回调，iterable 中的每个值都将被传递到这个回调。

```
const numbers = [1,2,3,4,5,6,7,8];let odds = numbers.filter(num => num % 2);
```

类似的规则也适用于此回调。因为只有一个输入，函数的主体是一个表达式，我们可以省略参数列表括号、定义主体的花括号和`return`关键字。

# 减少

最后，我们来到`.reduce()`，不可否认，这是三种方法中最令人困惑的。该方法的名称是指将多个值减少到一个。然而，我发现更容易认为它是建立而不是减少。

该方法通过定义起始点来工作。当该方法迭代每个值时，该起始点被修改并向下传递。

这是一个将一系列数字相加的经典案例。假设我们正在计算给你最喜欢的慈善机构的捐款总额:

```
const donations = [5, 20, 100, 80, 75];
let total = 0;for(let i=0; i < donations.length; i++) {
   total += donations[i];
}
```

与`.map()`和`.filter()`不同，`.reduce()`方法的回调需要两个参数:累加器和当前值。累加器将是第一个参数，是“向下传递”的值。

```
const donations = [5, 20, 100, 80, 75];let total = donations.reduce((total,donation) => {
   return total + donation;
});
```

我们也可以将第二个参数传递给`.reduce()`方法本身。这将作为累加器的起始值。假设我们将昨天总计 450 美元的捐款相加。

```
const donations = [5, 20, 100, 80, 75];let total = donations.reduce((total,donation) => {
   return total + donation;
}, 450);
```

这就是了。这些方法不吓人！可以把它们看作是让你的代码更具可读性。您正在编写更精简的代码，但更重要的是，您实际上是在描述您的循环的意图。

三个月后，当你回头看你的代码时，你会觉得读起来容易多了。您可以看到 map/filter/reduce，并开始了解该块试图实现什么，而不必阅读 for 循环中的语句，只是为了理解它的高级意图。