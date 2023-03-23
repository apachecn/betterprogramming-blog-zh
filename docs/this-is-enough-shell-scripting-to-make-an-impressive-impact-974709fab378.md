# 这些 Shell 脚本足以产生令人印象深刻的影响

> 原文：<https://betterprogramming.pub/this-is-enough-shell-scripting-to-make-an-impressive-impact-974709fab378>

## 一小段代码可以走很长的路

![](img/da15168273b7fc2124ff1591d164ba93.png)

由 mauricio Santos 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

你见过寿司师傅用武士刀切鱼吗？是啊，我也是。它可能看起来很棒，但我们都同意这样的刀片不是将鱼变成晚餐的最佳工具。

我喜欢 Python，我已经用它做了很多大大小小的事情，但是如果编程语言是刀片，那么即使是像 Python 这样的高效语言对于小而琐碎的任务来说也会感到沉重和笨拙。

对于这样的任务，shell 脚本可以强大得多，因为它提供的保证甚至比 Python 或 JavaScript 更少，但使您能够更快地自动化。

你可能会问，“那么，具体是在什么时候？”。在我看来，这在以下情况下很有用:

*   手头的任务相当小，您的自动化不需要防错。
*   如果自动化不工作，您仍然可以手动进行。那会花费你更多的时间，但也不是不可能的。
*   您可以根据终端中的几个命令来考虑您的任务

shell 脚本最棒的地方在于，您不需要知道太多就可以开始变得更有效率。通常情况下，你甚至不会启动文本编辑器。我将把重点放在你大部分时间实际需要的必需品上。

我们开始吧！

# 循环

您可以使用以下语法执行 for 循环:

```
$ for thing in 0 1 2 "string" 4; do echo $thing; done;
0
1
2
string
4
```

请注意，美元符号意味着`thing`是一个变量——它本身不是一个符号。

知道这一点很好，但很少有用。您还可以生成更强大的数字序列。语法稍有不同:

```
$ for number in {0..5}; do echo $number; done;
0
1
2
3
4
5
```

如果你想在一个文件上存储一个命令的输出，你只需要使用`>>`重定向操作符。`>>`将追加到文件中，而`>`将用新内容替换旧文件。

```
$ for number in {0..5}; do echo $number >> numbers.txt; done;
```

想看看它长什么样吗？你可以做`cat numbers.txt`或者只做`head numbers.txt`，如果太长了，你只想看一眼。

```
$ cat numbers.txt
0
1
2
3
4
5
```

# 命令替换和管道

如果不能利用终端中已经可用的各种命令，你将不会走得很远——更不用说可以用包管理器安装的东西，如`apt`或`brew`、`pacman`等。

让我们在`for`循环中使用简单的`cat`,使用被称为命令替换的`$()`语法来迭代文件中的行。

```
$ for number in $(cat numbers.txt); do echo $number; done;
0
1
2
3
4
5
```

