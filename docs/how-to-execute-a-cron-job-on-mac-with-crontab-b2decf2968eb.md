# 如何使用 Crontab 在 Mac 上执行 Cron 作业

> 原文：<https://betterprogramming.pub/how-to-execute-a-cron-job-on-mac-with-crontab-b2decf2968eb>

## 使用 crontab 在 Mac 上使用 Python 自动执行任务

![](img/abb644c4162e37dc6d7e9ba59f52d669.png)

照片由[布拉德在](https://unsplash.com/@bradneathery?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) [Unsplash](https://unsplash.com/s/photos/clock?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上整理

# 序言

在我写下面的步骤之前，我建议安装最新版本的 Python 和 Homebrew。 [Homebrew](https://brew.sh/) 是一个包管理器，就像 [pip](https://pip.pypa.io/en/stable/) 和 [Anaconda](https://www.anaconda.com/) 一样，坦率地说，我只是在这个例子中使用它，因为我花了几个小时试图运行一个简单的 cron 作业，但没有成功。

我经常收到错误，比如无法打开我想要执行的文件或者找不到安装的 Python 版本。我不会在这篇文章中讨论我的错误，但是如果你愿意，你可以自由地使用任何其他 Python 安装。

我卸载了我已经安装的 Python 3.x 版本(通过 pip 和 Conda ),并从头开始——卸载也是一个麻烦。

所以，让我们假设你在你的 Mac 上，有默认的 Python 发行版，我相信应该是 2.7.10。几乎没有开发人员在这个版本中更新模块，所以当你构建项目时，最好安装最新版本的 Python。

我按照[真正的 Python 指南](https://realpython.com/installing-python/#step-2-install-homebrew-part-2) 通过自制软件安装 Python 3(真的很快几步)，所以我假设你在这篇文章中也做了同样的事情。

# 步伐

在 Mac 上打开终端命令提示符，通过运行`cd ~/`导航到主目录。对我来说，就是`Users/Nakul`。

我们将使用 Mac OS 的内置 crontab 功能来编写我们的 cron 作业。

键入`crontab -e`并按回车键。

这将打开一个空文件，这是您编写 cron 作业的地方。在这种情况下，您可以编写作业来运行 shell 脚本或 Python 脚本。

键入`:q!`退出编辑器。

假设你遵循了上面的自制指南，仔细检查你的 Python 安装路径。你可以通过在终端输入`which python3`来实现。

它应该显示`/usr/local/bin/python3`(这是实际的自制软件安装位置的别名，但没关系，我们现在不会深入讨论)。您将在您的 cron 作业语法中包含这个路径，我们将对此进行讨论。

在编写 cron 作业之前，我们应该有一个想要运行的脚本。我已经在我的主目录中创建了一个目录`/Documents/Python/cron`，并创建了一个名为`cron_test.py`的简单脚本。

注意:这个 Python 脚本需要是可执行的，所以要修改它的权限，我刚刚运行了一个`chmod 777 cron_test.py`。

`cron_test.py`脚本简单地创建了一个以当前日期和时间为名称的目录。

如果您想要使用相同的脚本，脚本文本如下:

您可能想知道第一行`#!/usr/bin/python3`是什么。这一行被称为 *shebang* 以及 cron 作业将如何解释脚本，因此在这种情况下，它将使用 Python 运行`cron_test.py`(就像您在终端中执行 Python `cron_test.py`)。

好了，我们已经安装了 Python 3.x，我们有了 Python 脚本，现在让我们编写 cron 作业来运行它！

假设我们想每分钟都运行这个脚本(主要是为了在您的终端进行测试，这样您就可以不用等太久就能看到结果)。

cron 作业语法如下:

```
*/1 * * * * cd ~/Documents/Python/cron && /usr/local/bin/python3 cron_test.py >> ~/Documents/Python/cron/cron.txt 2>&1
```

以上是什么意思？我们来快速分析一下。

`*/1 * * * *`简单地说就是作业每分钟都会运行。

`cd ~/Documents/Python/cron && /usr/local/bin/python3`导航到您想要执行的脚本所在的目录，并指定使用 Python 而不是 [Bash](https://www.gnu.org/software/bash/) 来执行(因为它是一个 Python 脚本，还记得我们在脚本中添加了 shebang，我发现它需要在两个地方都工作)。

`cron_test.py`是脚本文件名。

`>> ~/Documents/Python/cron/cron.txt`指定当作业执行出现问题时，将日志输出到何处。

`2>&1`简单地禁用电子邮件，因为默认情况下，cron 作业会尝试发送电子邮件，但我们没有指定地址。

无论如何，这应该足够让你编写和测试你的脚本了，所以继续吧！

请随意浏览网页，了解更多关于 cron jobs 和 crontab 的信息。我决定为那些对编程有基本了解并希望尽快编写 cron 作业的人写这篇文章。

希望这有所帮助！