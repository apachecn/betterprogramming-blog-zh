# 在提交编码面试任务之前你应该考虑什么

> 原文：<https://betterprogramming.pub/what-you-should-consider-before-submitting-that-coding-interview-task-a655dbb6061e>

## 面向初级开发人员，但不仅限于此

![](img/3210b97a47c82779683659ed7944655e.png)

Tetiana SHYSHKINA 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

不管我们同意与否，如今大多数公司都将编码挑战作为招聘过程的一部分。

为这些挑战做准备可能会令人困惑。从我回顾的测试中，我发现候选人经常专注于研究花哨的算法，而忽略了编码挑战中最关键的方面。

在向面试官提交代码之前，这里有一些你需要考虑的建议。

# 字谜问题

我正在帮助我的朋友，一个初级开发人员，为技术挑战做准备。我们在做一个字谜题。这是一个常见的面试问题，面试官评估候选人对数据结构和算法的了解程度。

> [**问题陈述:**](https://web.stanford.edu/class/cs9/sample_probs/Anagrams.pdf)
> 
> 如果你可以通过重新排列字母将第一个字符串变成第二个字符串，那么这两个字符串就可以说是彼此的变位词。例如，“table”和“bleat”是变位词，“tear”和“rate”也是。你的工作是编写一个函数，接受两个字符串作为输入，并确定它们是否是彼此的变位组合。
> 
> **约束:**
> 
> 每个字符串由 ASCII[a-z]范围内的字母组成

过了一段时间，我的朋友想出了下面的解决办法:

```
public static boolean areAnagrams(string w1, string w2){char[] chr1 = w1.toCharArray();
char[] chr2 = w2.toCharArray();int[] count = new int[26];for (char ch : chr1){
    count[ch - 97] = count[ch - 97] + 1;
  }for (char ch : chr2){
    count[ch - 97] = count[ch - 97] - 1;
  }for (int n : count) {
    System.out.format("%d%n", n);if (n != 0){
      return false; 
    }
  }return true;
}
```

在接下来的部分中，我将使用这个例子来说明我在审查初级开发人员的代码时注意到的一些技巧和常见问题。

# 停止使用神奇的数字

看他的代码首先跃入脑海的是那些在代码中重复出现的幻数:`97`和`26`。

幻数被认为是一种糟糕的编程模式，原因如下:

1.  除了编写代码的开发人员之外，没有人知道这些神奇的数字是什么，甚至这个开发人员也会随着时间的推移而忘记它。
2.  它们增加了出错的机会。如果有人错误地修改了第二个循环中的数字`97`会怎么样。该代码似乎依赖于两个循环使用相同的数字这一事实。
3.  如果我们决定在将来更新这个函数，我们将不得不查找这些幻数的所有出现并更新它们。

根据问题陈述，我可以假设`97`是字母`a`的 ASCII 十进制代码，而`26`是 ASCII[a-z]范围内的字母数。

这个问题可以很容易地通过使用常数来解决。

# 在命名变量时要小心

在我朋友的例子中，他称他的变量为`w1`和`w2`、`ch1`和`ch2`、`n`。

`w1`和`w2`是输入——要比较的字符串。使用一个更明确的名字会使代码更容易阅读。比如`first`和`second`，或者`word1`和`word2`。

`ch1`和`ch2`似乎是`first`和`second`输入的字符数组。这些也可以重命名为`first_chars`和`second_chars`。

最后，`count`应该是一个数组，它将一个索引与字母的出现次数相关联。为什么不改名为可读性更强的，比如`frequencies`？

# 清理您的代码

在提交您的实现之前，请确保清除所有打印功能和`console.logs`。

调试你的代码是极好的，但是你不希望它被提交。它使代码变得混乱，并使审查变得困难。

如果您对问题或输入做出了任何假设，例如，为了缩小范围，最好在注释或自述文件中进行描述。

# 修正的字谜问题

考虑到前面的几点，代码看起来有点大，但是更容易阅读，并且所有的上下文都在那里。无论是谁审查它，都不需要挠头去理解它的作用。

```
public static boolean areAnagrams(string first, string second){int MAX_LETTERS = 26;
int[] frequencies = new int[MAX_LETTERS];
char a = 'a';for (int i = 0; i < first.length(); i++){
  char c = first.charAt(i);
  int offset = c - a;
  frequencies[offset]++;
}for (int i = 0; i < second.length(); i++){
  char c = second.charAt(i);
  int offset = c - a;
  frequencies[offset]--;
}for (int n : frequencies) {
  if (n != 0){
   return false; 
  }
}return true;
}
```

# 检查您的代码

你设法解决了这个问题，你写了一个有效的算法。现在怎么办？

仔细检查您的实现，并尝试看看如何改进它。不可否认，从不同的角度看待你已经实现的东西并不总是容易的。如果你的挑战没有时间限制，休息一下，一小时后再回来。

当看到我朋友的代码时，我想到的一件事是，它可以进一步优化。例如，当两个字符串没有相同数量的字母时，它们永远不会是变位词。

我们可以在遍历所有迭代之前先进行检查。

```
public static boolean areAnagrams(string first, string second){if (first.length() != second.length()) {
  return false;
}int MAX_LETTERS = 26;int[] frequencies = new int[MAX_LETTERS];
char a = 'a';for (int i = 0; i < first.length(); i++){
  char c = first.charAt(i);
  int offset = c - a;
  frequencies[offset]++;
}for (int i = 0; i < second.length(); i++){
  char c = second.charAt(i);
  int offset = c - a;
  frequencies[offset]--;
}for (int n : frequencies) {
  if (n != 0){
   return false; 
  }
}return true;
}
```

# 不要提交未经测试的代码

测试你的代码非常重要。编写一个函数来检查不同的输入，并确保结果符合预期。

测试还会揭示代码中的盲点。

在回顾我朋友的代码时，我想到一件事，他假设字符串`first`和`second`都在 ASCII[a-z]的范围内。但是，如果用户输入的字符串包含超出该范围的字符，会发生什么情况呢？该函数可能试图访问非法索引并抛出一个`ArrayIndexOutOfBoundsException`。

在这种情况下，我们需要一个验证器函数来检查输入，并在输入无效时抛出错误。

# 针对更有经验的开发人员的测试

这个字谜测试是一个针对初级开发人员的简单例子。对于更有经验的开发人员来说，评审人员还会检查您的实现中关注点的分离。

这意味着你不应该把所有的代码放在一个巨大的函数中。`areAnagrams`应该检查两个字符串是否是变位词，并返回一个布尔值，仅此而已。

# 添加自述文件和安装指南

在压缩和提交代码之前，请确保您有一个自述文件，解释评审者如何安装依赖项和使用您的代码。

当审查者想要测试你的代码时，它应该马上工作。他们不应该花时间去弄清楚需要安装哪个版本的依赖项才能工作。

# 注意你的提交历史

如果你通过 GitHub 提交代码，请注意提交历史。你的承诺应该遵循你承诺的回购协议的贡献准则。

提交消息应该是不言自明的——我强烈建议遵循[角度提交消息惯例](https://github.com/angular/angular/blob/master/CONTRIBUTING.md#commit)。

# 额外收获:你真的需要那个前端框架吗？

除非在您的编码任务中明确说明，否则您可能不需要花哨的前端框架。

如果我正在审查一个前端申请人的代码，我需要知道他们能用 HTML、CSS 和普通 JavaScript 制作一个简单的网页。

# 最后的想法

当我审查一个编码任务时，我不一定在乎你写了最快的算法。所以我没有分析我朋友的做法。我们每天编写的代码并没有那么复杂；许多图书馆已经实现了所有奇特的算法。

我认为评审者关心的是代码的清晰性，良好的编程模式，测试等等。