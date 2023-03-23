# 你知道有猫王接线员吗？

> 原文：<https://betterprogramming.pub/did-you-know-theres-an-elvis-operator-1406cb364929>

## 还有哪些聪明的运营商名称？

![](img/bf32cf73a3af13b4e86fa8d83285f1d0.png)

由[格雷戈·奥特加](https://unsplash.com/@antisocia1?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/elvis?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

在 Python 3.8 发布了[海象操作符](https://medium.com/better-programming/what-is-the-walrus-operator-in-python-5846eaeb9d95)之后，我开始用它[做实验](https://medium.com/better-programming/three-ways-to-use-the-walrus-operator-in-python-d5550f3a7dd)，不禁被命名中的创意所折服。我是说，它看起来真的很像海象。因此，我问自己这样一个问题:还有哪些聪明的操作符名称？

这让我想到了猫王算子，`?:`，它因与他著名的头发相似而得名。虽然我不能说这是运营商的“王者”，但我发现它非常有价值，值得分享。

# 什么是猫王算子？

像其他有通俗名字的运营商一样，Elvis 也有一个技术或正式的名字。属于*二进制赋值操作符*的一类——这意味着它采用两个操作数进行赋值 Elvis 操作符是一种“逻辑或”,用于对变量(或常数)进行赋值。

也被称为空安全*，如果值的布尔表达式为真，Elvis 操作符将取两个操作数中的第一个；否则，采用第二个值。*

```
*// example is language agnostic
// assume variable 'a' is null// This would store the default string
my_var = a ?: "a default string"*
```

*Elvis 运算符的目的是浓缩一个[三元运算符](https://medium.com/better-programming/does-python-have-a-ternary-conditional-operator-830454c34de7)语句，其中“如果为真”值的布尔评估是行列式表达式。*

```
*// examples are language agnostic// ternary example
my_var = a ? a : b// expanded if/else example
if(a) {
   my_var = a
} else {
   my_var = b
}*
```

# *我的语言有猫王运算符吗？*

*很有可能，是的；虽然它可能不像国王。特定的字符序列“`?:`”出现在许多语言中，例如 C、C++、 [Kotlin](https://kotlinlang.org/) 、 [Groovy](http://groovy-lang.org/) 和 [ColdFusion](https://coldfusion.adobe.com/) 。*

*在 PHP 的三元运算符实现中，中间的操作数可能会被省略，在这种情况下，三元运算符类似于 Elvis 运算符，并且行为相同。*

*在 Python、JavaScript、Ruby、Perl 中，Elvis 运算符是不存在的；然而，OR 运算符(`||`、`or`)的功能类似。在这些情况下，在分配第一个或第二个操作数之间的确定不完全取决于第一个是否为空，而是取决于第一个是否为真；或评估到一个真实的结果。这意味着空字符串、空数组/列表以及对象/字典中未定义的键的计算结果为 false，并导致第二个操作数被赋值。*

*JavaScript 示例:*

```
*let name = "";let user = name || "Default Name"; # Default Name*
```

*Python 示例:*

```
*person = {
   "first_name": "Jonathan",
   "last_name": "Hsu"
}middle_initial = person['middle_name'][0] or None # None*
```

# *我为什么要使用 Elvis 运算符？*

*使用 Elvis 运算符的主要原因是为了清晰起见合并语句。这种整合不仅仅是视觉上的，如果上面例子中的`a`是一个长函数，Elvis 操作符也有可能影响性能。*

*我发现使用 Elvis 操作符的主要原因是防止空值或未定义的值；因此，我倾向于把它视为一种零安全。对我来说，这在处理接受参数的 Python 命令行脚本时最常见。*

```
*import argparseparser = argparse.ArgumentParser(description='')
parser.add_argument('--server', dest='server', help='iFormBuilder server name (ie. app.iformbuilder.com)')args = parser.parse_args()
server = args.server or ""*
```

*目的是在命令行中没有提供参数的情况下，以简洁的方式提供默认值。*

*为什么我想要一个参数的默认值？两个原因:*

*首先，后续函数经常需要特定的数据类型，比如字符串。我可以使用这种技术来分配一个空字符串，而不是传递一个未定义的值或空值。*

*其次，如果我想避免在命令行输入所有参数的障碍，或者我想简化特定客户的执行，我可以跳过这些参数(假设我已经将它们设置为可选的)，并将客户的特定值设置为第二个操作数。*

```
*import argparseparser = argparse.ArgumentParser(description='')
parser.add_argument('--server', dest='server', help='iFormBuilder server name (ie. app.iformbuilder.com)')args = parser.parse_args()
server = args.server or "default.iformbuilder.com"*
```

# *结论*

*您可以在代码中使用 Elvis/null-safety/short-circuiting OR 运算符来防止空值，并在主要选项失败时提供默认的赋值。*

*您最喜欢这些运算符的哪些用途？*