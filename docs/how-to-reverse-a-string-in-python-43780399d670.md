# 如何在 Python 中反转字符串

> 原文：<https://betterprogramming.pub/how-to-reverse-a-string-in-python-43780399d670>

## 学习两种方法，找出哪种最快

![](img/a74e864b9ec90e62ca694e3fade7ac46.png)

照片由 [Guillaume TECHER](https://unsplash.com/@guillaume_t?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/reverse?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

字符串是一系列字符数据。我们通常称之为*文本数据*。此外，字符串是可迭代的，也是可枚举的——一种可以计数的数据类型——也有结构化的顺序。反转一个字符串意味着把最后一个字符变成第一个，倒数第二个变成第二个，以此类推。

这里有一个直观的例子:

```
name = "Jonathan"# name in reverse => "nahtanoJ"
```

那么，我们如何反转一个字符串呢？我将介绍两种方法。

*   通过编程使用*反转/接合*。这是一种通用技术，涉及到创建一个反向可迭代对象，并将其重构为一个字符串。确切的实现是特定于 Python 的；然而，这种技术在许多语言中都有广泛的应用。
*   使用*切片符号*的 Pythonic 方式。这是一种更为 Python 特有的精简技术，因为切片符号并不是在所有语言中都可以找到。

# 技巧 1。反转/连接

这种方法包括将字符串视为字符数组，对其进行反转，然后将其重构为字符串。在其他语言中，这种技术可能涉及一个`for`循环来迭代每个索引并创建一个反向数据集。下一步是获取数组并将其重组为一个字符串。

这里有一个 JavaScript 示例来展示这在传统的`for`循环中是怎样的。

```
var name = "Jonathan";
var arr = [];for(var i=name.length-1; i >= 0; i--) {
   arr.push(name[i]);
}var flipped = arr.join("");console.log(flipped); // nahtanoJ
```

我们在这里做的是从最高的索引开始手动递减计数，用每个字符填充一个数组。最后，我们用一个空字符串作为分隔符来连接数组，这意味着字符是连续的，它们之间没有任何东西。

在 Python 中，我们没有传统的`for`循环。可以使用一个`while`循环来重新创建它，但是我们不打算走这条路。相反，我们将使用`reversed()`函数返回一个反向迭代器，该迭代器将类似地由空字符串连接。

```
name = "Jonathan"reversed = "".join(reversed(name))print(reversed) # nahtanoJ
```

注意`.join()`是 Python 实现中 string 类的成员。

# 技巧二。切片符号

切片符号是 Python 特有的技术，它允许在 iterable 的方括号内使用简写符号，然后执行传统的切片。

切片符号包括两个冒号和方括号内的最多三个值。第一个值、中间值和结束值(用冒号分隔)如下:[开始:停止:步骤]

通过省略开始和停止位置，同时将`-1`作为步长值传递，我们指示 Python 向后切片计数，并且不要在字符串结束之前停止。

```
name = "Jonathan"print(name[::-1]) # nahtanoJ
```

# 性能比较

根据[丹·巴德](https://dbader.org/blog/python-reverse-string)的说法，切片表示法比反向/连接技术快 7 倍！

我复制了他的测试场景，得到了类似的结果，但没有那么引人注目。

```
import timeitdef reverse_join(s):
    return "".join(reversed(s))def slice_notation(s):
    return s[::-1]s = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789"reverse_join_times = timeit.repeat(lambda: reverse_join(s))
slice_notation_times = timeit.repeat(lambda: slice_notation(s))avg_reverse = sum(reverse_join_times)/len(reverse_join_times)
avg_slice = sum(slice_notation_times)/len(slice_notation_times)print(avg_reverse) # 1.8536329854000002 
print(avg_slice) # 0.38605856120000015
print(avg_reverse / avg_slice) # 4.801429554206191
```

你有另外一种反转绳子的方法吗？在下面的评论中分享它和任何其他的想法或见解！