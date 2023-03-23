# 用 Python 从 Google Sheets 中自动提取数据

> 原文：<https://betterprogramming.pub/automating-data-extractions-from-google-sheets-with-python-666a692d8ac2>

## 使用 Google Sheets API 将数据加载到 MySQL

![](img/323687ed60645e54ecb49f8568037a16.png)

照片由[丹尼尔·莱维斯·佩鲁西](https://unsplash.com/photos/Pp9qkEV_xPk)在 [Unsplash](https://unsplash.com/) 拍摄。

就像我们最近的文章[中关于用 Python 自动化电子邮件一样，今天我们想讨论一个容易自动化的常见任务。](https://medium.com/better-programming/how-to-automate-your-emails-with-python-386b4e2d5395)

我们将概述如何编写能够从 Google Sheets 导出数据并将其插入 MySQL 的 Python 脚本。

你什么时候会用这个？

一种有用的方法是，当你用 Google Forms 创建某种形式的表单，并希望每天晚上自动加载新数据时。这对于连接到 Google Forms 的 Google Sheets 来说效果最好，因为从理论上讲，Google Sheets 的模式不应该有任何改变。

这是很重要的一点。如果您想要从中提取数据的 Google Sheet 是不断变化的，那么您将需要开发一个更加复杂的系统。

![](img/5ff189abb874b5d28ea36fe4bf36814b.png)

只要您的数据模式没有太大的变化，那么自动化这项任务是非常容易的。这要感谢 [gspread](https://github.com/burnash/gspread) 库。这个库使得与各种 Google APIs 的交互变得非常简单。

所以，让我们开始自动化你的谷歌表摘录。

# 从 Google Sheets 中提取数据

首先，我们将构建一个直接从 Google Sheet 中提取数据的函数。

首先，我们需要设置一些东西:

*   我们需要设置我们的服务帐户凭据。为了做到这一点，我们需要去谷歌项目。
*   在那里，我们可以创建一个服务帐户，然后创建一个 JSON 文件。
*   我们还需要启用对 Google Sheets API 的访问。

有了所有的设置，现在我们可以开始提取。

为了做到这一点，我们将使用名为`[open_by_url](https://gspread.readthedocs.io/en/latest/user-guide.html)`的函数。这将使我们能够访问准确的电子表格，如下面的代码所示:

通过我们的`GetSpreadsheetData`函数，我们将从 Google Sheet 返回数据。本质上，我们得到的是数组的数组。

所以看起来是这样的:

```
#Array of arrays
x = [
["username1","2020-01-01","A","B","D","C"],
["username2","2020-01-01","A","B","D","C"],
["username3","2020-01-01","A","B","D","C"]]
```

这一点很重要，因为我们正在操作这个数据集，并努力将其插入 MySQL。

# 插入 MySQL 并保留空值

现在我们有了一个用 Python 从 Google Sheet 返回数据的函数，我们现在可以将它插入到 MySQL 中。

在此之前，我们还创建了一个函数来替换 Google 工作表中从`‘’`到`None`的所有空值。

这是一个重要的区别，可能会导致重大问题。我们不会深入细节，但长话短说，`Null`不等于`‘’`。

在这种情况下，我们创建了函数`preserveNullValues`。这使用了一个嵌套循环来查找所有的`‘’`。

从技术上来说，我们也可以用类似这样的方式写得更紧凑:

```
 ['None' if v is None else v for v in d]
```

但是我们发现这经常会令人困惑，所以我们想确保对这个函数的作用有一个清晰的理解。

此外，我们创建了一个名为`WriteToMysqlTable`的插入 MySQL 的函数。

首先，我们有一个单独的文件，包含密码、主机、用户名和数据库名。利用这一点，我们将创建一个连接。

该连接将用于运行`insert`语句。

在这种情况下，脚本对 SQL insert 进行了硬编码。然而，我们可能应该用一个配置来设置它，作为一个变量传递，或者只是动态创建。

同样重要的是要注意，一旦我们运行了`insert`语句，我们将需要用 MySQL 调用`cursor.commit()`来确保数据确实被插入了。

这样，我们就差不多完成了。现在我们只需要把所有这些功能放在一起。

# 整理您的 Python 脚本

好了，现在我们有了所有这些函数，我们需要把所有的东西放在一起。

如果您查看下面的代码，我们提供了 Google 工作表的 URL，因此您可以动态地将它用于您希望从中提取数据的任何 Google 工作表。

一旦数据被提取，我们就可以运行`preserveNullValues`，然后运行`WriteToMysqlTable`。

就是这样！

实际上，大部分工作都是通过 gspread 库完成的。一旦数据被提取出来并放在一个数组中，移动数据就非常容易了。

这就是 Python 的伟大之处。你需要做的大部分事情都存在于某个地方的某个图书馆里。

# 是时候开始自动化你的谷歌表摘录了

我们希望这个脚本能帮助你用 Python 自动化你的 Google Sheet extracts，并寻找你可以自动化的其他任务。

在上面的例子中，我们手动安排这个脚本。然而，如果你想在将来更有效地自动化，你可以使用一个类似于气流的框架。然后，您可以每天运行该脚本，而不是手动运行该脚本。

有些人[想开发自己的自动化系统](https://www.youtube.com/watch?v=8bUBGXlcBJo&t=2s)。个人不建议这样。有很多很好的框架可以帮助你管理自动化任务。通常花更多的时间在流程上比构建一个系统要好。

无论你决定如何自动化，我们祝你好运，自动化快乐！