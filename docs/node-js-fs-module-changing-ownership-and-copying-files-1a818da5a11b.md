# Node.js fs 模块—更改所有权和复制文件

> 原文：<https://betterprogramming.pub/node-js-fs-module-changing-ownership-and-copying-files-1a818da5a11b>

## 我们如何操作节点中的文件和目录？

![](img/0940df0a29602573638db85fbd7e51a5.png)

奥利弗·黑尔在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

操作文件和目录是任何程序的基本操作。因为 Node.js 是一个服务器端平台，可以直接与运行它的计算机交互，所以能够操作文件是一个基本特性。

幸运的是，Node.js 的库中内置了一个`fs`模块。它有许多功能，可以帮助操纵文件和文件夹。支持的文件和目录操作包括基本的操作，如操作和打开目录中的文件。

同样，它也可以对文件做同样的事情。它可以同步和异步地做到这一点。它有一个异步 API，该 API 具有支持承诺的功能。

此外，它还可以显示文件的统计数据。几乎所有我们能想到的文件操作都可以用内置的`fs`模块来完成。

在本文中，我们将使用`chown`函数改变文件的所有权，并使用`copyFile`函数复制文件。

# 使用 fs.chown 和 fs.chownSync 函数更改文件所有权

`chown`函数让我们异步改变文件的所有权。它需要四个参数。

1.  第一个参数是 path 对象，它可以是字符串、`Buffer`对象或 URL 对象的形式。
2.  第二个参数是 UID，它是用户的用户 ID。
3.  第三个参数是 GID，它是组 ID。
4.  第四个参数是一个回调函数，它有一个`err`参数，如果`chown`操作成功，这个参数就是`null`，否则`err`将是一个错误对象。`chown`操作完成后，无论成功与否，回调函数都会被调用。

要使用`chown`函数，我们可以编写如下代码:

在上面的例子中，我们将文件`file.txt`的所有权更改为 UID 为 1000 的用户和 GID 为 1000 的组。为了找出 Linux 系统中当前用户和用户组的 UID 和 GID，我们可以使用`id`命令。

`chown`函数有一个名为`chownSync`的同步版本。它需要三个参数。

1.  第一个参数是 path 对象，它可以是字符串、`Buffer`对象或 URL 对象的形式。
2.  第二个参数是 UID，它是用户的用户 ID。
3.  第三个参数是 GID，它是组 ID。它返回`undefined`。

我们可以在下面的代码中使用它:

```
const fs = require("fs");
const file = "./files/file.txt";fs.chownSync(file, 1000, 1000);
console.log("File ownership changed");
```

它和`chown`做同样的事情，但是同步进行。如果我们只想按顺序执行`chown`操作，我们不必使用`chown`，更好的选择是使用`chown`函数的 promise 版本。

`chown`函数的 promise 版本有三个参数。

1.  第一个参数是 path 对象，它可以是字符串、`Buffer`对象或 URL 对象的形式。
2.  第二个参数是 UID，它是用户的用户 ID。
3.  第三个参数是 GID，它是组 ID。当`chown`操作成功完成时，它返回一个没有参数的承诺。

我们可以在下面的代码中使用它:

为了确认`chown`如我们预期的那样工作，我们可以在 Linux 系统中运行以下命令。一种方法是运行`ls -l`，如下面的命令所示:

```
ls -l ./files/file.txt
```

我们也可以使用`stat`命令。

例如，我们可以运行`stat ./files/file.txt`从`Uid`和`Gid`输出中获取`./files/file.txt`的所有权信息。使用`stat`命令，我们应该得到如下所示的输出:

```
File: './files/file.txt'
  Size: 16              Blocks: 0          IO Block: 512    regular file
Device: eh/14d  Inode: 22799473115106242  Links: 1
Access: (0555/-r-xr-xr-x)  Uid: ( 1000/hauyeung)   Gid: ( 1000/hauyeung)
Access: 2019-11-02 12:26:47.996290200 -0700
Modify: 2019-11-02 12:26:47.996290200 -0700
Change: 2019-11-02 12:44:45.037581600 -0700
 Birth: -
```

此外，我们可以过滤掉我们不需要的信息，只获得带有`"%U %G"`格式序列的 UID 和 GID。例如，我们可以运行`stat -c “%U %G” ./files/file.txt`来获取`file.txt`的 UID 和 GID。

![](img/90e4adf5df08a25ce37bb1e048544419.png)

