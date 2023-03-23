# 如何用 Python 构建一个简单的 Magic 8 球

> 原文：<https://betterprogramming.pub/how-to-build-a-simple-magic-8-ball-in-python-29335954d9f2>

## 循序渐进的初学者指南

![](img/4dd25f38ad0d9f713b728b12496f8541.png)

照片:[丹尼斯·阿拉尔孔](https://www.flickr.com/photos/dalarcon/4237451273)

我们都知道什么是神奇的 8 号球，对吧？你问它一个问题，摇一摇它，然后阅读它的(通常是神秘的)反应。

用 Python 做一个也很容易。在本教程中，您将确切地了解如何操作，以及之后可以做的一些更改。您将学习一些 Python 技能，这些技能也可以很容易地在其他程序中使用。

# Python 入门

首先，如果你还没有安装 Python，可以从[Python 网站](https://www.python.org/downloads/)安装。对于本教程中的代码，您需要 3.6 或更高版本的 Python。

安装 Python 时，确保选中复选框将 Python 添加到`PATH`变量。如果你不这样做，程序一旦完成就很难运行了。

一旦你安装了 Python，在你的电脑上打开一个文本/代码编辑器。我更喜欢使用[括号](http://brackets.io/)或 [PyCharm](https://www.jetbrains.com/pycharm/) 。Windows 自带预装记事本，macOS 包含 TextEdit，Linux 用户可以使用 [Vim](https://www.vim.org/) 。

打开文本/代码编辑器，保存一个新文件。我会把它命名为`main.py`，但是你可以随意命名，只要它以`.py`结尾。

# 编码

现在您已经准备好了一个 Python 文件，我们需要导入`random`模块。这个模块将随机选择程序中的答案。

在 Python 中，[习惯上](https://www.python.org/dev/peps/pep-0008/#imports)将所有的导入放在每个文件的顶部，这很容易做到。用这一行开始你的程序:

```
import random
```

接下来，您需要为神奇的 8 号球添加一个可能的响应列表。这将是一个存储在名为`responses`的变量中的字符串列表。

```
responses = [
    "It is certain",
    "Without a doubt",
    "You may rely on it",
    "Yes definitely",
    "It is decidedly so",
    "As I see it, yes",
    "Most likely",
    "Yes",
    "Outlook good",
    "Signs point to yes",
    "Reply hazy try again",
    "Better not tell you now",
    "Ask again later",
    "Cannot predict now",
    "Concentrate and ask again",
    "Don't count on it",
    "Outlook not so good",
    "My sources say no",
    "Very doubtful",
    "My reply is no"
]
```

接下来，问用户一个问题。`input`函数返回用户输入的任何内容。由于这个神奇的 8 球是随机的，所以你根本不需要保存他们的问题。

```
input("Tell the Magic 8 Ball your question: ")
```

最后，您需要选择一个随机响应并显示给用户。

在本例中,`random.choice`函数从一个列表中选择一个随机项目，这个列表就是`responses`列表。然后，我们使用 f 字符串和`print`函数向用户显示答案。

```
answer = random.choice(responses)
print(f"The Magic 8 Ball says: {answer}")
```

# 运行您的程序

首先打开命令提示符(Windows/Linux)或终端(Mac)。尝试按顺序运行下面的每个命令。如果您正确安装了 Python，至少其中一个应该可以工作。

```
python main.py

py main.py

python3 main.py
```

确保用 Python 文件的完整路径替换`main.py`。

一旦你的程序开始运行，就开始玩吧！您可以通过按向上箭头键，然后按 Enter/Return 键来快速再次运行它。

这里有一个你的终端可能看起来像什么的例子。请记住，这是在 Windows 上，您的终端可能会有所不同。

```
$ python main.py
Tell the Magic 8 Ball your question: Will my life be filled with joy?
The Magic 8 Ball says: Signs point to yes
```

# 可能的变化

本教程中的代码是你的，你可以随意修改。这里有几个例子:

## 无尽生成

如果你想不断产生新的回应，你必须再次运行该程序。让程序无限循环运行是很容易的。

在询问用户问题之前，请输入以下代码:

```
while True:
```

然后，在新行之后缩进四个空格。

这是因为每次循环`while`中的代码运行时，它都会检查`True`是否等于`True`。这将永远是真的，所以循环将运行，直到它被手动停止。

尝试再次运行该程序。它会不停地提出问题，给出回应。

要中断循环，请按 CMD/CTRL + C，然后按 Enter/Return。您将看到一条以`KeyboardInterrupt`结尾的错误消息。然后，您将看到您的终端在等待另一个命令。

## 人生的意义是什么？

当用户问生命的意义是什么时，我们可以告诉他们`42`。你可以把这想象成一个隐藏的复活节彩蛋，送给外面的《银河系漫游指南》爱好者。

看一下这段代码，其中包含注释。Python 中的注释以`#`开始，一直延续到行尾。

```
# This will get the input, make it all lowercase, and save it in the 'question' variable.
question = input("Tell the Magic 8 Ball your question: ").lower()

# This will check if the words "meaning of life" are in the question.
if "meaning of life" in question:

    #This will respond with the meaning of life.
    print("The Magic 8 Ball says: 42")

else:

    # This will give a normal Magic 8 Ball response.
    answer = random.choice(responses)

    print(f"The Magic 8 Ball says: {answer}")
```

只需用这段代码替换普通的响应打印代码，就万事大吉了！

## 你还想要什么

用这段代码做你想做的事情！改变它的一部分，增加更多有趣的功能，或者删除一些东西。全靠你了！

# 资源

*   [下载 Python](https://www.python.org/downloads/)
*   [括号代码编辑器](http://brackets.io/)
*   [PyCharm IDE](https://www.jetbrains.com/pycharm/)
*   [Vim](https://www.vim.org/)
*   [完整代码](https://gist.github.com/bsoyka/e6afb4d6dc80740d705c409b1915f526)
*   [具有额外功能的完整代码](https://gist.github.com/bsoyka/8c2dfa9ae85a333fb4c05e3d212f4b05)