# 使用 Node.js 'fs '模块检查文件可访问性、追加数据和更改权限

> 原文：<https://betterprogramming.pub/node-js-fs-module-check-file-accessibility-appending-data-and-changing-permissions-dbce0f2b373c>

## 通过文件系统访问扩展您的应用

![](img/8e32bb6da5feedd124bbefb2b5fe8aaa.png)

照片由[拉杰什·德](https://unsplash.com/@rajesh_de?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

操作文件和目录是任何程序的基本操作。因为 Node.js 是一个服务器端平台，可以直接与运行它的计算机交互，所以能够操作文件是一个基本特性。

幸运的是，Node.js 的库中内置了一个`fs`模块。

它有许多功能，可以帮助操纵文件和文件夹。支持的文件和目录操作包括基本的操作，如操作和打开目录中的文件。

同样，它也可以对文件做同样的事情。它可以同步和异步地做到这一点。它有一个异步 API，该 API 具有支持承诺的功能。

此外，它还可以显示文件的统计数据。几乎所有我们能想到的文件操作都可以用内置的`fs`模块来完成。

在本文中，我们将使用`fs`模块中的函数来编写文件，并做其他事情，如检查文件的可访问性、添加文件和更改权限。

# 使用 fs.access 和 fs.accessSync 检查文件可访问性

我们可以使用`access`函数来检查文件的权限。它需要三个参数。

第一个参数是 path 对象，它是一个字符串、`Buffer`对象或 URL 对象。

第二个参数是 mode，它是一个整数，指定要检查哪种权限。整数存储为常数。可能的值包括`fs.constants.W_OK`、`fs_constants.R_OK`或`fs.constants.F_OK`。

默认值为`fs.constants.F_OK`。`fs.constants.F_OK`表示文件存在。`fs.constants.W_OK`意味着我们可以向它写入数据。`fs_constants.R_OK`表示我们可以阅读。

它们可以与位运算结合使用，以同时检查多个权限。例如，如果我们想检查一个文件是否可读或可写，我们可以写:`fs.constants.W_OK | fs_constants.R_OK`。

最后一个参数是一个回调函数，它使用一个`err`参数来显示错误(如果存在的话),如果正在检查的权限不允许用于文件，就会设置这个参数。`err`是`null`否则。

要使用`access`函数检查文件是否存在，我们可以编写以下代码:

```
const fs = require("fs");
const file = "./files/file.txt";fs.access(file, fs.constants.F_OK, err => {
  console.log(`${file} ${err ? "does not exist" : "exists"}`);
});
```

当我们运行上面的代码时，如果文件存在，我们应该得到类似下面的输出:

```
./files/file.txt exists
```

要使用它来检查文件是否可读，我们可以将`fs.constants.R_OK`传入`access`，如下面的代码所示:

```
const fs = require("fs");
const file = "./files/file.txt";fs.access(file, fs.constants.R_OK, err => {
  console.log(`${file} ${err ? "is not readable" : "is readable"}`);
});
```

如果该文件是可读的，那么我们应该得到类似下面的输出:

```
./files/file.txt is readable
```

要检查文件是否可写，我们可以使用下面代码中的`fs.constants.W_OK`标志:

```
const fs = require("fs");
const file = "./files/file.txt";fs.access(file, fs.constants.W_OK, err => {
  console.log(`${file} ${err ? "is not writable" : "is writable"}`);
});
```

如果文件是可写的，我们应该得到如下输出:

```
./files/file.txt is writable
```

如果我们想检查一个文件是否存在以及它是否可写，我们可以使用按位`AND`操作符将多个常量连接在一起，如下面的代码所示:

值得注意的是，在对同一个文件进行其他文件操作(如写入)之前，我们不应该使用`access`函数来检查文件的可访问性，因为它引入了一个竞争条件，在检查文件和完成文件操作之间的时间内，其他操作可能已经改变了文件的可访问性。

由于`open`、`readFile`和`writeFile`系列函数在执行任何操作之前都会检查文件的可访问性，如果不能以您指定的方式访问，就会抛出错误，所以我们不需要使用`access`函数进行预先检查。

在 Windows 上，目录上的访问控制策略可能会限制用户对它的访问，但是`access`函数不会检查这一点，因此它可能会报告它是可访问的，即使它受到 Windows 访问控制列表的限制。

还有一个同步版本的`access`函数，叫做`accessSync`函数。它采用与`access`函数相同的参数，除了最后的回调参数，并返回`undefined`。

如果出现错误，将会抛出一个异常。例如，我们可以像下面的代码一样使用它:

# 使用 fs.appendFile 和 fs.appendFileSync 将数据追加到现有文件

要将数据追加到现有文件中，我们可以使用`appendFile`函数来完成。它需要四个参数。

第一个参数是对文件的引用，可以是字符串路径、`Buffer`对象、URL 对象或文件描述符，后者是标识文件的整数。

第二个参数是要写入文件的数据，可以是一个字符串或一个`Buffer`对象。

第三个参数用于传入一个带有几个选项的对象。有三样东西可以设置，它们是编码、模式和标志。编码指定了正在编写的文本的字符编码，默认为`utf8`。模式是一个整数，默认为`0o666`。

该模式设置文件权限和粘滞位，但仅在文件已经创建的情况下。`0o666`既可读又可写的意思。flag 选项指定正在写入的文件的读取或写入选项。

上面列出的对于这个论点都是有效的。最后一个参数是回调函数，这个函数接受一个`err`对象，如果存在错误，这个对象不是`null`。

例如，如果我们想用路径`./files/file.txt`将数据追加到一个文本文件中，我们可以用我们想写的内容调用`appendFile`，如下面的代码所示:

如果您使用给定的路径转到文件，那么您应该看到内容写在文件的末尾。

我们也可以使用由`open`函数提供的文件描述符，我们可以一起使用`open`和`append`，如下面的代码所示:

注意，我们将`a+`传递给了`open`函数的参数，让我们将新数据追加到现有文件中。

`append`功能的同步版本是`appendSync`功能。它将给定的内容同步附加到给定的文件中。

它需要三个参数。第一个参数是对文件的引用，它可以是字符串路径、`Buffer`对象、URL 对象或文件描述符，后者是标识文件的整数。

第二个参数是要写入文件的数据，它可以是一个字符串或一个`Buffer`对象。第三个参数用于传入一个带有几个选项的对象。

有三件事可以设置，编码、模式和标志。编码指定了正在写入的文本的字符编码，默认为`utf8`。

模式是默认为`0o666`的整数。该模式设置文件权限和粘滞位，但仅在文件已经创建的情况下。`0o666`表示既可读又可写。

flag 选项指定正在写入的文件的读取或写入选项。上面列出的对于这个论点都是有效的。

我们可以像下面的代码那样调用它:

```
const fs = require("fs");
const file = "./files/file.txt";fs.appendFileSync(file, "data", { encoding: "utf8", mode: 666 });
console.log('The "data" was appended to file!');
```

我们也可以通过使用`openSync`函数来获取文件描述符，将它与文件描述符一起使用，如下面的代码所示:

# 使用 fs.chmod 和 fs.chmodSync 更改文件的权限

要异步更改 Node.js 程序中文件的权限，我们可以使用`chmod`函数。它需要三个参数。

第一个参数是对文件的引用，它可以是一个字符串路径、一个`Buffer`对象、一个 URL 对象或一个文件描述符，后者是一个标识文件的整数。

第二个参数是文件模式，它是一个八进制数，对应于您想要设置的文件权限。

第三个参数是一个回调函数，带有一个`err`参数，在文件权限更改操作完成时调用。

如果没有错误,`err`将为`null`,否则将设置带有错误信息的对象。

要设置文件的文件模式，我们可以使用内置在`fs`模块中的常量。它们可以与按位`OR`运算符结合使用，以同时设置多个模式。

模式常数列表如下:

*   `fs.constants.S_IRUSR` —对应八进制`0o400`，使文件对所有者可读。
*   `fs.constants.S_IWUSR` —对应于八进制`0o200`，使文件可由所有者写入。
*   `fs.constants.S_IXUSR` —对应于八进制`0o100`，使文件可由所有者执行或搜索。
*   `fs.constants.S_IRGRP` —对应于八进制`0o40`，使成组可读。
*   `fs.constants.S_IWGRP` —对应八进制`0o20`，使文件按组可写。
*   `fs.constants.S_IXGRP` —对应八进制`0o10`，使文件可按组执行或搜索。
*   `fs.constants.S_IROTH` —对应八进制`0o4`，使文件对其他人可读。
*   `fs.constants.S_IWOTH` —对应八进制`0o2`，使文件可被其他人写入。
*   `fs.constants.S_IXOTH` —对应于八进制`0o1`，使文件可被其他人执行或搜索。

我们还可以用三个八进制数字设置文件权限，就像 Unix 或 Linux 文件权限一样。

最左边的数字是所有者的权限。中间的数字是该组的权限，最右边的数字是对其他人的权限。

有效数字包括以下内容:

*   `7` —读、写、执行
*   `6` —读写
*   `5` —读取并执行
*   `4` —只读
*   `3` —编写并执行
*   `2` —只写
*   `1` —仅执行
*   `0` —不允许

我们可以在下面的代码中使用`chmod`:

上面的代码将给予每个人对`file.txt`的读写权限。同样，我们可以用这些常数做同样的事情:

正如我们所看到的，使用八进制数要短得多，所以使用数而不是常数可能更好。

`chmod`功能的同步版本是`chmondSync`功能。这需要两个参数。

第一个参数是对文件的引用，它可以是字符串路径、`Buffer`对象、URL 对象或文件描述符，后者是标识文件的整数。

第二个参数是文件模式，它是一个八进制数，对应于您想要设置的文件权限。它们应该与上面列出的相同。

我们可以像在下面的代码中一样使用它，将对`file.txt`的读写权限授予每个人:

```
const fs = require("fs");
const file = "./files/file.txt";fs.chmodSync(file, 0o666);
console.log("File permission changed");
```

或者相当于:

我们分别使用`access`、`append`和`chmod`函数检查文件可访问性、附加文件和更改权限。

`access`函数检查文件是否存在，是否可读可写。

`append`函数将数据追加到现有文件中，无论是文本还是二进制数据。

`chmod`函数让我们像在 Unix 或 Linux 命令行中一样更改文件的权限。这些功能也有同步版本，分别是`accessSync`、`appendSync`和`chmodSync`。