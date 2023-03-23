# Node.js 中文件系统模块的初学者指南

> 原文：<https://betterprogramming.pub/a-beginners-guide-to-the-file-system-module-in-node-js-4743c0f3dab>

## 了解如何使用 Node.js 中的“fs”模块

![](img/c8ff471d43f1ef7ec86eae92bd9c401d.png)

[Sigmund](https://unsplash.com/@sigmund?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照。

文件系统模块，或简称为`fs`，允许您访问机器上的文件系统并与之交互。

使用`fs`模块，您可以执行如下操作:

*   创建文件和目录。
*   修改文件和目录。
*   删除文件和目录。
*   读取文件和目录的内容。

本文将教你最常用、最有用的`fs`方法。事不宜迟，我们来看看那些方法是什么。

# 如何使用 fs

文件系统模块是一个核心 [Node.js](https://nodejs.org/en/) 模块。这意味着您不必安装它。您唯一要做的事情就是将`fs`模块导入到您的文件中。

因此，在文件的顶部添加以下行:

```
const fs = require('fs');
```

现在您可以通过使用前缀`fs`从文件系统模块中调用任何方法。

或者，您可以从`fs` API 中只导入您想要的方法，如下所示:

```
const { writeFile, readFile } = require('fs');
```

然而，在本教程中，您将看到使用的第一个选项——导入整个`fs`模块。

## 警告

对于本教程，您还需要导入`path`模块。它是另一个核心 Node.js 模块，允许您处理文件和目录路径。

在导入`fs`模块后，在您的文件中添加以下行:

```
const path = require('path');
```

`path`模块是*而不是强制*与文件系统模块一起工作。但是，有帮助！

# 同步与异步

值得注意的是，默认情况下，所有的`fs`方法都是异步的。但是，您可以通过在方法末尾添加`Sync`来使用同步版本。

例如，像`writeFile`这样的方法变成了`writeFileSync`。同步方法同步完成代码，因此它们阻塞了主线程。阻塞 Node.js 中的主线程被认为是不好的做法。

因此，本教程使用文件系统模块中的异步方法。

# 写入文件

你将学习的第一件事是如何写一个文件。要从 Node.js 应用程序写入文件，可以使用`writeFile`方法。

`writeFile`方法至少接受以下参数:

*   文件的名称
*   内容
*   回调

如果指定的文件已经存在，它将使用您作为参数提供的内容替换旧内容。如果指定的文件不存在，它将创建一个新文件。

导入`fs`和`path`模块后，在应用程序中编写以下代码:

上面的代码创建了一个名为`content.txt`的新文件，并添加了文本`This is my first file!`作为内容。如果有错误，回调函数会抛出一个错误。否则，它向控制台输出文件创建成功。

假设您将文件命名为`fsPractice.js`，进入终端并运行命令`node fsPractice.js`。运行该命令后，您应该会看到包含指定内容的新文件。

`writeFile`还有其他变体，例如:

*   `fs.writeFileSync` —同步写入文件。
*   `fsPromises.writeFile` —使用基于承诺的 API 写入文件。

看看这个要点，看看你的应用程序在这一点上应该是什么样子。

# 从文件中读取

在读取文件之前，您需要创建并存储文件的路径。这就是`path`模块派上用场的地方。

使用来自`path`模块的`join`方法，您可以创建如下文件路径:

```
const filePath = path.join(process.cwd(), 'content.txt');
```

第一个参数`process.cwd()`返回当前工作目录。现在您已经有了文件路径，您可以读取文件的内容了。

在文件中编写以下代码:

```
fs.readFile(filePath, (error, content) => {
  if (error) throw error;

  process.stdout.write(content);
});
```

`readFile`方法至少有两个参数:

*   文件的路径
*   回调

如果有错误，它抛出一个错误。否则，它在终端中输出文件内容。

如果您进入终端并运行命令`node fsPractice.js`，您应该在终端中看到文件内容。

`readFile`还有其他变体，例如:

*   `fs.readFileSync` —同步写入文件。
*   `fsPromises.readFile` —使用基于承诺的 API 写入文件。

看看[这个要点](https://gist.github.com/catalinpit/badc2a539a44412892a0e05a9575d54d)，看看你的应用程序在这一点上应该是什么样子。

# 读取目录的内容

显示目录中的文件与读取文件内容非常相似。但是您传递的不是文件路径，而是当前工作目录(您可以传递任何其他目录)。

之后，您传递一个回调函数来处理响应。在文件中编写以下代码:

```
fs.readdir(process.cwd(), (error, files) => {
  if (error) throw error;

  console.log(files);
});
```

到目前为止，您只使用了`process.stdout.write`向终端输出内容。然而，您可以像上面的代码片段一样简单地使用`console.log`。

如果您运行这个应用程序，您应该得到一个包含您的目录中所有文件的数组。

看看这个要点，看看你的应用程序在这一点上应该是什么样子。

# 删除文件

文件系统模块有一个允许您删除文件的方法。然而，重要的是要注意它只对文件有效，对目录无效。

当你用文件路径作为参数调用`unlink`方法时，它会删除文件。将以下代码片段添加到您的文件中:

```
fs.unlink(filePath, (error) => {
  if (error) throw error;

  console.log('File deleted!')
});
```

如果您重新运行代码，您的文件将被删除！

看看这个要点，看看你的应用程序在这一点上应该是什么样子。

# 创建目录

您可以使用`mkdir`方法异步创建一个目录。在您的文件中编写以下代码:

```
fs.mkdir(`${process.cwd()}/myFolder/secondFolder`, { recursive: true }, (err) => {
  if (err) throw err;

  console.log('Folder created successfully!');
});
```

让我们一步一步来。首先，您想要在当前工作目录中创建一个新文件夹。如前所述，您可以使用`process`对象中的`cwd()`方法获取当前工作目录。

之后，传递想要创建的一个或多个文件夹。但是，这并不意味着您必须在当前工作目录中创建新文件夹。你可以在任何地方创建它们。

现在，第二个参数是递归选项。如果不将其设置为`true`，则无法创建多个文件夹。如果将`recursive`选项设置为`false`，上面的代码会给出一个错误。试试看！

但是，如果您只想创建一个文件夹，则不需要将`recursive`选项设置为`true`。

下面的代码会工作得很好！

```
fs.mkdir(`${process.cwd()}/myFolder`, (err) => {
  if (err) throw err;

  console.log('Folder created successfully!');
});
```

因此，我想强调一下`recursive`的用法。当您想要在文件夹内创建文件夹时，需要将其设置为`true`。它将创建所有文件夹，即使它们不存在。

另一方面，如果您只想创建一个文件夹，您可以将其保留为`false`。

看看这个要点，看看你的应用程序在这一点上应该是什么样子。

# 删除目录

删除目录的逻辑类似于创建目录的逻辑。如果您查看您编写的创建目录的代码和下面的代码，您会发现相似之处。

因此，在您的文件中编写以下代码:

```
fs.rmdir(`${process.cwd()}/myFolder/`, { recursive: true }, (err) => {
  if (err) throw err;

  console.log('Folder/s deleted successfully!');
});
```

您使用文件系统模块中的`rmdir`方法，并传递以下参数:

*   要删除的目录
*   `recursive`房产
*   回调

如果您将`recursive`属性设置为`true`，它将删除文件夹及其内容。需要注意的是，如果文件夹里面有内容，需要设置为`true`。否则，您会得到一个错误。

以下代码片段仅在文件夹为空时有效:

```
fs.rmdir(`${process.cwd()}/myFolder/`, (err) => {
  if (err) throw err;

  console.log('Folder/s deleted successfully!');
});
```

如果您在`myFolder`中有其他文件和/或文件夹，如果您没有通过`{ recursive: true }`，您会得到一个错误。

知道什么时候使用`recursive`选项，什么时候不使用，以避免问题，这一点很重要。

看看这个要点，看看你的应用程序在这一点上应该是什么样子。

# 重新命名

使用`fs`模块，您可以重命名目录和文件。下面的代码片段展示了如何使用`rename`方法来实现这一点:

您可以看到`rename`方法有三个参数:

1.  第一个参数是现有的文件夹/文件。
2.  第二个参数是新名称。
3.  回调。

因此，要重命名文件或目录，需要传递当前文件/目录的名称和新名称。运行应用程序后，应该会更新目录/文件的名称。

需要注意的是，如果新路径(例如文件/文件夹的新名称)已经存在，它将被覆盖。因此，请确保不要错误地覆盖现有的文件/文件夹。

看看这个要点，看看你的应用程序在这一点上应该是什么样子。

# 向文件添加内容

使用文件系统模块，您还可以向现有文件添加新内容。`appendFile`方法允许您这样做。

如果你比较一下`writeFile`和`appendFile`的方法，你会发现它们是相似的。您传递文件路径、内容和一个回调。

```
fs.appendFile(filePath, '\nNew data to be added!', (err) => {
  if (err) throw err;

  console.log('New content added!');
});
```

上面的代码片段演示了如何向现有文件添加新内容。如果您运行应用程序并打开您的文件，您应该会看到里面的新内容。

看看[这个要点](https://gist.github.com/catalinpit/7c8d40db53dea9e6831f9ee89d92475c)，看看你的应用程序在这一点上应该是什么样子。

# 结论

如果你正在读这篇文章，这意味着你现在可以:

*   创建文件和目录。
*   修改文件和目录。
*   删除文件和目录。
*   读取文件和目录的内容。

干得好！然而，文件系统模块更复杂，我鼓励您查看[官方文档](https://nodejs.org/docs/latest-v16.x/api/fs.html)。在官方文档中，你可以看到所有的方法。

YouTube 版本

你想学习如何编码吗？查看这 20 个 [*学习如何编写*](https://catalins.tech/20-best-places-to-learn-programming-for-free) *代码的最佳地点。*

*如果你想学习 JavaScript，看看这 5 个* [*最好的 JavaScript 资源*](https://catalins.tech/5-best-resources-to-learn-javascript-as-a-beginner) *。*

*下面列举一个带有* [*编程项目思路*](https://catalins.tech/10-programming-project-ideas-for-beginners) *的清单，供初学者参考。*

*检查这些*[*JavaScript es 2020*](https://catalins.tech/javascript-es2020-the-features-you-should-know)*特性。*