由[路易斯·汉瑟](https://unsplash.com/@louishansel?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 用 fs.copyFile 和 fs.copyFileSync 复制文件

要复制 Node.js 程序中的文件，我们可以使用`copyFile`函数。`copyfile`函数有四个参数。

1.  第一个参数是源文件的路径，可以是字符串、`Buffer`对象或 URL 对象。
2.  第二个参数是目标文件的路径，也可以是字符串、`Buffer`对象或 URL 对象。
3.  第三个参数是一个数字标志参数，它指定复制操作的行为。该标志的默认值为 0。

该标志可以是下列值之一或它们的按位组合:

*   `fs.constants.COPYFILE_EXCL` —如果目标文件已经存在，复制操作将失败。
*   `fs.constants.COPYFILE_FICLONE` —复制操作将尝试创建写入时复制引用链接。写入时复制意味着如果文件被复制但没有被修改，那么它将只引用原始文件。只有当我们第一次写入文件时，它才会进行实际的复制。如果平台不支持写入时复制，则使用回退复制机制。
*   `fs.constants.COPYFILE_FICLONE_FORCE` —复制操作将尝试创建写入时复制引用链接。如果平台不支持写入时复制，则操作将失败。

我们可以将上面的常量与按位的`OR`运算符结合起来，例如`fs.constants.COPYFILE_EXCL | fs.constants.COPYFILE_FICLONE`。

4.第四个参数是文件复制操作完成时调用的回调函数。它有一个`err`参数，当复制文件操作成功时为`null`，否则为一个带有错误信息的对象。

复制操作的原子性没有保证。如果在打开目标文件进行写入后出现错误，Node.js 将尝试删除目标。

默认情况下，如果没有设置标志，目标文件将覆盖同一位置同名的现有文件(如果存在)。

要使用`copyFile`函数，我们可以编写类似下面的代码:

在上面的代码中，我们将源文件的源设置为`file.txt`，目标文件设置为`fileCopy.txt`。

此外，我们指定了如果文件以`fs.constants.COPYFILE_EXCL`常量存在，复制操作将会失败，并且我们指定了复制文件操作将通过带有`fs.constants.COPYFILE_FICLONE`标志的写时复制方法来完成。

`copyFile`功能的同步版本是`copyFileSync`功能。除了回调之外，它采用与`copyFile`函数相同的参数。

1.  第一个参数是源文件的路径，可以是字符串、`Buffer`对象或 URL 对象。
2.  第二个参数是目标文件的路径，也可以是字符串、`Buffer`对象或 URL 对象。
3.  第三个参数是一个数字标志参数，它指定复制操作的行为。该标志的默认值为 0。这些标志和`copyFile`函数的标志是一样的，我们也可以用同样的方式组合它们。

复制操作的原子性没有保证。如果在打开目标文件进行写入后出现错误，Node.js 将尝试删除目标。

默认情况下，如果没有设置标志，目标文件将覆盖同一位置同名的现有文件(如果存在)。

要使用`copyFileSync`函数，我们可以编写一些代码，如下例所示:

在上面的代码中，我们设置源文件的来源为`file.txt`，目标文件为`fileCopy.txt`。

此外，我们指定了如果文件以`fs.constants.COPYFILE_EXCL`常量存在，复制操作将会失败，并且我们指定了复制文件操作将通过带有`fs.constants.COPYFILE_FICLONE`标志的写时复制方法来完成。

我们通过用`try...catch`块包围代码来捕捉任何抛出的异常。

还有一个`copyFile`函数的承诺版本。它将文件从源异步复制到目标。

1.  第一个参数是源文件的路径，可以是字符串、`Buffer`对象或 URL 对象。
2.  第二个参数是目标文件的路径，它也可以是字符串、`Buffer`对象或 URL 对象。
3.  第三个参数是一个数字标志参数，它指定复制操作的行为。该标志的默认值为 0。这些标志与`copyFile`函数相同，我们可以用同样的方式组合它们。

复制操作的原子性没有保证。如果在打开目标文件进行写入后出现错误，Node.js 将尝试删除目标。

默认情况下，如果没有设置标志，目标文件将覆盖同一位置同名的现有文件(如果存在)。

如果复制操作成功，则`copyFile`的 promise 版本解析时不带任何参数。

我们可以在下面的代码中使用`copyFile`的承诺:

在上面的代码中，我们将源文件的源设置为`file.txt`，目标文件设置为`fileCopy.txt`。

此外，我们指定了如果文件以`fs.constants.COPYFILE_EXCL`常量存在，复制操作将会失败，并且我们指定复制文件操作将通过带有`fs.constants.COPYFILE_FICLONE`标志的写时复制方法来完成。

我们通过用`try...catch`块包围代码来捕捉任何抛出的异常。

# 结论

通过`chown`功能，我们可以改变文件的所有权。函数有两个异步版本，一个是常规异步版本，一个是 promise 版本。

还有一个同步版本的`chown`函数，也就是`chownSync`函数。它们都通过传入您选择的用户的 UID 和 GID 来更改所有者。UID 是用户 ID，GID 是组 ID。我们可以用`copyFile`功能复制文件。

`copyFile`函数有两个异步版本，一个是常规异步版本，一个是 promise 版本。

还有一个同步版本的`copyFile`函数，叫做`copyFileSync`函数。我们可以用在`fs`模块中作为常量的标志改变文件的复制方式。