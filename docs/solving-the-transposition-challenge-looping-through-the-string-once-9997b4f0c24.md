# 在字符串中循环一次，解决变调挑战

> 原文：<https://betterprogramming.pub/solving-the-transposition-challenge-looping-through-the-string-once-9997b4f0c24>

## 转置字符串中的两个字符，而不会失去理智

![](img/4b5267795431c6743cd92f3cbab14ffd.png)

在某种程度上，每个人的脑海中都有一首歌(好的或坏的)，对我来说，这首歌就是我今天要写的编程问题。现在解决了，旋律也在我脑子里解决了，我想和你分享一下我的知识。

我将很快向您展示问题陈述，以及一些您可以运行代码的测试用例，然后我将向您展示我是如何摆脱这个问题的。

如果你对他们没有任何经验，可以看看这个关于 deques 的[教程。此外，我想说的是，你可以拿着我将要向你展示的东西进行实验，因为我相信它有许多令人兴奋的应用，如果你找到了，你可以随时在回复中给我留言。](https://medium.com/better-programming/implementing-a-deque-in-ruby-cf6e9bfd9c3c)

作为本教程的附加内容，我将向您展示如何使用基准模块来测试您的算法的性能时间。

让我们开始吧！

# 学习成果

*   能够使用队列对字符串进行排序
*   能够使用 Ruby 的基准模块测试代码性能

# 问题陈述

换位错误是指两个字母被调换，比如“hte”代替了“the”，或者“rign”代替了“ring”。给定一个字符串，你能修复每个出现“gn”的地方，使它变成“ng”吗？如果一行中有多个“g”，则“n”应该与第一个“g”交换。最终文本中不应该出现“gn”，所以要小心所有的可能性！

# 全额信贷

尝试仅通过一遍课文来解决这个问题。

## 例子

以下是三个测试案例:

*   `puts transpose(‘he was searchign on Bign for signign kigns’)`

`*Expected Output => he was searching on Bing for singing kings*`

*   `*puts transpose(‘rignadingdiggn!’)*`

`*Expected Output => ringadingdingg!*`

*   `puts transpose(‘gngngnnggnngggnnn’)`

`*Expected Output => nnnnnnnnngggggggg*`

看着问题陈述，我相信你已经想到了一些解决方案。让我们来看看一个非常简单的解决方案:

```
while string.include?('gn')
  string.gsub!("gn", 'ng')
end
puts string
```

这个解决方案只是尽可能多次地遍历字符串，直到它消除所有“gn”的实例。如果您的字符串中有一个、两个甚至三个换位错误，这是一个完美的解决方案，但是如果错误数量增加，会发生什么呢？请看下面的片段:

`transposer(‘gngngnnggnngggnnngngngngngngngngngngngngngngngngngngngngngngngngngngngngngngnnnnnnnnnnnnnnggggggggggggggggggggggggggggggggggggggggggngngngngnnnnnnnnnngng’)`

如果我们对此进行基准测试，我们会看到运行时间增加了一倍多。想象一个大得多的测试案例，像一个完整的 Microsoft Word 文档，有许多换位错误，我们可以很快看到为什么暴力解决方案可能不是最可靠的。

我想了一会儿，在一张白纸上草草写了一些伪代码，就可以完成你将要看到的东西。

# 解决办法

## 步伐

*   在字符串中循环
*   如果你到达一个 g，把它推到一个 deque 的后面
*   如果你到达一个 n，把它推到队列的前面
*   如果您找到的不是上面两个字符，将 deque 清空到结果中，然后将这个新字符添加到结果中
*   继续，直到到达字符串的末尾

如果你第一眼看不明白这些步骤，没关系，当我们完成实现时，你会明白的。

我们写点代码吧！

出于本教程的目的，我不会解释 dequee 中的方法，除非我在关于 deque = Deque.new
#initialize the result variable
result = ''

#Correctly define our loop
for i in 0..string.length do
#The rest of our code goes in here
end

#Return our result
result
end

让我们把操作写在循环中。如果我们仔细观察伪代码的第二步到第四步，使用组合 If 语句来处理我们的检查是有意义的:

```
if string[i] == 'g'
  deque.pushBack(string[ i ])
  next
elsif string[i] == 'n'
  deque.pushFront(string[ i ])
  next
else
  deque.empty_into(result) unless deque.empty?
end
```

首先，我们检查“g”字符。如果我们遇到一个，我们把它推到德克后面。`elsif`模块正在检查是否遇到‘n’字符。最后，如果我们遇到除了‘g’或‘n’之外的任何字符，`else`块将把 deque 清空到结果字符串中。

*注意:注意* `*if*` *和* `*elsif*` *块中的* `*next*` *语句。它们确保当找到“n”或“g”时，循环直接进入下一次迭代。*

现在，对于上面伪代码中概述的最后一步，如果字符串中的某个字符既不是“n”也不是“g”，我们只需将它附加到结果字符串中:

```
if string[ i ] != 'g' && string[i] != 'n' && string[i]
  result += string[ i ]
  next
end
```

这样，我们就在`transpose`算法上打结了。接下来，我想向您展示为什么以及何时这种解决方案可能比暴力解决方案更可取。为此，Ruby 内置了漂亮的基准测试模块。

将以下代码片段添加到您的文件中的`transpose`函数之后:

```
Benchmark.bm(10000000) do |x|
  x.report {puts transposer('gngngnnggnngggnnngngngngngngngngngngngngngngngngngngngngngngngngngngngngngngnnnnnnnnnnnnnnggggggggggggggggggggggggggggggggggggggggggngngngngnnnnnnnnnngng')}
end
```

现在在您的终端中运行代码。它应该运行几秒钟，最后显示运行算法 10000000 次所用的总时间。

对于暴力解决方案，该数字应该在 0.001214 左右徘徊，而对于我们的最佳解决方案，它应该在 0.000376 秒左右。我们可以看到强力解决方案的运行时间比最优解决方案的运行时间长得多。随着我们增加算法运行的次数，我们会注意到最优解的时间并没有增加很多，但是强力求解的时间一直在增加。这是因为强力解决方案的时间复杂度为 O(n ),而我们的优化解决方案的时间复杂度为 O(n)。

我希望这篇教程对你有所帮助——下次再见！

# 资源

在[我的报告](https://github.com/Oluwadamilareolusakin/transposition-interview-challenge)中找到本教程的代码。

要获得更多关于实现 deques 的教程，请查看[在 Ruby 中实现 deques](https://medium.com/better-programming/implementing-a-deque-in-ruby-cf6e9bfd9c3c)。