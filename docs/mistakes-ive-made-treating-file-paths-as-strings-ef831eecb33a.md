# 我把文件路径当作字符串时犯的错误

> 原文：<https://betterprogramming.pub/mistakes-ive-made-treating-file-paths-as-strings-ef831eecb33a>

## 作为开发人员，您做的一些事情可能会工作数年，但却在您最意想不到的时候反过来伤害您

![](img/eb1dd5a5eb318c58b9d5cf93189869b2.png)

这些是你希望另一个开发人员在你的职业生涯早期告诉你的事情，这样你就不会犯同样的错误。这篇文章就是关于这些事情中的一件，如果你正在读这篇文章，就当是我对你的警告。

文件路径看起来像字符串。您有许多目录，可能还有一个以扩展名结尾的文件名。您用一个`/`字符分隔目录和文件，结果看起来像`/path/to/file`。因此，您可以像对待字符串一样对待它们，将它们连接或串联起来，直到您将它们传递给另一个用于读取或写入文件的 file 方法。这些是我几个月前的想法。这就是我错的地方。

# 不要忘记 Windows

如果你在 Mac(就像我有幸做的那样)或 Linux 上开发，那么你可能已经阅读了上面的段落并且没有注意到任何错误。如果你在 Windows 上开发，当你阅读`/`角色时，你可能会对着你的咖啡叹气。

当你在 Mac 上工作并部署到 Linux 环境时，很容易忘记——就像我多年来所做的那样——Windows 使用反斜杠。当您使用需要在这两种平台上运行的命令行工具时，发现自己犯了这样的错误是非常痛苦的。 [create-twilio-function](https://github.com/twilio-labs/create-twilio-function) 就是这样一个命令行工具，它必须经历[个](https://github.com/twilio-labs/create-twilio-function/commit/af3031dcd5947a2abb735f7769bcd8fdb7e1aa73) [的变化](https://github.com/twilio-labs/create-twilio-function/commit/fa281c1fce15db0915a8b403c4d19b9b2422da99)，比如:

```
mkdir(path + '/' + dirName);
```

收件人:

```
const path = require('path');
mkdir(path.join(pathName, dirName));
```

以便它能在 Windows 上正常工作。

对于 Windows 用户，我很抱歉。对于其他人来说，当使用 Node.js 时，`[path](https://nodejs.org/api/path.html)`模块是你的朋友。每当你必须连接两条路径时，使用`[path.join](https://nodejs.org/api/path.html#path_path_join_paths)`。并查看其他实用程序，如`[path.relative](https://nodejs.org/api/path.html#path_path_relative_from_to)`(返回从一条路径到另一条路径的相对路径)和`[path.normalize](https://nodejs.org/api/path.html#path_path_normalize_path)`(返回路径解析段，如`.`或`..`)。

不要理会`[path.sep](https://nodejs.org/api/path.html#path_path_sep)`，它会根据您正在使用的系统返回一个`/`或一个`\`。用`path.join`就行了。

# 路径的行为与字符串不同

现在是我的第二个错误，这次是在 Ruby 中工作。这个稍微微妙一些，躲过了我的测试。您看，您可以使用`Pathname`类来创建路径片段，然后将它们连接起来。例如:

```
require "pathname"
path1 = Pathname.new("path")
path2 = Pathname.new("to")
path1 + path2
# => #<Pathname:path/to>
```

如您所见，`Pathname`对象有一个`+`操作符来连接路径，就像`+`连接字符串一样。事实上，它还可以混合使用字符串和路径:

```
require "pathname"
path1 = Pathname.new("path")
path2 = "to"
path1 + path2
# => #<Pathname:path/to>
```

这一切似乎都很好，除了它不能反过来工作。

```
require "pathname"
path1 = "to"
path2 = Pathname.new("path")
path1 + path2
# => TypeError (no implicit conversion of Pathname into String)
```

像这样的小错误意味着我们做错了什么。不过，这不是我的问题。不，我的问题源于期望连接一个路径名和一个字符串，而不是连接两个字符串。这在[我的 RubyGem](https://github.com/philnash/jekyll-gzip/)中有所体现。你看，我试着用下面这条线创建一个路径球:

```
files = Dir.glob(dir + "**/*{#{extensions}}")
```

原来在某些情况下，`dir`实际上是一个字符串而不是路径名，并且它不包含分隔符。因此，当我真正想让它寻找`"dirname/**/*{#{extensions}}"`时，它却在寻找`"dirname**/*{#{extensions}}"`。连接两个路径名或者一个路径名和一个字符串会增加分隔符([正如有人在我的 commit 评论中指出的](https://github.com/philnash/jekyll-gzip/commit/6651b7f51b62cd14a3e256d77fa604a49eacb9d8#diff-392aaa6a279f62e98df890fff8d82d1eL54-R54))，但是连接两个字符串不会。这意味着 gem 愉快地去寻找错误的路径名，没有找到文件，然后继续成功地什么也不做。将整行替换为:

```
files = Dir.glob(File.join(dir, "**", "*{#{extensions}}"))
```

已修复问题。在这种情况下，`[File.join](https://ruby-doc.org/core-2.6.2/File.html#method-c-join)`是用来避免字符串意外的方法。

# 始终使用内置路径池

无论您是使用 Node.js、Ruby 还是任何其他语言，都不要试图将文件路径视为字符串。它们在不同的平台上表现不同，将路径和字符串混合在一起会导致难以调试的错误。

使用您的标准库，为自己省去麻烦。