# 你不知道你需要的 4 个 Ruby 方法

> 原文：<https://betterprogramming.pub/4-ruby-methods-you-didnt-know-you-needed-1b223ec1963c>

## 隐藏在 Ruby 发电站中的有趣方法

![](img/7d22ac1337af81becfd0857a5530d730.png)

照片由 [Unsplash](https://unsplash.com/s/photos/discover?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的[九科普夫](https://unsplash.com/@enka80?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)拍摄

我对 Ruby 编程语言有着特殊的热情，因为这是我学习的第一门语言。Ruby 实用、简洁，但高度可扩展。语法和结构看起来很令人愉快，而且这种语言也有广泛的支持基础。

我想整理一个有趣的列表，列出我在用 Ruby 开发时遇到的一些有趣的方法。

Ruby 有很多内置的方法。一些方法更有用，更常用，而另一些方法更短暂，或者最初不清楚它们的用途。下面我们将讨论一系列介于这两个极端之间的方法。

**注意:**每个部分的标题都包含一个到相关 [APIdock](https://apidock.com/) 页面的链接。

## 1.[点击](https://apidock.com/ruby/Object/tap)

首先，我们有一个最有用的 Ruby 方法。这种方法对于复杂代码的故障排除和调试非常有帮助。允许你直接点击方法链的中间，并重定向一些输出。

这就像在 Bash 中使用管道一样。您可以将左侧的输出通过管道传输到右侧，并对其进行重定向。

上面的链接有关于如何使用 tap 方法的更多详细信息，但下面是其功能的一个简单示例:

```
[1] pry(main)> var = "string".reverse.tap { |x| puts x }.upcase
gnirts
=> "GNIRTS"
[2] pry(main)> var
=> "GNIRTS"
[3] pry(main)>
```

让我们假设我们的目标是带着我们的小线和`reverse`然后`upcase`它。然后，我们希望将这个结果存储在一个名为`var`的变量中。让我们也假设我们想看看在反转阶段和上升阶段之间发生了什么。这就是 tap 的用武之地。

我们可以看到我们的最终输出被整齐地存储在`var`变量中，tap 没有干预。但是我们也可以看到在`upcase`阶段之前的字符串状态。

如果你有几个方法链接在一起做一些繁重的工作，这对于在它们中间做一些调试是很棒的。

## 2. [each_with_index](https://apidock.com/ruby/Enumerable/each_with_index)

这种方法肯定比前一种更常见，但有时在开发的狂热中很容易被遗忘。这就像经典的`each`方法，但是它给了你一个重要的好处:一个索引。

`each_with_index`不仅可以让你访问一个可枚举元素中的每一个单独的元素，还可以给你正在查看的元素的当前索引号。如果您可能需要根据可枚举的索引来跳转或跳过其中的元素，这可能会很有用。

假设您有一个数据数组，其中第二个和第三个元素是您想要丢弃的某种不重要的头值。您希望保留其余的数据，但在迭代数组时只需跳过这些值。你可以这样做:

```
[1] pry(main)> arr = ["one", "two", "three", "four", "five"]
[1] pry(main)> arr.each_with_index do |val, index|
[1] pry(main)*   next if index.between?(1,2)
[1] pry(main)*   <do_something_with_val>
[1] pry(main)* end
```

虽然这是处理这种情况的一种非常迭代的方法，但是它概述了这种方法的功能以及您可以利用它的所有独特方式。

## 3. [respond_to？](https://apidock.com/ruby/Object/respond_to%3F)

如果你曾经尝试过用[鸭子打字](https://medium.com/tech-and-the-city/looks-like-a-duck-quacks-like-a-duck-db1283502acc)(超级文章的道具 [Malina Tran](https://medium.com/u/135df4e82305?source=post_page-----1b223ec1963c--------------------------------) )或者读过任何 Sandi Metz 的书，那么你很可能接触过`respond_to?`方法。

如果对象响应传递的方法名，此方法返回 true。这可以是一串符号，它是一个方法的名称。看看下面这个关于字符串的快速示例:

```
[1] pry(main)> "string".respond_to?(:chomp)
=> true
```

这种方法不一定能洞察你正在使用的*是什么，*却能洞察*对*或*类似于*的嘎嘎声的反应。

记住，你是在物体之间传递信息——你不需要知道你与之交谈的物体的所有错综复杂的细节。事实上，你真的不应该。维护抽象是敏捷、可扩展接口的关键，这种方法允许您维护更多的抽象。

## 4.[挤压](https://apidock.com/ruby/String/squeeze)

这是一个非常简单的方法，它对字符串进行操作，并从本质上删除重复的字符。如果您简单地对包含多个相同字符的字符串调用`squeeze`方法，您将得到一个删除了重复字符的字符串:

```
[1] pry(main)> "aabbccdd".squeeze
=> "abcd"
```

你也可以在一个参数上传递`squeeze` ——应该被操作的字符串形式的一系列字符。如果您只想删除某些字符的重复项，这很有用。

# 结论

我希望您喜欢学习这些方法，并在您自己的项目中尝试它们——或者甚至只是在`irb`或`pry`中使用它们来了解它们是如何工作的。