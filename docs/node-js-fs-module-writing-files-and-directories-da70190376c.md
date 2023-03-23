# Node.js 的“fs”模块:编写文件和目录

> 原文：<https://betterprogramming.pub/node-js-fs-module-writing-files-and-directories-da70190376c>

## 将文件系统引入您的应用程序

![](img/34a3b9a07f6485c7e4cb724ad8b87a95.png)

迈克尔·贾斯蒙德在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

操作文件和目录是任何程序的基本操作。因为 Node.js 是一个服务器端平台，可以直接与运行它的计算机交互，所以操作文件的能力是一个基本特性。

幸运的是，Node.js 的库中内置了一个`fs`模块。它有许多功能，可以帮助操纵文件和文件夹。它支持基本的文件和目录操作，比如操作和打开目录中的文件。

它可以对文件做同样的事情。它可以同步和异步地做到这一点。它有一个支持承诺的异步 API 函数。此外，它还可以显示文件的统计数据。

几乎所有我们能想到的文件操作都可以用内置的`fs`模块来完成。在这篇文章中，我们将使用`fs`模块中的函数来写文件，使用`write`系列函数来写文件。

# fs。写

write 函数有两个版本，一个用于将文本写入磁盘，另一个用于将二进制数据写入磁盘。

`write`函数的文本版本让我们可以异步地将文本写到磁盘上。这需要一些争论。

第一个参数是文件描述符——一个标识文件的数字。

第二个参数是写入文件的字符串。如果传入的值不是字符串，则将其转换为字符串。

第三个参数是文件开始写入的位置。如果传入的值不是数字，那么它从当前位置开始。

第四个参数是一个字符串，该字符串具有要写入的文件的字符编码，默认为`utf8`。最后一个参数是带有三个参数的回调函数。

第一个是有错误对象的`err`对象，如果有错误，它不是`null`。

第二个参数是`written`参数，一个整数，指定有多少字节被写入文件系统。不一定和写的字符串字符数一样。

第三个参数是`string`参数，它包含写入的字符串。

在 Linux 上，位置写在追加模型中不起作用。在 Windows 上，如果文件描述符是 1，代表标准输出，那么默认情况下，包含非 ASCII 字符的字符串将不会正确呈现。

要使用`write`函数，我们可以先使用`open`函数来获取您想要写入的文件的文件描述符，然后我们可以通过将文件描述符传递给`write`函数来写入文件。例如，如果我们想写入路径为`./files/file.txt`的文件，我们可以这样写:

```
const fs = require("fs");fs.open("./files/file.txt", "r+", (err, fd) => {
  if (err) throw err;
  fs.write(fd, "abc", 0, "utf8", (err, written, string) => {
    console.log(err, written, string);
    fs.close(fd, err => {
      if (err) throw err;
    });
  });
});
```

当我们运行上面的代码时，我们应该得到如下所示的输出:

```
null 3 abc
```

在上面的代码中，我们首先用`open`函数打开文件。我们传入了`r+`标志，这样我们就可以写入文件。然后，我们在回调函数中获得文件描述符`fd` ，并将其传递给`open`函数。有了`fd`文件描述符，我们可以将它传递给`write`函数。

在`write`函数的第二个参数中，我们指定要将字符串`abc`写入文件。在第三个参数中，我们指定我们想把它写在位置 0，第四个参数指定字符串的字符编码应该是 UTF-8。

最后一个参数中的回调将得到写操作的结果。从那里，我们从输出中知道三个字节和字符串`‘abc’` 被写入文件。

除了`r+`标志，还有许多其他可能的系统标志，包括:

*   `'a'` —打开一个追加文件，这意味着将数据添加到现有文件中。如果文件不存在，则创建该文件。
*   `'ax'` —与`'a'`相似，但如果路径存在，则会引发异常。
*   `'a+'` —打开文件进行读取和追加。如果文件不存在，则创建该文件。
*   `'ax+'` —与`'a+'`相似，但如果路径存在，则会引发异常。
*   `'as'` —以同步模式打开附加文件。如果文件不存在，则创建该文件。
*   `'as+'` —以同步模式打开文件进行读取和追加。如果文件不存在，则创建该文件。
*   `'r'` —打开文件进行阅读。如果文件不存在，将引发异常。
*   `'r+'` —打开文件进行读写。如果文件不存在，将引发异常。
*   `'rs+'` —以同步模式打开文件进行读写。
*   `'w'` —打开文件进行写入。文件被创建(如果不存在)或覆盖(如果存在)。
*   `'wx'` —与`'w'`类似，但如果路径存在则失败。
*   `'w+'` —打开文件进行读写。文件被创建(如果不存在)或覆盖(如果存在)。
*   `'wx+'` —类似于`'w+'`，但是如果路径存在，就会抛出异常。

`write`函数的二进制版本允许我们异步地将文本写到磁盘上。这需要一些争论。

第一个参数是文件描述符，它是一个标识文件的数字。

第二个参数是 buffer 对象，它可以是 Buffer、TypedArray 或 DataView 类型。

第三个参数是`offset`，它决定了要写入的缓冲区部分。

第四个参数是`length`参数，它指定写入的字节数，最后一个参数是`position`，它是一个整数，描述了`write`函数开始写入的位置。

最后一个参数是一个回调函数——一个接受`err`参数的函数，它有一个 error 对象。如果发生错误，第二个是`bytesWritten`参数，它获取写入磁盘的字节数，第三个是`buffer`对象，它包含写入磁盘的二进制数据。

例如，我们可以在下面的代码中使用它:

```
const fs = require("fs");fs.open("./files/binaryFile", "w", (err, fd) => {
  if (err) throw err;
  fs.write(fd, new Int8Array(8), 0, 8, 0, (err, bytesWritten, buffer) => {
    console.log(err, bytesWritten, buffer);
    fs.close(fd, err => {
      if (err) throw err;
    });
  });
});
```

