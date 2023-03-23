# 如何编写 Python 脚本来创建和更新 Changelog

> 原文：<https://betterprogramming.pub/how-to-write-a-python-script-to-create-and-update-a-changelog-c2d39615064b>

## 不再有生成器、日志或依赖——现在是 Python！

![](img/b200f619811df959b38240aa47ebf4c0.png)

照片由 [Javier Allegue Barros](https://unsplash.com/@soymeraki?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

如果你读了我的[上一篇关于如何编写 bash 脚本来创建和更新 changelog 的文章](/how-to-write-a-bash-script-to-create-and-update-a-changelog-de4df98985ab)，你可能也会对这篇文章感兴趣。我正在为工作编写一些 python 脚本，这时我想到一些 Python 开发者可能会喜欢用 Python 编写一个 changelog 脚本。

现在他们知道了！

我添加了 [Python 支持](https://github.com/macro6461/changelog-python)来扩展我的[原始 changelog 脚本](https://github.com/macro6461/changelog-bash)给偏爱 Python 的工程师。现在，您可以对 Python 代码使用相同的脚本逻辑。如果你是一个 python 开发者，并且想要在你的 changelog 创建/生成中添加更多的定制，你现在可以更容易地完成这些。

由于和我之前的脚本是一个逻辑，所以在功能上我就不太深入了。相反，我将强调一些句法差异。让我们直接跳进来吧！

首先，创建一个`changelog.py`文件来运行脚本的逻辑。您可以通过运行`chmod 755 changelog.py`使该文件可执行。这样你就可以从命令行运行`./changelog.py`,或者在另一个脚本中添加`./changelog.py`来包含它。

接下来，我们需要我们的项目版本与我们的 changelog 同步。下面是我们如何在 Bash 中获得版本。

```
version="$(git describe --long)"
```

在 Python 中，您可以使用下面的代码获得相同的结果。

```
version=subprocess.check_output([“git”, “describe”, “ — long”]).strip()
```

在代码的其他部分，您将需要使用字符串插值。Python 中的做法与 Bash 中略有不同。Bash 字符串插值见下文。

```
name="Matthew Croak"
introduction="My name is $name"echo $introduction> "My name is Matthew"
```

这是 Python 的字符串插值。

```
name="Matthew Croak"
introduction="My name is {}".format(name)print(introduction)> "My name is Matthew"
```

字符串插值对脚本很重要，因为我们将从提供的字符串写入和重写 changelog 文件。这些字符串将包含更新的信息，例如今天的日期和包的版本。

接下来，让我们编写我们的`init`函数。这个函数将被用来决定我们是否想要创建一个新的 changelog 或者简单地更新一个现有的 changelog。在 bash 中，像这样检查文件是否存在。

```
if test -f "$changelog" ; then
  new_changelog_item
else
  new_changelog
fi
```

在 Python 中，我们需要从`os.path`导入`exists`函数。

要导入这个函数，只需将`from os.path import exists`放在`changelog.py`文件的顶部。一旦我们导入了它，我们可以用下面的代码检查 CHANGELOG.md。

```
if exists('CHANGELOG.md'):
  new_changelog_item()
else:
  new_changelog()
```

接下来，让我们回顾一下如何读写我们的 changelog 文件。在 Bash 中，您可以这样读取文件:

```
while read line; do           
  # code performed for each line
done < CHANGELOG.md
```

在 Python 中，我们使用内置的`open`和`readlines`函数。

```
changelog = open('CHANGELOG.md', 'r')
changelog_items = changelog.readlines()for line in changelog_items:
  # code performed for each line 
```

关于如何在 Python 中读取文件的更多信息，请查看本文。

除了需要读取我们的文件，我们还需要*更新*我们的 changelog 文件。

在 Bash 中，这是使用`[sed](https://www.geeksforgeeks.org/sed-command-in-linux-unix-with-examples/)`完成的。

```
while read line; do
  if [[ $line == "## [Unreleased]"* ]]; then
    **sed -i 's/old_text/new_text/' CHANGELOG.md**
done < CHANGELOG.md
```

在 Python 中，我们可以利用内置函数`open`和`writelines`。

```
changelog = open('CHANGELOG.md', 'w')
changelog_items = changelog.writelines(new_lines)
```

你注意到当我们使用`open`读取文件时，我们如何使用`"r"`，当我们使用`open`将写入文件时，我们如何使用`"w"`。下面是我们使用 Python 更新 changelog 中特定行的剩余代码。

```
index = -1
changelog_items=read_changelog()
for line in changelog_items:
  index+=1
  if "## [Unreleased]" in line:
    changelog_items[index] = "## [Unreleased]\n{}\n### Added\n- ADD CHANGE HERE!\n".format(item)
    write_changelog_lines(changelog_items)
    break
```

我将读/写代码分解到它们自己的函数中，以使代码更具可读性。下面是`read_changelog`的代码。

```
def read_changelog():
  changelog = open('CHANGELOG.md', 'r')
  changelog_items = changelog.readlines()
  return changelog_items
```

这里是`write_changelog_lines`的代码。

```
def write_changelog_lines(changelog_items):
  with open('CHANGELOG.md', 'w') as changelog:
    changelog.writelines(changelog_items)
```

现在你知道了！添加了 python 对纯*脚本的支持，以合并到您的应用程序中来生成变更日志，添加变更日志项目。它还在格式和变更日志内容方面为您提供了灵活性，而不需要生成器、日志或其他依赖项。完整代码在 [GitHub](https://github.com/macro6461/changelog-python/blob/main/changelog.py) 上。*

[***升级您的免费 Medium 会员资格***](https://matt-croak.medium.com/membership) *并接收各种出版物上数千名作家的无限量、无广告的故事。这是一个附属链接，你的会员资格的一部分帮助我为我创造的内容获得奖励。*

*您还可以通过电子邮件* [***进行订阅***](https://matt-croak.medium.com/subscribe) *每当我发布新内容时，您都会收到通知！*

*谢谢！*

# 参考

[](/how-to-write-a-bash-script-to-create-and-update-a-changelog-de4df98985ab) [## 如何编写 Bash 脚本来创建和更新 Changelog

### 不再有生成器、日志或依赖项

better 编程. pub](/how-to-write-a-bash-script-to-create-and-update-a-changelog-de4df98985ab) [](https://github.com/macro6461/changelog-python) [## GitHub-macro 6461/changelog-python:生成和更新 changelog 的 Python 脚本

### 这是创建和更新 changelog 的纯 python 脚本的代码。不需要发电机。不需要…

github.com](https://github.com/macro6461/changelog-python) [](https://www.geeksforgeeks.org/read-a-file-line-by-line-in-python/) [## 在 Python - GeeksforGeeks 中逐行读取文件

### 先决条件:Python 提供了创建、写入和读取文件的内置函数。有两种类型的文件…

www.geeksforgeeks.org](https://www.geeksforgeeks.org/read-a-file-line-by-line-in-python/) [](https://www.geeksforgeeks.org/sed-command-in-linux-unix-with-examples/) [## Linux/Unix 中的 Sed 命令及示例

### UNIX 中的 SED 命令代表流编辑器，它可以对文件执行许多功能，如搜索、查找和…

www.geeksforgeeks.org](https://www.geeksforgeeks.org/sed-command-in-linux-unix-with-examples/) [](https://github.com/macro6461/changelog-python/blob/main/changelog.py) [## 主宏中的 changelog-python/changelog . py 6461/changelog-python

### 此文件包含双向 Unicode 文本，其解释或编译可能与下面显示的不同…

github.com](https://github.com/macro6461/changelog-python/blob/main/changelog.py)