[管道也是一个很好的工具](https://bash.cyberciti.biz/guide/Pipes)来做一些类似命令替换的事情，但是具有非常不同的语法，并且在底层的实现也有点不同。

它们所做的是将一个命令的输出作为下一个命令的输入。让我们`cat`我们的数字，但将输出馈入`sort`，因此它们最终以`--reverse`的顺序输出:

```
$ cat numbers.txt | sort --reverse
5
4
3
2
1
0
```

# 不离开终端的黑客新闻

有一套程序可以完美地解决你想在终端中解决的任何问题，但是假设你需要从黑客新闻中获得最新的新闻。

你可以用`curl`程序做请求。默认情况下，它输出响应正文:

```
$ curl [https://google.com](https://google.com)<HTML><HEAD><meta http-equiv="content-type" content="text/html;charset=utf-8">
<TITLE>301 Moved</TITLE></HEAD><BODY>
<H1>301 Moved</H1>
The document has moved
<A HREF="https://www.google.com/">here</A>.
</BODY></HTML>
```

现在让我们看看来自 HN 的头条新闻。他们提供了一个我们可以使用的简洁的 API，但是对于更大的请求，`curl`会显示一个进度条输出。让我们用`-s`选项删除它:

```
$ curl -s [https://hacker-news.firebaseio.com/v0/topstories.json?print=pretty](https://hacker-news.firebaseio.com/v0/topstories.json?print=pretty)[ 22679905, 22679894, 22679888, 22679875, 22679867, 22679849, 22679845, 22679837, 22679828, 22679808, 22679788, 22679784, 22679776, 22679764, ..., 22675186 ]
```

酷！现在让我们用`[jq](https://stedolan.github.io/jq/manual/)`[程序](https://stedolan.github.io/jq/manual/)解析那个`json`数组。我们希望让它在新的一行中输出每个故事 ID，就像我们在本文开始时对数字所做的那样:

```
$ curl -s [https://hacker-news.firebase...](https://hacker-news.firebaseio.com/v0/newstories.json?print=pretty) | jq -r '.[]'
22675852
22675849
22675842
22675832
22675831
22675808
22675805
...
```

好吧，我打赌你已经知道该怎么做了。让我们使用命令替换，并对此进行迭代！我还将导出一个变量，以便更好地显示在屏幕上:

现在让我们做一些更有趣的事情，通过使用 ID 来达到故事的终点:

对于这些`echo`调用中的每一个，您都会看到类似下面的响应:

```
{ “by” : “smacktoward”, “descendants” : 0, “id” : 22680175, “score” : 1, “time” : 1585092547, “title” : “I had the symptoms. But did I have the coronavirus?”, “type” : “story”, “url” : “[https://www.nytimes.com/2020/03/24/opinion/coronavirus-testing.html](https://www.nytimes.com/2020/03/24/opinion/coronavirus-testing.html)" }
```

让我们再次用`jq`解析它们，并以更容易阅读的方式只输出`title`和`link`:

会输出这样的结果。为了几个概念和外部程序不算太寒酸吧？

```
“Zig cc: A drop-in replacement for GCC/Clang”
[https://andrewkelley.me/post/zig-cc-powerful-drop-in-replacement-gcc-clang.html](https://andrewkelley.me/post/zig-cc-powerful-drop-in-replacement-gcc-clang.html)
 —--
“Dyson develops ventilators for NHS”
[https://www.bbc.com/news/technology-52021757](https://www.bbc.com/news/technology-52021757)
 ---
“Swift 5.2 Released”
[https://swift.org/blog/swift-5-2-released/](https://swift.org/blog/swift-5-2-released/)
 ---
“Little Snitch and the deprecation of kernel extensions”
[https://blog.obdev.at/little-snitch-and-the-deprecation-of-kernel-extensions/](https://blog.obdev.at/little-snitch-and-the-deprecation-of-kernel-extensions/)
 ---
“U.S. Retailers Plan to Stop Paying Rent to Offset Virus Closures”
[https://www.bloomberg.com/news/articles/2020-03-24/u-s-retailers-plan-to-stop-paying-rent-to-offset-virus-closures](https://www.bloomberg.com/news/articles/2020-03-24/u-s-retailers-plan-to-stop-paying-rent-to-offset-virus-closures)
```

# 包扎

Shell 脚本并不是每项工作的最佳工具，但它肯定会让您作为技术专业人员的生活变得更加轻松。这也是让你与众不同的东西。

最棒的是这东西并不难。一旦你掌握了重要的 20%的概念和参考资料，就足以完成很多工作了。

但是，如果您想让您的 shell 脚本更上一层楼，我建议您学习以下内容:

*   If-elif-else 逻辑以及`test`命令(又名`[`)。
*   `grep`命令，以及一点正则表达式(如果你还没有的话)。
*   内置的`set`用于调试脚本和修改整体行为。
*   `cut`号的命令。
*   `awk`命令。
*   `sed`命令。

# 资源

*   [https://github.com/HackerNews/API](https://github.com/HackerNews/API)
*   [https://stedolan.github.io/jq/manual/](https://stedolan.github.io/jq/manual/)
*   [https://www.tldp.org/LDP/abs/html/textproc.html](https://www.tldp.org/LDP/abs/html/textproc.html)