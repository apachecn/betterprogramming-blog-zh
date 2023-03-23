# 如何用 Python 编写自己的密码生成器

> 原文：<https://betterprogramming.pub/how-to-write-your-own-password-generator-in-python-2511e633cf53>

## 随机单词。随机数。随机特殊字符

![](img/91624a85045191676270f1f066f2e805.png)

照片由[米哈伊尔·尼洛夫](https://www.pexels.com/@mikhail-nilov?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)从[派克斯](https://www.pexels.com/photo/light-fashion-man-people-6964165/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)拍摄

你不是一直想生成“stormy-Friday-777-%”这样的密码吗？没有吗？不管怎样，我们都要做。坐下来，享受编码一个密码生成器，我们将使用英语词典中的单词，一些数字技巧，和良好的旧连接。

在这篇文章中，我们将创建一个密码生成器，如果你坚持到最后，我们将看看三种不同的方法来生成基于英语词典的单词。

1.  从 GitHub 下载文本文件
2.  使用网页抓取
3.  使用“随机单词”模块

额外的收获是，你会学到你从未听过的单词。我保证！

虽然有很多密码生成器，但我认为这将是一个巨大的 Python 挑战，所以我开始创建自己的密码生成器。

在本文的大部分内容中，我们都在使用 Python 3.10。让我们开始吧！

# 简短的

简单来说就是创建一个密码生成器，它会返回一个密码供您以任何方式使用。我们将打印出这个程序的密码，这样我们就可以看到发生了什么，但是您可以使用这个代码来扩展为几种情况创建密码的想法。

```
Create a tool that generates a password with the following formatting:word1-word2-word...-number-specialcharacterexample:
stormy-Friday-007-%The use of special character is optional.
The generator is based on the English language.
```

一些网站要求使用至少一个特殊字符，所以我们将实现它作为娱乐。

特殊字符包括:

`!”#$%&’()*+,-./:;<=>?@[\]^_`{|}~`

# 寻找一些词源

我对搜索网站上的单词并不感兴趣，所以我找了一个文本文件。我不是历史上第一个需要这个的人，谢天谢地 GitHub 上已经有一个文本文件了。这里是[链接](https://github.com/dwyl/english-words)。

他们从`[infochimps](https://web.archive.org/web/20131118073324/http://www.infochimps.com/datasets/word-list-350000-simple-english-words-excel-readable)`中取出 excel 文件，并将其提取到可读性更好的文本文件中进行编码。这是我们将在生成器的第一次迭代中使用的。然后我们将使用其他方法进行调整。

# 工具编码

现在我们已经做了一些背景研究，我们可以开始代码本身的工作。

我通常为我的项目写伪代码，这样我就可以让思考过程继续下去。我已经想好了我们在简报中想要什么，所以我需要确保我们钉所有的要求。

我还想把大部分需求拆分成各自的功能，这样后期更新更容易。

记住为你的函数和变量起一个好听的名字。如果你被公共汽车撞了，有人可以接管是件好事。

```
#store words in a list#generate random words#generate random numbers#generate random special characters#generate password
```

# 单词，我们需要很多单词

让我们从最明显的开始。如果我们想要随机的单词，我们需要一种方法来生成它们。

如果您决定从 infochimps 下载文本文件，您需要打开该文件并将每一行放入一个列表中。这将是一个庞大的名单，但我们可以处理。将文本文件放在硬盘上，使用您喜欢的打开和读取方法在代码中找到它。我就用`with open`。

有几种方法可以遍历单词列表中的单词。在我们的例子中，我们将把整个文件存储在内存中，并从我们创建的列表中选择一个元素。如果您的文件很大，这可能不是一个可行的解决方案。

以下是一些想法的草图:

```
word_file = '/Users/martinandersson/Documents/projects/python/password_generator/source/words.txt'with open(word_file,'r') as file:
    word_list = list(file)print(word_list[0:10]) #check if the list conversion works.
```

您可能注意到了使用 list()函数将整个文件立即转换为列表的激进方法。不遍历文件中的每一行的一个缺点是，不能直接去掉`\n`(换行符)。然而，当我们使用列表中的元素时，我们可以做到这一点。

经过一些重构后，我们会得到这样的东西:

```
def get_words(word_path):
    word_file = word_path
    with open(word_file,'r') as file:
        word_list = list(file) #creates a list incl. \n(ewline)
    return word_listdef main():
    words = get_words('/Users/martinandersson/Documents/projects/python/password_generator/source/words.txt')
    print(words[0:5]) #test listif __name__ == '__main__':
    main()
```

我们在这里并没有做太多，只是确保我们创建了一个函数来获取单词，所以我们返回列表。通过这种方式，我们可以将一个文件通过管道传输到一个函数中，而不是依赖于更硬编码的方法，即直接将路径发送到代码中。如果你愿意，你也可以用`argv[1]`扩展这个想法。

执行上面的代码将打印:

```
['2\n', '1080\n', '&c\n', '10-point\n', '10th\n']
```

因为这些是列表中从 0 到 5 的项目。我们在`main()`中要求这样。因为我们将整个文本文件放入一个列表中，所以我们还没有删除`\` n。

如果您希望遍历所有行，去掉换行符，然后列出一个列表，您可以使用`readlines()`。

```
def another_text_read(filename):
    with open(filename) as file:
        word_list = file.readlines()
        word_list = [word.rstrip() for word in word_list]
    return word_list
```

这运行起来有点慢，但它将完成工作。

# 最终代码

以下是该项目的最终代码:

在这一节中，我将逐一分解所有的过程和函数，这样就很容易理解这里发生了什么。

```
import random
```

Random 将用于生成数字和列表索引。因此，我们需要在攻击其余模块之前导入这个模块。

```
def get_words(word_path)
```

这是密码生成器的核心。我们需要单词，我们需要真正的英语单词，而不是胡言乱语。我们不是在编写一个乱码生成器。

正如我们已经讨论过的，从英语中获取单词有几种方法。在我的最终代码中，我保留了列表方法的文本文件。你可以用你觉得最好的。

需要`word_path`来告诉脚本在哪里寻找 word 文件。在我的例子中，当我们执行这个函数时，我们告诉脚本它在哪里。您还可以通过使用`argv[1]`并在启动脚本时指向磁盘上的一个文件来扩展它。

这个函数获取整个文本文件，并在返回这个列表之前将它转换成一个列表。这样做的一个缺点是你不能马上去掉`\n`这个角色。不过，当我们得到将要使用的单词时，我们会在代码的后面处理这个问题。

```
def generate_random_words(word_source, word_count)
```

这个函数首先创建一个空单词列表。为了填充这个列表，我们生成一个随机索引，并按照`word_count`的指示选择尽可能多的单词。单词 source 是我们从前面的函数中创建的列表。然后我们将它添加到空列表中来填充它。

当我们完成填充后，我们返回随机单词的列表。这一次，`\n`被去掉了，我们在列表中有了格式正确的单词。

```
def generate_special_char(amount)
```

该函数返回一个特殊字符串，字符串的长度由`amount`参数决定。

```
def generate_random_number(padding)
```

该函数基于填充生成一个随机数。这段代码不处理整数的原因是我们希望能够有一些前导零，比如 007。

使用字符串连接数字也很有效，因为我们不会立即生成具有正确填充的数字。如果我们基于`randint(0,padding)`创建一个整数，我们将不能一次得到 007。现在我们可以了。

密码最后也会是一串——双赢。

```
def generate_password(all_words, word_count, separator, padding=2, special = True, special_amount=1)
```

这个函数基于许多参数生成我们的整个密码。

*   `all_words`是所有可用单词的列表。
*   `word_count`告诉我们要用多少单词。
*   `separator`告诉我们分离器会是什么样子
*   `padding`告诉我们生成的数字应该有多少个数
*   `special`告诉我们是否在密码末尾添加了特殊字符。
*   `special_amount`告诉我们需要多少个特殊字符

该函数将使用其他函数来正确创建密码。

一些事情可能会突出。比如，为什么它通过创建一个名为`new_password`的变量并转换从`generate_random_words()`返回的列表来开始整个事情？

因为我们知道格式以单词开始，然后是数字，最后是特殊字符，所以我们可以用单词列表开始整个密码。无论我们决定在密码中添加什么，我们只需添加到这个列表中。

当我们在密码列表中添加完我们想要的内容后，我们使用 join 将列表中的所有元素用所选的分隔符连接起来。

```
def main()
```

`main()`通过使用磁盘上文件的路径调用`get_words`函数开始。然后它使用`generate_password()`功能打印生成的密码。

我们在这里打印它的唯一原因是为了获得密码的可视化表示。在真实的用例场景中，您最有可能返回密码，并将其发送到您需要发送它的任何地方。

# 获得随机单词的更多方法

上面的代码使用了我们从 GitHub 获得的文本文件。如前所述，我们现在将研究两种获取所有英语单词的替代方法。

因为我们非常聪明，为`get_words()`创建了一个函数，我们所要做的就是替换它，并确保它返回一个列表，以便工具的其余部分工作。

## 使用 urllib 进行网页抓取

如果你想删除并且不使用这些文本，你可以使用字典的在线版本。

对于代码，您需要导入`requests`或`urllib`。我们将使用`urllib`，因为它是内置的。

如果您使用的是 Mac，并且出现认证错误，请尝试以下操作:

## **错误**

```
urllib.error.URLError: <urlopen error [SSL: CERTIFICATE_VERIFY_FAILED] certificate verify failed: unable to get local issuer certificate (_ssl.c:997)>
```

## **解决方案**

运行以下命令后安装证书:

```
/Applications/Python 3.10/Install Certificates.command
```

为了调整我们的代码以支持使用`urllib`，我们需要做以下事情:

```
from urllib.request import urlopendef get_words(website):
    with urlopen(website) as word_list:
        text_file = word_list.read().decode("utf-8").splitlines()

        return text_file
```

首先，我们从`urllib.request`导入`urlopen`。这允许我们传递一个 web url 并加载内容。在我们的例子中，是一个文本文件。

特别注意`.decode(“utf-8”)`部分。虽然`text_file`是一个列表，但是它的元素不是。它们是字节。

因此，我们需要将它解码成我们喜欢的字符编码，在我们的例子中是`utf-8`。如果看到前面有 b 的元素(`b’ninja’`)，就需要解码。

就像之前一样，我们返回列表，以便在脚本中稍后使用。

我们的主要程序也将略有变化:

```
def main():
    words = get_words('[http://www.mieliestronk.com/corncob_lowercase.txt'](http://www.mieliestronk.com/corncob_lowercase.txt'))
    print(generate_password(words,2,'_',5,True,2))
```

# 随机词模块

注意:不支持 Python3.10。使用 python3.6 — python3.9

人类的美妙之处在于，当他们遇到其他人可以受益的东西时，他们会制作 python 模块。除了其他事情。

要安装随机字模块，只需打开一个终端并键入`pip3 install random-word`。

这将安装该模块，因此您可以轻松生成随机单词。如果您想了解更多关于该模块的信息，请看[文档](https://pypi.org/project/Random-Word/)。

让我们开始替换`generate_random_words()`函数中的代码，以便与`random-word`模块一起工作。这次，我们根本不需要`get_word()`功能。

```
from random_word import RandomWordsdef generate_random_words(word_count):
    word_container = RandomWords()
    word_list = word_container.get_random_words(limit=word_count)return word_listprint(generate_random_words(2))
```

该代码将生成一个包含两个随机单词的列表，就像`[‘transmuter’, ‘cacophonous’]`一样，我在这里，认为英语不是胡言乱语。该代码不适用。

random-word 有一个名为`hasDictionaryDef(string)`的标志，如果你不喜欢的话。

# 最后的想法

这是一个超级有趣的挑战，在生成随机单词时，最明显的选择是使用`random-word`模块。虽然 3.10 还不支持它，但希望你们中的大多数人还没有疯到可以上最新版本。

这个项目背后的想法是练习逻辑思维和解决问题。我还想把我们需要的东西整理成几个功能。这种方式很容易在我们需要的时候修改代码，测试三种不同的方式来做一件事并不会破坏代码的其余部分。这是所有项目中需要记住的事情。一个函数不应该重载并做太多事情。通过一个论点，吐出一个清单，或类似的东西。

我希望你喜欢这个进步，如果你有更好的方法，请让我知道，这样我可以学到一些新的东西。

## 扩展计划理念

如果你想扩展这个程序，你可以用它来为新用户生成密码。比方说，您正在为新员工或用户的计算机设置电子邮件帐户。他们需要一个启动密码。

您也可以创建一个菜单，用户可以在其中回答一些问题，比如多少单词，数字上的填充等。这样，您可以根据用户的需要获取密码生成器函数的所有参数。这将带走我们这里的一些硬编码元素。

谢谢你坚持到最后。