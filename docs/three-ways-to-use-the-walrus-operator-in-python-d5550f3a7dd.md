# 在 Python 中使用 Walrus 运算符的三种方式

> 原文：<https://betterprogramming.pub/three-ways-to-use-the-walrus-operator-in-python-d5550f3a7dd>

## 使用这个新的运算符扩展您的工具箱

![](img/e0ae829a6fa423cffa66719f031e6d9e.png)

照片由[弗罗林·科兹马](https://unsplash.com/@florinkozma?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/walrus?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

随着 Python 3.8 的发布，赋值表达式操作符(也称为 [walrus 操作符](https://medium.com/better-programming/what-is-the-walrus-operator-in-python-5846eaeb9d95))也发布了。

运算符允许将值的赋值传递给表达式。这通常会将语句数减少一个。例如:

```
my_list = [1,2,3]count = len(my_list)
if count > 3:
   print(f"Error, {count} is too many items")# when converting to walrus operator...if (count := len(my_list)) > 3:
   print(f"Error, {count} is too many items")
```

如果你不得不快速看一眼或者眨几下眼睛，你就会理解对这位新操作员的担忧。这似乎是为了最小的利益而混淆了两个简单的陈述。

假设对于*为什么* 引入这个操作符有一个合理的答案，那么自然的问题就是*它在现实世界中如何有用？*

我发现了三个领域，我已经并将继续采用海象运营商。此外，除了节省一行代码之外，每种方法都有一个附带的好处，我将分享这一点。

# While 循环

While 循环似乎是实现 walrus 操作符最常引用的上下文。在这里使用 walrus 操作符的好处是，您可以合并 while 语句中的表达式和修饰语。我无法告诉你我写了多少次无限循环，因为我忘了在 while 循环的末尾添加修饰符语句。

```
line = f.readLine()while line:
   print(line)
   line = f.readLine()while line := f.readLine():
   print(line)
```

不过，要小心。使用 walrus 操作符更适合传统的 do/while 循环，因为赋值发生在表达式之前。请参见下面的差异:

```
n = 0
while n < 3:
   print(n) # 0,1,2
   n += 1w = 0
while (w := w + 1) < 3:
   print(w) # 1,2
```

根据我有限的经验，我发现它在替换无限 while 循环时最有用:

```
while True:
   p = input("Enter the password: ")
   if p == "the password":
      breakwhile (p := input("Enter the password: ")) != "the password":
   continue
```

能够将 while 循环变成几乎一条语句已经很棒了。

# 列表理解

我发现 walrus 操作符对优化我的一些列表理解语句很有帮助。我寻找的两个标准是 1)过滤数据的需要和 2)存储征税函数的结果。首先，让我们看一个传统的列表理解语句:

```
scores = [22,54,75,89]valid_scores = [
   longFunction(n)
   for n in scores
   if longFunction(n)
]
```

注意情况`if longFunction(n)`？注意到我们的`longFunction()`被调用了两次吗？这是海象操作员的候选人。

```
scores = [22,54,75,89]valid_scores = [
   result
   for n in scores
   result := longFunction(n)
]
```

在我们优化的代码中，`longFunction()`只被调用一次，潜在地减少了理解语句中调用总数的一半。

# 处理返回的数据

这是对我来说最有帮助的 walrus 操作符的实现。我经常使用名为 iFormBuilder 的移动数据收集软件。具体来说，我请求记录(数据行)，如果我的查询返回数据，我需要处理它。下面是我每天写的一段代码:

```
# look for failed inspections
# if there are failed inspections, assign to technicianrecords = api.readFailedRecords()
if len(records) > 0:
   for record in records:
      api.assignToTechnician(record)
```

我已经开始使用 duck typing 合并记录的读取和检查，所以只有当返回的列表有数据时才执行这个块:

```
if records := api.readFailedRecords():
   for record in records:
      api.assignToTechnician(record)
```

就可读性而言，这种重构是破坏性最小的——事实上，我发现在这种情况下，walrus 操作符更容易阅读。这与前两个例子形成了对比，在这两个例子中，可读性受到了小的影响，有利于安心和性能。

# 结论

海象运营商是新的和有争议的，但只有时间会告诉它如何接受。你开始在你的代码中使用它了吗？