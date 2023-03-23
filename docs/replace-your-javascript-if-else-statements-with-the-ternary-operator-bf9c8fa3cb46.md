# 用三元运算符替换 JavaScript If/Else 语句

> 原文：<https://betterprogramming.pub/replace-your-javascript-if-else-statements-with-the-ternary-operator-bf9c8fa3cb46>

## 通过重构您的业务逻辑来升级

![](img/7d72e2ed1e4c64126a78457fe6b6cb71.png)

约书亚·阿拉贡在 [Unsplash](https://unsplash.com/) 上的照片

当你学习编程时，你会不可避免地遇到这样的场景，你会想，*我本可以写得更好的*。

重写代码的机会，也就是所谓的重构，总是存在的。这样做是你编程技能成熟的标志。

变量和 if/else 语句是两个最早的编程概念，因此重构简单的 if/else 赋值语句是实践重构的最佳时机。

让我们来看看下面这个变量`canVote`的赋值，它将基于一个条件为真或为假。

```
const age = 19;if(age >= 18) {
   const canVote = true;
} else {
   const canVote = false;
}
```

这种类型的代码块足够简单，但是检查发现了冗余——我们有两个用于`canVote`的赋值语句。事实上，if/else 语句的全部目的就是给`canVote`赋值。

这就是三元运算符`?:`发挥作用、大放异彩的地方。

# 什么是三元运算符？

三元运算符是一种速记技术，用于根据条件分配两个值中的一个。让我们先用三元运算符重构上面的代码，然后再谈它的语法和好处。

```
const age = 19;const canVote = (age >= 18) ? true : false;
```

我们将五行 if/else 语句压缩成一行。厉害！现在，随着代码的浓缩，最初可能会更难阅读，所以让我们来解构三元运算符的各个部分。

首先，三元运算符有三个操作数——被运算的值——这就是它的名字的由来。按照出现的顺序，操作数是条件、正赋值和负赋值。

```
(condition) ? positive assignment : negative assignment 
```

那么它是如何工作的呢？

条件首先作为表达式进行计算，这意味着它要么为真，要么为假。当条件评估为真时，返回正赋值。当条件评估为假时，返回负赋值。

在我们之前的代码示例的上下文中，返回值被分配给`canVote`。

如果语法让你陷入循环，你并不孤单。很容易混淆不同的符号，这就是为什么我总是把三元运算写反了。

```
// step 1: write the ternary syntax
const canVote = () ? : ;// step 2: fill in the condition and assignments
const canVote = (age >= 18) ? true : false;
```

请记住，等号和分号不是三元运算符的一部分——它们是更大的赋值语句的一部分。

# 为什么要用三元运算符？

既然我们已经介绍了操作符并演示了如何重构旧代码，那么它有什么好的呢？

不可否认，当我第一次了解三元运算符时，我很抗拒采用它。我认为 if/else 语句更容易阅读。而且是…在真空中。

我们的大脑识别单词`if`和`else`比从一行代码中提取`() ? :`要快得多。然而，当您的 if/else 赋值存在于更大的程序上下文中时，从三元运算符获得的垂直空间可不是闹着玩的。

而且，如果我们重命名`canVote`会怎么样？对于传统的 if/else 语句，必须在两个地方进行更改。三元运算符赋值只发生在一个地方。

一旦你更熟悉了，三元运算符也可以用在函数中来简化 return 语句。

```
function canVote(age) {
   return (age >= 18) ? true : false;
}
```

是的，我知道上面的内容可以进一步重构，只返回对`age >= 18`的评估，但是为了保持一致，我们将保持原样。

你在你的标准实践中采用了三元运算符吗？你最喜欢的浓缩代码并使用它的机会是什么时候？