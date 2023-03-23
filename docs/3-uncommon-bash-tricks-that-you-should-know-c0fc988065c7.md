# 你应该知道的 3 个不常见的 Bash 技巧

> 原文：<https://betterprogramming.pub/3-uncommon-bash-tricks-that-you-should-know-c0fc988065c7>

## 使用这些未充分利用的 Bash 模式，在终端上输入更少的内容

![](img/9b8c734ee157f363e38a0b97c471a7ea.png)

作者图片—意大利马泰拉

**优秀的开发人员打字更少**——允许他们:

*   工作更快
*   工作更精确
*   少犯错误
*   减轻他们疲惫双手的压力

减少输入的一个方法是正确使用 shell(通常是 Bash)。这就是这篇文章的内容——三个 Bash 技巧，让你少打字。

这篇文章中提到的三个 Bash 技巧是:

1.  **带`{a,b}`的参数扩展**——避免重新键入单个命令
2.  **使用`$_`访问最后一个参数**——避免从最后一个命令重新输入
3.  **用`^old^new`快速替换**——快速改变最后一个命令的一部分

所有这些技巧也都与 zsh 兼容。

*这篇文章遵循了用* `$`启动 shell 命令的惯例。*对于将被 shell 扩展的命令，我将在下面显示不带*T4 的扩展命令。

# 用`{a,b}`进行参数扩展

在编写单个命令时，重复自己是很常见的。

以改变一个文件的后缀为例，我们可以使用`mv`来完成:

```
$ mv README.txt README.md
```

注意我们如何写两次`README`？

参数扩展将避免这种重复——允许我们更改文件的后缀，而无需两次键入`README`:

```
$ mv README.{txt,md}
mv README.txt README.md
```

我们使用的参数展开是`{txt,md}`，展开成两个实参— `txt md`(中间用空格隔开)。

**参数扩展为花括号内的每个元素创建一个参数，用逗号分隔**:

```
$ echo {1,2,3}
1 2 3$  echo pre{1,2,3}fix
pre1fix pre2fix pre3fix
```

空条目将创建一个没有替代内容的参数:

```
$ echo pre{,1,2}fix
prefix pre1fix pre2fix
```

另一个例子——将`models`文件夹重命名为`data`文件夹中的`ml`:

```
$ mv data/models data/ml
```

我们可以通过使用参数扩展来节省重新键入`data/`:

```
$ mv data/{models,ml}
mv data/models data/ml
```

我们可以将参数扩展用于一系列数字，这对创建编号目录很有用:

```
$ mkdir data{0..2}
mkdir data0 data1 data2
```

**我们还可以在参数**中进行参数扩展——例如，在路径中途更改文件夹:

```
$ cat models/{baseline,final}/data.csv
cat models/baseline/data.csv models/final/data.csv
```

最后一个例子，使用三个参数——将两个 Python 测试文件移动到“tests”文件夹中:

```
$ mv tests{_unit.py,_system.py,}
mv tests_unit.py tests_system.py tests
```

## 摘要

任何时候，当你在一个命令中多次重新输入某个内容时，参数扩展都有可能帮助你拯救疲惫的双手。

# 使用`$_`访问最后一个参数

终端由一系列命令操作——我们经常在多个命令中重用信息。

我们之前的技巧，参数扩展，是关于在单个命令上输入更少的内容— **这个技巧是关于在多个命令上输入更少的内容**。

举一个简单的例子，制作一个文件夹并移入其中:

```
$ mkdir temp
$ cd temp
```

注意，我们在第二个命令中再次重用了参数`temp`？

**我们可以保存重新输入的** `temp` **，并使用** `$_`将其从之前的命令中提前:

```
$ mkdir temp
$ cd $_
cd temp
```

上面，我们使用`$_`来访问前一个命令的最后一个参数，在本例中是`temp`。

想要重用最后一个命令的最后一个参数(这里是`temp`)的用例非常常见，以至于 Bash 将它存储在一个特殊的变量`_`中，我们使用一个`$`前缀来访问这个变量(与`$PATH`或`$HOME`相同)。

使用`$_`的另一个例子——使用`cat`移动文件并打印到`STDOUT`:

```
$ mv main.py src/main.py 
$ cat src/main.py
```

注意我们再次重用了最后一个参数`src/main.py`？

您可以使用`$_`重写该命令，以自动将`src/main.py`提前到您的第二个命令中:

```
$ mv main.py src/main.py 
$ cat $_
cat src/main.py
```

使用`$_`意味着你不需要重写一个复杂的文件路径，让你没有机会不正确地重新键入它。

## 摘要

*任何时候，当你通过多个命令多次重新输入某个东西时，使用* `$_` *可能有助于减轻你疲惫双手的压力。*

# 用`^old^new`快速替换

有时(通常在我们的例子中),我们在 Shell 中运行一个命令，然后很快意识到我们犯了一个错误。

我们可以使用**快速替换，通过替换之前命令中的文本来修复错误，而不是再次重新键入命令。**

举个例子——你登录一个服务器，运行命令进行连接——却发现应该是`user`而不是`ubuntu`!

```
$ ssh [ubuntu@198.compute.com](mailto:ubuntu@198.compute.com)
```

不用重新输入整个命令，您可以使用快速替换来更改您想要的部分——这里将`ubuntu`更改为`user`:

```
$ ^ubuntu^user
ssh [user@198.compute.com](mailto:user@198.compute.com)
```

快速替换中的模式是`^old^new`。这相当于做:

```
$ !!:s/old/new
```

`!!`得到最后一个命令，`:s`得到一个替换的正则表达式。我想你会同意`^old^new`更容易一点！

## 摘要

*任何时候，当你通过多条命令多次重新输入某个东西时，使用* `$_` *可能有助于减轻你疲惫双手的压力。*

感谢阅读！