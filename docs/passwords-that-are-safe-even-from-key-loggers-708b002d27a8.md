# 我使用 Python 构建了一个密码生成器——它不会受到键盘记录器的攻击

> 原文：<https://betterprogramming.pub/passwords-that-are-safe-even-from-key-loggers-708b002d27a8>

## 了解我为什么构建它，以及您如何构建和定制它

![](img/34b5ec6d71d0e7424af602434a3364d9.png)

[Bermix 工作室](https://unsplash.com/@bermixstudio?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

我是偏执狂。我不想与密码管理员共享我的密码。我不想把它们写下来或打在任何地方。

我甚至不想亲眼看到它们，永远。

我不想记住他们。我希望每个人都有独特的 17 个字符，所有那些有趣的规则，至少有一个大写字母，一个小写字母，一个数字，一个特殊字符，等等。

我不希望键盘记录程序能够截获我的密码。我也不想丢失任何密码。

我要求的太多了吗？

## 啊哈！

最近，在研究 Python 编程思想时，我又一次灵光一现“啊哈！”瞬间，本文中的代码就诞生了。是的，它符合上面提到的所有要求。让我解释一下。

# 一些背景

首先，在我开始解释这个程序之前，这里有一个你如何使用它的例子。

当系统提示您输入密码时，请将您所在网页的完整 URL 复制到剪贴板中。

运行`urlpassword.py`程序。它会活过来，向你要一个简短的个人密钥，然后很快消失，屏幕上除了你的密钥之外，没有任何密码或任何东西。最后，只需从剪贴板粘贴到密码字段，你就完成了。

# 它是如何工作的

几乎在所有情况下，密码对于您登录的域都是唯一的。

当你登录到某个地方时，在你的浏览器中查看 URL 或网址行，你会看到核心域地址前面有类似于`https://`的东西，后面是一些额外的数据，确切地定义了你在那个域中的位置。

比如你登录亚马逊，核心域名是`[www.amazon.com](http://www.amazon.com)`，对于谷歌是`[www.google.com](http://www.google.com)`等等。使用 Python，很容易从完整 URL 中提取核心域字符串，不管它是什么。但是域名只是用来生成你的唯一密码的一部分。

这个`urlpassword.py`程序是专门为你修改的。更改代码非常简单，我稍后将向您展示如何以及在哪里更改代码。

如果有人从你的电脑上获得你的程序，他们仍然需要知道你的密钥(银行密码是理想的，但任何易于输入和记忆的短字符串都是完美的)，否则他们将生成一个完全不同的密码。

![](img/118f3b8ac6e94ae44aeede8534556d94.png)

[分钟键](https://unsplash.com/@minutekey?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

类似地，一个坏人通过一个键盘记录程序窃取了你的钥匙，他仍然需要从你的电脑上获得这个程序的拷贝。破解你的密码需要两个部分。

# urlpassword.py 代码

这是这个程序的主要源代码。请注意，它导入了另外两个模块，我将在接下来解释这些模块。

```
import pyperclip
from prb import *url = pyperclip.paste()
domain = url.split("/")[2].lower()
key = input(f"{domain} ... Enter your key: ")
prb = Prb(domain + key)
a1 = "abcdefghijklmnopqrstuvwxyz"
a2 = a1.upper()
a3 = "1234567890"
a4 = "!#$%*^&@~"
a5 = a1 + a2 + a3 + a4
pw = []
pw.append(a1[prb.next_byte() % len(a1)])
pw.append(a2[prb.next_byte() % len(a2)])
pw.append(a3[prb.next_byte() % len(a3)])
pw.append(a4[prb.next_byte() % len(a4)])
n = 0
while len(pw) < 17:
    n += prb.next_byte(n)
    n %= len(a5)
    pw.insert(prb.next_byte() % (len(pw) + 1), a5[n])
pw = "".join(pw)
pyperclip.copy(pw)
```

URL 字符串是从系统剪贴板中获取的，在前几行中提取域。

下一行提示您输入您的密钥，域字符串和您的密钥的组合用于初始化`Prb`对象(伪随机字节)。

您可能希望在新密码中使用的所有字符都存储在字符串`a1`到`a4`中。

如果您想添加或删除任何字符，请在这里随意操作。列表`pw`是通过首先从这些字符串中的每一个添加一个字符来构建的。这保证了每组中至少有一个。

while 循环使用伪随机字节在随机位置将额外的字符插入到`pw`列表中，直到列表中有 17 个字符。

随意将`17`改成你想要的任意长度的密码。最后，这个列表被连接成一个单独的密码字符串，然后被塞进系统剪贴板，等待您粘贴。

# Pyperclip

注意`pyperclip`模块是由这个程序导入的。

一个简单且一次性的`pip install pyperclip`将这个库添加到您的 Python 安装中。

你可以在`pyperclip`的 [Pypi 页面这里](https://pypi.org/project/pyperclip/)了解更多关于`pyperclip`的信息。基本上，这增加了从剪贴板中抓取数据并将数据放回其中的能力。我在很多实用 Python 程序中一直使用它。

## 伪随机字节有多好？

`Prb`类也被导入到`prb.py`模块中。在我解释之前，下面是代码清单:

```
class Prb:
    def __init__(self, seed):
        id = "[J](https://craigware.com)ohnClarkCraig"
        unique = "NbROtW62oDiJH9y0*p20F1Bj2*bQyj&IcA"
        unique_seed = seed + id + unique
        len_unique_seed = len(unique_seed)
        self.p, self.q = 0, 0
        self.buf = list(range(256))
        for i in range(9973):
            n = ord(unique_seed[i % len_unique_seed])
            self.next_byte(n + self.next_byte()) def next_byte(self, b=0):
        self.p = (self.buf[self.p] + b + 1) % 256
        self.q = (self.buf[self.q] + b + 2) % 256
        self.buf[self.p], self.buf[self.q] = 
                self.buf[self.q], self.buf[self.p]
        return self.p ^ self.q
```

这个类维护一个名为`buf`的列表，包含从 0 到 255 的 256 个整数。变量`p`和`q`是这个列表的索引。

每次调用`next_byte()`时，列表项的索引和顺序都会根据可选的传递整数`b`进行随机化。

即使改变传递到这个方法中的数字 b 的一位，输出字节的序列也会完全改变。

# 专为您定制

回想一下，用于初始化这个伪随机字节生成器的种子字符串是从主程序传递过来的，它包含当前域和您的密钥。

这与上面显示的`id`字符串结合在一起，它明确地标识了您的唯一性，以及您想要通过名为 unique 的字符串加入的一组或多或少随机的字符。

然后，这个长的组合字符串被一遍又一遍地输入到`next_byte()`方法中，将生成器初始化为一个绝对唯一的状态。(可用的独特状态比已知宇宙中的电子还多。到目前为止。)

另外，我已经将`Prb`代码提交给了一些专家，他们说它非常好。

此外，在使用各种众所周知的伪随机字节测试程序进行了大量广泛的测试后，它已顺利通过。当然，它不是经过专业认证的加密算法，但对于个人密码保护来说，它非常有效，你可以相信它不会有任何后门或神秘的代码。

这个程序生成的密码对暴力攻击有很强的抵抗力。黑客需要得到你的程序的唯一拷贝，然后通过你可能选择的所有可能的快捷键进行暴力破解。

这在理论上是可能发生的，但是比较一下通过键盘记录器、在线密码管理程序攻击或其他你无法控制的邪恶事件攻击你的密码的可能性。

我建议通过更改`Prb`类中显示的字符串来定制您的程序副本，然后不要再更改它们。任何微小的改变都会导致你所有的密码都不一样。将程序的纸质副本保存在安全的地方，以防您出于任何原因需要恢复它。

```
**Want to Connect?**John’s passion and mission are sharing Python code to help demystify life’s challenges and to have fun. John is the author of [Python for Numworks](https://amzn.to/3k4O39j) , [Python for OpenSCAD](https://amzn.to/3mHK0Br), [Python for the TI-Nspire CX II](https://read.amazon.com/kp/embed?asin=B09DJ7MRDX&preview=newtab&linkCode=kpe&ref_=cm_sw_r_kb_dp_Z6AHQNW2MQBZHCNKC38G&tag=solarproud-20), [Python for the TI-84 Plus CE Python calculator](https://read.amazon.com/kp/embed?asin=B09GJMBDMF&preview=newtab&linkCode=kpe&ref_=cm_sw_r_kb_dp_Z6AHQNW2MQBZHCNKC38G&tag=solarproud-20) , and [many other titles](http://johnclarkcraig.com/).
```