# 如何在 JavaScript 中测试执行时间

> 原文：<https://betterprogramming.pub/how-to-test-execution-time-in-javascript-aef9ed52f8da>

## 使用 console.time()和 console.timeEnd()

![](img/12b55676ed156d5af62c66c2957279b5.png)

Christina Kirschnerova 在 [Unsplash](https://unsplash.com/s/photos/abstract?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

你对你的代码运行得有多快(或多慢)感到好奇吗？您是否曾经想过两种潜在解决方案中哪一种性能更高？

你有没有因为太麻烦而被劝阻不要去发现？

我知道我是…或者更确切地说，我曾经是，直到我发现了一个超级简单的、不需要额外导入的方法来在我的代码中设置计时器。

我说的是`console`库中的两个方法:`.time()`和`.timeEnd()`

# 什么是“控制台”库？

控制台是一个内置的基本 JavaScript 功能库。我们经常使用`console.log()`来测试和打印基本输出，但是您知道`.log()`只是控制台库中的许多方法之一吗？

整个系列的教程可以专门用于控制台库，但这是另一天。只要知道它有各种各样的工具，让你的 JavaScript 开发生活变得更容易。

## 介绍 console.time()和 console.timeEnd()

我们将分别使用方法`.time()`和`.timeEnd()`来启动和停止我们的执行定时器。这两种方法都有一个参数——一个标识定时流程的名称。如果我们想调用我们的执行测试`"function_timer"`，那么我们将执行`console.time("function_timer")`来启动计时器，并用`console.timeEnd("function_timer")`结束它。结果将在几毫秒内以测试的名称打印到控制台。

```
let scores = [99,45,28,94,86];console.time("max");
let max = Math.max(...scores);
console.timeEnd("max");// max: 0.284ms
```

## 使用的好处。时间()和。时间结束()

使用`console.time()`和`console.timeEnd()`的好处在于它们是本地可用的，这意味着您不需要导入任何额外的库。

此外，没有必要搞乱 JavaScript 日期对象。

但是最重要的是，你可以让多个定时器同时运行，它们重叠也没什么不好。

```
let scores = [99,45,28,94,86];
let total = 0;console.time("outer-loop");
for(const score in scores) {
  console.time("inner-loop);
  total += score;
  console.timeEnd("inner-loop");
}
console.timeEnd("outer-loop");/*
inner-loop: 0.140ms
inner-loop: 0.021ms
inner-loop: 0.002ms
inner-loop: 0.002ms
inner-loop: 0.001ms
outer-loop: 2.045ms
*/
```