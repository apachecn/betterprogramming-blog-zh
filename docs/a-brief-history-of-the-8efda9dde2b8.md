# 编程中分号的简史

> 原文：<https://betterprogramming.pub/a-brief-history-of-the-8efda9dde2b8>

## 分号的用法到底是从哪里来的？

![](img/c1c20371465b5baff2b35cbcb0b53b3a.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上 [ActionVance](https://unsplash.com/@actionvance?utm_source=medium&utm_medium=referral) 拍照

在本文中，我将简要介绍分号(许多计算机科学入门学生的祸根)在计算机编程语言中的使用历史。我们将看到分号有两种用途:作为语句分隔符和语句终止符，我们将看到分号的使用在过去 60 年左右的编程语言历史中是如何变化的。

不过，在开始之前，让我先说明一下这篇文章副标题的出处。这句话被认为是计算机科学的真正先驱之一，Alan Perlis。我在有史以来最伟大的计算机科学教科书之一“计算机程序的结构和解释”中发现了这句话，作者是哈尔·阿伯尔森和杰拉德·让伊·萨斯曼。

这段引文可以在该书第 11 页的脚注中找到。在脚注中，作者正在讨论在许多编程语言中发现但在类似 Lisp 的语言中没有的特殊语法形式的缺乏，例如 Scheme(本书的目标语言)。

术语*句法糖*最初是由彼得·兰丁创造的，指的是可以用更统一的方式编写的方便的句法特征，比如在 Lisp 中使用括号，而不是一种语言的所有不同的句法特征，比如 C 和其他类似 C 的语言，它们有花括号、方括号、分号、冒号等等。

# 为什么编程语言使用分号

分号在编程语言中有两个用途:语句分隔符和语句终止符。当一种语言使用分号作为语句分隔符时，这允许您在同一行上编写多个语句，分号用于标记语句的分隔，以便编译器或解释器可以找出一个语句的结束位置和另一个语句的开始位置。

在分号是分隔符的语言中，分号被视为可选的，当一行代码只包含一条语句时，通常不写分号。

分号作为语句结束符不是可选的，它用于明确标记语句的结束。当分号不在预期的位置时，使用分号作为终止符的编程语言将标记一个错误。许多编程专家声称分号结束的语句会导致更快的程序，因为编译器可以更有效地执行——不过，在本文的结尾，我会对这种说法提出质疑。

# 早期高级语言和分号的使用

当 Fortran 在 20 世纪 50 年代中期作为高级编程语言引入时，编程语言的史前时代就结束了。Fortran 没有使用分号作为语句分隔符或语句终止符。Fortran 中的语句每行只能写一条，新行是语句结束符。

现代编程语言中分号的第一次使用是在 ALGOL 58 中。然而，这个版本并没有被大量实现，它很快演变成了 ALGOL 60。ALGOL 的这个早期版本使用分号作为分隔符，尽管它经常被多余地用作语句结束符(尽管这不是必需的)。

分号的这种混合使用可能会导致非常混乱的程序，其中分号经常放在一些行的末尾，而不是其他行的末尾，如我从 [Steve Maurer 的网站](http://cs.ecs.baylor.edu/~maurer/SieveE/Algol60.htm)借来的这段 ALGOL 60 代码所示:

```
COMMENT advance to the next un-crossed out number. ; 
COMMENT this number must be a prime ; 
FOR i := i WHILE i<1000 AND candidates[i] = 0 DO 
  BEGIN 
      i := i+1; 
  END; 
  COMMENT insure against running off end of the data structure ; 
  IF i<1000 THEN
      BEGIN
         COMMENT cross out all multiples, starting with 2*p.; 
         j := 2; 
         k := j*i;
        FOR k := k WHILE k < 1000 DO 
             BEGIN 
                  candidates[k] := 0; 
                  j := j + 1; 
                  k := j*i; 
             END; 
             COMMENT advance to the next candidate ; 
             i := i+1; 
      END 
 END;
```

Algol 60 导致了 Algol 的其他版本……导致了 Algol 68。很多很多语言都是基于 Algol 的，但是对当今语言影响最大的版本是 Basic 组合编程语言(BCPL)。

BCPL 是一种编程语言，由剑桥大学的 Martin Richards 于 1967 年开发。BCPL 是用许多后来影响了 C 语言发展的概念设计的，我们马上就会看到。BCPL 也使用分号作为语句分隔符，许多编写 BCPL 的程序员也使用分号来表示一行的结束——尽管和 Algol 一样，这种用法是多余的。在 BCPL，行尾字符是真正的语句分隔符。

另一种影响分号当前使用的语言是 IBM 的编程语言 PL/I。PL/I 开发于 20 世纪 60 年代中期，是当时的 C++语言，因为该语言的设计者试图包含所有其他流行编程语言的所有功能，因此该语言可以取代科学计算的 Fortran 和商业计算的 COBOL。

PL/I 使用分号作为语句终止符，这一设计决策对其他语言实现者产生了影响，如 c 语言的开发者 Ken Thompson 和 Dennis Ritchie。

# b、NB、C 和分号

大约在 1968 年左右，随着贝尔实验室逐渐退出 Multics 项目，Ken Thompson 开始开发一种基于 BCPL 的新语言，他称之为 B。你可以在 Dennis Ritchie 关于 C 语言历史的文章中读到更多关于 B 语言发展的内容，点击这里找到[。](https://www.bell-labs.com/usr/dmr/www/chist.html)

Thompson 将 B 设计成一种系统编程语言，用于开发当时还处于萌芽状态的 Unix 操作系统。Thompson 在他设计的 B 编程语言中对 BCPL 做了许多修改，其中一个修改是强制使用分号作为语句终止符。正如我上面提到的，分号作为语句结束符在 BCPL 是可选的，但这仅仅是因为编译器使用了巧妙的规则来允许分号不存在。Thompson 想让 B 成为一种更有效的语言，所以需要分号作为语句结束符。

当 Thompson 和 Ritchie 试图用 B 语言构建 Unix 时，他们意识到即使 Thompson 的新语言作为一种系统编程语言也不完全正确，所以 Ritchie 开发了一个新版本的 B 语言，称为 n B，来解决其中的一些问题。NB 作为一种语言并没有持续很长时间，因为两人决定他们只需要抛弃 b。从这些抛弃中诞生了 C 语言。

# 分号今天的状态

众所周知，分号已经成为许多语言中的语句终止符——C、C++、Java、C#和 JavaScript，这里仅举一些最流行的语言为例。JavaScript 是一个有趣的例子，因为最新版本的 JavaScript 允许您在大多数以换行符结尾的语句末尾省略分号。

并非所有的编程语言都使用分号作为语句终止符。Python 使用换行符作为语句结束符，Visual Basic 和 Visual Basic.NET 也是如此。Ruby 是另一种不这样使用分号的流行语言。

一种较新的语言 Go 也不使用分号作为语句结束符——尽管它使用分号作为语句分隔符。另一方面，另一种新的系统编程语言 Rust 使用分号作为语句终止符。

要查看哪些语言使用哪些字符作为语句终止符和语句分隔符的列表，请点击[这里的](https://en.wikipedia.org/wiki/Comparison_of_programming_languages_(syntax)#Statements)。

# ；//终止符；

显然，如果我们真的应该使用分号的话，对于如何最好地使用分号还没有达成共识。我的猜测是，随着编译器和解释器技术的改进，对诸如分号作为语句结束符这样的语法糖的需求将会减少——尽管它作为语句分隔符确实工作得很好。

如果你喜欢这篇文章，请继续关注，因为我很快就要写花括号的简史了。

感谢阅读。