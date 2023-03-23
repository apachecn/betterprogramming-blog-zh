# 正则表达式简介

> 原文：<https://betterprogramming.pub/introduction-to-regex-8c18abdd4f70>

## 使用 Python 逐步介绍正则表达式

![](img/45dc6a6c73567cd13191498fc7b933f8.png)

照片由 [Fotis Fotopoulos](https://unsplash.com/@ffstop?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 什么是正则表达式？

Regex 代表*正则表达式*，本质上是一种简单的*定义字符模式的方式。正则表达式主要用于模式识别、文本挖掘或输入验证。*

Regex 让很多人望而却步，因为它乍一看像是胡言乱语。但那些知道如何使用它的人，似乎无法停止！这是一个值得学习的强大工具。

# 正则表达式简介

关于 regex，您需要知道的第一件事是您可以匹配特定的字符或单词。

让我们假设，我们想知道一个特定的字符串是否包含字母“a”或单词“lot”。我们可以使用以下 python 代码:

```
import re
str = "Learning regex can be a lot of fun"
lst = re.findall('a', str)
lst2 = re.findall('lot', str)
print(lst)
print(lst2)
```

这将返回一个包含三个匹配项的列表和一个包含一个匹配项的列表:

```
['a', 'a', 'a']
['lot']
```

保持相同的设置，假设您想要以任意顺序搜索这三个字母:a、b 和 c。您可以使用方括号列出一个列表:

```
lst = re.findall('[abc]', str)
lst2 = re.findall('[a-c]', str)
print(lst)
print(lst2)
```

这将返回:

```
['a', 'c', 'a', 'b', 'a']
['a', 'c', 'a', 'b', 'a']
```

![](img/89ff21a6acad16c12ef08821d3872144.png)

由 [Max Duzij](https://unsplash.com/@max_duz?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

让我们看另一个例子。假设你有一个很大的电子邮件存档，你想找到夏洛克发给他的朋友华生医生的所有电子邮件。您知道每个电子邮件文件头看起来会像这样:

```
From: Sherlock Holmes <[sherlock@mysteryemails.com](mailto:sherlock@mysteryemails.com)>
Subject: The giberrish looking algorithm
To: Dr Watson <johnhwatson[@mysteryemails.com](mailto:drwatson@mysteryemails.com)>
```

因此，您想要搜索包含单词“发件人:”后跟夏洛克的电子邮件地址和“收件人:”后跟沃森博士的电子邮件地址的内容。仅使用我们到目前为止介绍的基础知识，我们可以扫描文档一次，查找来自:夏洛克·福尔摩斯<[*sherlock@mysteryemails.com*](mailto:sherlock@mysteryemails.com)*>’*，然后再次查找来自:华生医生<[*@ mysteryemails . com*](mailto:drwatson@mysteryemails.com)*>*。

```
import re
str = '''
  From: Sherlock Holmes <[sherlock@mysteryemails.com](mailto:sherlock@mysteryemails.com)>
  Subject: The giberrish looking algorithm
  To: Dr Watson <[johnhwatson@mysteryemails.com](mailto:johnhwatson@mysteryemails.com)>'''lst = re.findall('From: Sherlock Holmes <[sherlock@mysteryemails.com](mailto:sherlock@mysteryemails.com)>',str)
lst2= re.findall('To: Dr Watson <[johnhwatson@mysteryemails.com](mailto:johnhwatson@mysteryemails.com)>',str)print(lst)
print(lst2)
```

返回此:

```
['From: Sherlock Holmes <[sherlock@mysteryemails.com](mailto:sherlock@mysteryemails.com)>']
['To: Dr Watson <[johnhwatson@mysteryemails.com](mailto:johnhwatson@mysteryemails.com)>']
```

匹配了这两个字符串后，我们知道这封邮件确实是从夏洛克发给沃森博士的。

# 正则表达式备忘单

每当我需要写一个复杂的正则表达式时，我的第一个停靠点是下面的[列表](https://www.py4e.com/lectures3/Pythonlearn-11-Regex-Handout.txt)，由查克·塞费恩博士提供:

```
Python Regular Expression Quick Guide^        Matches the beginning of a line
$        Matches the end of the line
.        Matches any character
\s       Matches whitespace
\S       Matches any non-whitespace character
*        Repeats a character zero or more times
*?       Repeats a character zero or more times 
         (non-greedy)
+        Repeats a character one or more times
+?       Repeats a character one or more times 
         (non-greedy)
[aeiou]  Matches a single character in the listed set
[^XYZ]   Matches a single character not in the listed set
[a-z0-9] The set of characters can include a range
(        Indicates where string extraction is to start
)        Indicates where string extraction is to end
```

使用上面的备忘单作为指南，您可以想出几乎任何语法。让我们仔细看看一些更复杂的搜索模式。

# 填补空白

根据我们前面的例子，我们希望使我们的模式匹配算法更加健壮。我们想搜索单词“ *From:* ”，然后是夏洛克的电子邮件地址。

使用上面的备忘单，我们可以使用'*。** '匹配任意字符零次或多次。

```
import re
str = '''
  From: Sherlock Holmes <[sherlock@mysteryemails.com](mailto:sherlock@mysteryemails.com)>
  Subject: The giberrish looking algorithm
  To: Dr Watson <[johnhwatson@mysteryemails.com](mailto:johnhwatson@mysteryemails.com)>'''lst = re.findall('From:.*[sherlock@mysteryemails.com](mailto:sherlock@mysteryemails.com)',str)print(lst)
```

这就给出了:

```
['From: Sherlock Holmes <[sherlock@mysteryemails.com](mailto:sherlock@mysteryemails.com)']
```

# 多行匹配

继续上一个例子，让我们扩展匹配模式，使其跨多行工作。我们可以使用 regex 的(M)multiline 函数来实现这一点。这里值得强调的是，这个函数期望我们*定义我们期望在模式匹配中看到新行*的地方。

```
import re
str = '''
  From: Sherlock Holmes <[sherlock@mysteryemails.com](mailto:sherlock@mysteryemails.com)>
  Subject: The giberrish looking algorithm
  To: Dr Watson <[johnhwatson@mysteryemails.com](mailto:johnhwatson@mysteryemails.com)>'''lst = re.findall('From:.+[sherlock@mysteryemails.com](mailto:sherlock@mysteryemails.com).+\n.*\n.*To:.+[johnhwatson@mysteryemails.com](mailto:johnhwatson@mysteryemails.com)', str,re.M)print(lst)
```

这给了我们这个:

```
['From: Sherlock Holmes <[sherlock@mysteryemails.com](mailto:sherlock@mysteryemails.com)>\n\t\tSubject: The giberrish looking algorithm\n\t\tTo: Dr Watson <[johnhwatson@mysteryemails.com](mailto:johnhwatson@mysteryemails.com)']
```

注意，我们有时用`.*`，其他时间用`.+`。不同之处在于，`.+`期望至少是一个字符，而`.*` 可以处理零个或多个字符。

# 加快步伐

假设您在一个输入字段上构建某种验证，用户可以在该字段中输入任何数字，后跟字母 d、m 或 y。

您的正则表达式算法看起来会像这样:

```
^[0-9]+[dmy]$
```

分解上述内容:^表示比赛的开始，后面跟着一个 0-9 的数字。然而,+号意味着至少需要一个 0-9 的数字，尽管可以有更多。然后字符串后面需要跟 d，m，或者 y，因为$所以必须在最后。

在 python 中测试上述内容:

```
import re
str = '1d'
str2 = '200y'
str3 = 'y200'
lst = re.findall('^[0-9]+[dmy]$', str)
lst2 = re.findall('^[0-9]+[dmy]$', str2)
lst3 = re.findall('^[0-9]+[dmy]$', str3)
print(lst)
print(lst2)
print(lst3)
```

返回以下内容:

```
['1d']
['200y']
[]
```

![](img/329acad41d4e3d1033d49ebd96c577a5.png)

凯文·Ku 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 转义特殊字符

说到正则表达式，某些字符是特殊的。例如，点、星和美元符号都用于匹配目的。如果你想匹配这些字符，会发生什么呢？

在这种情况下，我们可以使用反斜杠:

```
import re
str = 'Sentences have dots. How do we escape them?'
lst = re.findall('.', str)
lst1 = re.findall('\.', str)
print(lst)
print(lst1)
```

上面的例子是用点号和反斜杠点号。如您所料，它返回两个结果。第一个匹配所有字符，而第二个只匹配点。

```
['S', 'e', 'n', 't', 'e', 'n', 'c', 'e', 's', ' ', 'h', 'a', 'v', 'e', ' ', 'd', 'o', 't', 's', '.', ' ', 'H', 'o', 'w', ' ', 'd', 'o', ' ', 'w', 'e', ' ', 'e', 's', 'c', 'a', 'p', 'e', ' ', 't', 'h', 'e', 'm', '?']
['.']
```

# 匹配精确的字符数

假设你想匹配一个日期。你知道格式会是什么:DD/MM/YYYY。有时会有 2d 或 2Ms，有时只有一个，但总是 4y。

```
import re
str = 'The date is 22/10/2018'
str1 = 'The date is 3/1/2019'
lst = re.findall('[0-9]{1,2}\/[0-9]{1,2}\/[0-9]{4}', str)
lst = re.findall('[0-9]{1,2}\/[0-9]{1,2}\/[0-9]{4}', str1)print(lst)
print(lst1)
```

这给出了以下结果:

```
['22/10/2018']
['3/1/2019']
```

# 提取匹配的模式

仅仅知道你在匹配一个模式是不够的。你需要从比赛中提取信息的能力。

例如，假设您正在扫描一个大型数据集，寻找电子邮件地址。如果你用我们学到的，你可以寻找一个模式:

*   以字母、数字、点号或下划线开头
*   后跟至少另一个字母或数字
*   后面可以跟一个点或下划线
*   然后一个@
*   然后再次遵循与@之前相同的逻辑
*   最后，寻找一个点后面跟着一个字母

```
^[a-zA-Z0-9\.\_]*[a-zA-Z0-9]+[\.\_]*\@[a-zA-Z0-9\.\_]*[a-zA-Z0-9]+[\.\_]*\.[a-zA-z]+
```

从上面的匹配中，您只想提取域名，即@后面的所有内容。你所要做的就是在你要找的东西周围加上括号:

```
import re
str = '[email123_test@gmail.com](mailto:email123_test@gmail.com)'
lst = re.findall('^[a-zA-Z0-9\.\_]*[a-zA-Z0-9]+[\.\_]*\@([a-zA-Z0-9\.\_]*[a-zA-Z0-9]+[\.\_]*\.[a-zA-z]+)', str)
print(lst)
```

返回:

```
['gmail.com']
```

# 概括起来

总之，您可以使用正则表达式来匹配数据字符串，并且它可以以多种不同的方式使用。Python 包含一个名为 re 的 regex 包，它允许您使用这个包。但是，如果您发现自己在 Unix 机器上，您可以将正则表达式与 grep、awk 或 sed 一起使用。在 Windows 上，如果您想访问所有这些命令，可以使用 Cygwin 之类的工具。

参考资料:

*   [https://www . py4e . com/lectures 3/python learn-11-Regex-讲义. txt](https://www.py4e.com/lectures3/Pythonlearn-11-Regex-Handout.txt)
*   【https://en.wikipedia.org/wiki/Regular_expression 号