如果运行它，我们会得到以下输出:

```
null 8 Int8Array [
  0, 0, 0, 0,
  0, 0, 0, 0
]
```

# fs.writeSync

`write`功能的同步版本是`writeSync`功能。有一个写二进制数据的版本和一个写文本数据的版本。

`writeSync`函数的文本版本让我们可以异步地将文本写到磁盘上。这需要一些争论。

第一个参数是文件描述符，它是一个标识文件的数字。

第二个参数是将被写入文件的字符串。如果传入的值不是字符串，它将被转换为字符串。

第三个参数是文件开始写入的位置。如果传入的值不是数字，那么它从当前位置开始。

第四个参数是一个字符串，它包含要写入的文件的字符编码，默认为`utf8`。返回写入的字节数。

我们可以使用文本版本的`writeSync`函数，如下面的代码所示:

```
const fs = require("fs");const fd = fs.openSync("./files/file.txt", "r+");
const numBytesWritten = fs.writeSync(fd, "abc", 0, "utf8");
console.log(numBytesWritten);
```

我们应该在`console.log`语句中得到 3，因为写了 3 个字节。

`writeSync`函数的二进制数据版本有五个参数。它将文件描述符作为第一个参数。第二个参数是`buffer`对象，可以是 Buffer、TypedArray 或 DataView 对象。

第三个参数是`offset`，它是一个整数，指定了`writeSync`函数将开始写入的缓冲区部分。

第四个参数是`length`参数，它指定了写入的字节数。

最后一个参数是`position`，它是一个整数，描述了`writeSync`函数开始写入的位置。返回写入的字节数。

我们可以使用二进制版本的`writeSync`函数，如下所示:

```
const fs = require("fs");const fd = fs.openSync("./files/binaryFile", "w");
const numBytesWritten = fs.writeSync(fd, new Int8Array(8), 0, 8, 0);
console.log(numBytesWritten);
```

我们应该在`console.log`语句中得到 8，因为写入了 8 个字节。

![](img/6f651fefb4662f39ac327e1a67e8a24c.png)

照片由[莱奥莱奥](https://unsplash.com/@leo_leo?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# fs.writeFile

我们可以用`fs.writeFile`函数以一种不太复杂的方式编写文件。它需要四个参数。

第一个参数是对文件的引用，它可以是字符串路径、缓冲区对象、URL 对象或文件描述符，后者是标识文件的整数。

第二个参数是要写入文件的数据，可以是 string、Buffer 对象、TypedArray 或 DataView 对象。第三个参数用于传入带有几个选项的对象。

有三件事可以设置:编码、模式和标志:

*   编码指定了正在写入的文本的字符编码，默认为`utf8`。
*   模式是一个整数，默认为`0o666`。该模式设置文件权限和粘滞位，但仅在文件已经创建的情况下。`0o666`表示既可读又可写。
*   flag 选项指定正在写入的文件的读取或写入选项。上面列出的对于这个论点都是有效的。最后一个参数是回调函数，这个函数接受一个`err`对象，如果存在错误，这个对象不是`null`。

例如，如果我们想用`writeFile`函数写入路径为`./files/file.txt`的文件，我们可以这样写:

```
const fs = require("fs");fs.writeFile(
  "./files/file.txt",
  "abc",
  { encoding: "utf8", mode: 666, flag: "w" },
  err => console.log(err)
);
```

在第一个参数中，我们传入文件的字符串路径。然后，在第二个参数中，我们传入了要写的内容。第三个参数包含编码选项，使用`mode`选项写入文件的文件权限，以及用于设置文件打开模式的`flag`。如果文件写入成功。上面的`console.log`语句应该输出`null`，并且您可以看到您写到磁盘上的文件的内容。

当一个文件描述符被传入`writeFile`函数的第一个参数时，其行为与传入其他对象来引用该文件略有不同。如果将文件描述符传递给第一个参数，该文件不会被替换。

这意味着如果用文件描述符多次调用`writeFile`函数，那么写入文件的项将被追加到文件中，而不是覆盖现有的内容。因此，如果第一个`writeFile`调用传入了文件描述符`1`和内容`a`，第二个`writeFile`调用传入了文件描述符`1`和内容`b`，那么我们在文件描述符为`1`的文件中得到`ab`。

# fs.writeFileSync

有一个名为`writeFileSync`的`writeFile`同步版本，它采用与`writeFile`函数相同的参数，只是没有回调。它返回`undefined`。我们可以在下面的函数中使用它:

```
const fs = require("fs");fs.writeFileSync("./files/file.txt", "abc", {
  encoding: "utf8",
  mode: 666,
  flag: "w"
});
```

运行上面的代码后，您应该看到以给定路径写入磁盘的文件中的内容。

操作文件和目录是任何程序的基本操作。因为 Node.js 是一个服务器端平台，可以直接与运行它的计算机交互，所以能够操作文件是一个基本特性。幸运的是，Node.js 的库中内置了一个`fs`模块。

它有许多功能，可以帮助操纵文件和文件夹。在这段代码中，我们使用了`fs`模块中的函数来编写文件，使用了`write`系列函数来编写文件。我们使用`open`和`write`函数首先打开文件以获取文件描述符，然后将内容写入文件。它有单独的版本来处理文本和二进制文件。

还有一个名为`writeSync`的函数的同步版本，它接受相同的参数，没有回调。它还有一个文本和二进制版本。为了更方便地写入文件，我们可以使用`writeFile`和`writeFileSync`函数，在给定对文件的引用(如路径字符串或 URL 对象)的情况下，可以写入文本和二进制文件。`writeFile`为异步，`writeFileSync`为同步。