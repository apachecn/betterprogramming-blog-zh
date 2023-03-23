# 如何替换 JavaScript 中出现的所有字符串

> 原文：<https://betterprogramming.pub/how-to-replace-all-occurrences-of-a-string-in-javascript-5542c41f4989>

## 有什么不好。replace()以及如何克服它的基本用法

![](img/4734d949643b8e5854502bdb360ad33f.png)

Javier Allegue Barros 在 [Unsplash](https://unsplash.com/s/photos/change?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

字符串替换是一种常见的入门技巧，也是数据操作中经常需要的步骤。

JavaScript 有一个非常简单明了的字符串替换选项，但它有一些缺点，需要修改或使用完全不同的替代方法来实现“完全替换”,即交换所有出现的字符串。

# 的。replace()方法

JavaScript 有一个原生的`String`方法用于子串替换。`.replace()`方法有两个参数——一个搜索字符串和一个替换字符串。

```
let phrase = "If teh shoe fits";phrase = phrase.replace("teh", "the");console.log(phrase); // If the shoe fits
```

注意，我们必须将`phrase.replace()`重新分配给`phrase`。这是因为`.replace()`不修改原始值。

这种方法非常有效……直到你想替换不止第一次出现的搜索字符串。

```
let phrase = "teh teh teh";phrase = phrase.replace("teh", "the");console.log(phrase); // the teh teh
```

这里有两个选项，您需要替换搜索字符串的所有匹配项。一个继续使用`.replace()`方法，而另一个结合使用`.split()`和`.join()`方法。

# 使用正则表达式

`.replace()`方法实际上可以适应替换所有事件；但是，搜索字符串必须替换为正则表达式。正则表达式(简写为 regex)是一个根据基本字符串设置和比较的表达式。

为了指定我们使用的是正则表达式，搜索字符串嵌套在正斜杠中。为了全球化替换，我们在第二个正斜杠后使用了`g`修饰符。

```
let phrase = "teh teh teh";phrase = phrase.replace(/teh/g, "the");console.log(phrase); // the the the
```

使用正则表达式的挑战在于用于构建表达式本身的特殊字符。

如果你不精通的话，正则表达式会让人不知所措，看起来就像天书一样。此外，如果字符串本身包含特殊字符，那么表达式的复杂性会增加很多。

也就是说，这通常是最有效的选择。因此，如果您习惯使用正则表达式，并且您的字符串不包含冲突的特殊字符，那么请选择此选项。

# 使用。拆分()。join()技术

对于一个语法要求较低的选项，它将绕过对正则表达式的熟悉，并使用您很可能已经熟悉的工具，我们可以依次使用`.split()`和`.join()`的组合，将我们的字符串转换为数组，然后再转换回字符串。

搜索字符串将用作原始分隔符，实际上是将其从数据中删除，而替换字符串用作新的分隔符，将其放在与原始搜索字符串相同的位置。

```
let phrase = "teh teh teh";phrase = phrase.split("teh").join("the");console.log(phrase); // the the the
```

要在“慢动作”中看到这一点，我们可以分解方法链并打印每个步骤。

```
let phrase = "teh teh teh";phrase = phrase.split("teh");
console.log(phrase); // ["", " ", " ", ""]phrase = phrase.join("the");
console.log(phrase); // the the the
```

对于空字符串，拆分数组可能看起来很奇怪，但这是因为我们的分隔符恰好是字符串中的第一个字符，也是最后一个字符。

子串替换有别的方法吗？你觉得哪个更容易读和写？请在下面的评论中告诉我们！