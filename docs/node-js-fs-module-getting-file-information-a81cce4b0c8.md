# Node.js 的 fs 模块—获取文件信息

> 原文：<https://betterprogramming.pub/node-js-fs-module-getting-file-information-a81cce4b0c8>

## 将文件及其数据引入您的应用程序

![](img/1f821e3cc64a5864dc76a34d8999b75d.png)

照片由 [Gayatri Malhotra](https://unsplash.com/@gmalhotra?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

操作文件和目录是任何程序的基本操作。由于 Node.js 是一个服务器端平台，可以直接与运行它的计算机交互，因此能够操作文件是一个基本特性。

幸运的是，Node.js 的库中内置了一个`fs`模块。它有许多功能，可以帮助操纵文件和文件夹。支持的文件和目录操作包括基本的操作，如操作和打开目录中的文件。

同样，它也可以对文件做同样的事情。它可以同步和异步地做到这一点。它有一个异步 API，该 API 具有支持承诺的功能。此外，它还可以显示文件的统计数据。

几乎所有我们能想到的文件操作都可以用内置的`fs`模块来完成。

在本文中，我们将使用`fs`模块中的函数，通过`fs.stat()`、`fs.lstat()`和`fs.fstat()`函数获取位于`fs.Stats`对象中的文件的数据。

要获得关于文件的信息，我们可以使用由`fs.stat()`、`fs.lstat()`和`fs.fstat()`函数返回的`fs.Stats`对象，以及它们的同步对应物。

它们可以将数字数据显示为`bigint`，因为它是作为 option 键传入的，其值设置为`true`。它具有以`Ns`为后缀的纳秒精度属性。

`stat`函数将一个 path 对象作为第一个参数，这个对象可以是一个字符串、缓冲区或 URL 对象。

第二个参数是一个对象，它可以将`bigint`作为键，这是一个布尔值。如果设置为`true`，那么数值信息将作为`bigInts`返回。

第三个参数是一个回调函数，它的第一个参数是 error 对象，第二个参数是`stats`对象，它包含文件的信息，并且在检索文件信息时它正在运行。

`stat`函数异步运行。它的同步对应物是`statSync`函数，它接受同样的前两个参数，没有回调函数。`statSync`以对象形式返回文件信息。

`lstat`类似于`stat`，但不遵循符号链接。它接受一个 path 对象作为第一个参数，该对象可以是字符串、缓冲区或 URL 对象。

第二个参数是一个对象，它可以将`bigint`作为键，这是一个布尔值。如果设置为`true`，那么数值信息将作为`bigInts`返回。

第三个参数是一个回调函数，它的第一个参数是 error 对象，第二个参数是`stats`对象，它包含了文件的信息，并且在检索文件信息时正在运行。

当传入的路径是一个符号链接时，它给出了这个符号链接的信息。`lstat`异步运行，以便在不确定的时间内检索数据。

它的同步对应物`lstatSync`函数接受与`lstat`函数相同的参数，但没有回调函数，并返回包含文件信息的`Stat`对象。

`fstat`功能类似于`stat`功能。它接受一个 path 对象作为第一个参数，该对象可以是字符串、缓冲区或 URL 对象。

第二个参数是一个可以将`bigint`作为键的对象，它是一个布尔值。如果设置为`true`，那么数值信息将作为`bigInts`返回。

第三个参数是一个回调函数，它的第一个参数是 error 对象，第二个参数是`stats`对象，它包含文件的信息，并且在检索文件信息时它正在运行。

**`stat`和`fstat`唯一的区别**是它采用文件描述符而不是路径对象。

我们可以从回调中获得文件描述符，回调由`fs.open`函数及其对应的 promise 和同步函数`fsPromises.open`和`fs.opensync`接受。

要使用`fs.stat`函数，我们可以像下面的代码一样使用它:

```
const fs = require("fs");fs.stat("./files/file.txt", (err, stat) => {
  if (err) throw err;
  console.log(stat);
});
```

然后，如果我们运行上面的代码，我们会得到类似下面的输出:

```
Stats {
  dev: 3605029386,
  mode: 33206,
  nlink: 1,
  uid: 0,
  gid: 0,
  rdev: 0,
  blksize: 4096,
  ino: 22799473115106240,
  size: 0,
  blocks: 0,
  atimeMs: 1572569358035.625,
  mtimeMs: 1572569358035.625,
  ctimeMs: 1572569358035.625,
  birthtimeMs: 1572569358035.625,
  atime: 2019-11-01T00:49:18.036Z,
  mtime: 2019-11-01T00:49:18.036Z,
  ctime: 2019-11-01T00:49:18.036Z,
  birthtime: 2019-11-01T00:49:18.036Z
}
```

正如我们所见，`Stats`物体有许多属性。上面列出了数据属性。它还有一些函数属性。

`Stats`对象中的数据属性含义如下:

*   `dev` —存储给定文件的设备的数字标识符。可以是数字，也可以是`bigInt`。
*   `ino` —文件的“信息节点”号。它是一个包含文件、目录或其他文件系统对象的基本信息的数字。可以是数字，也可以是`bigInt`。
*   `mode` —文件类型和模式的位域描述。可以是数字，也可以是`bigInt`。
*   `nlink` —文件存在的硬链接数量。可以是数字，也可以是`bigInt`。
*   `uid` —拥有文件的用户的数字用户标识符。仅适用于 POSIX 系统。可以是数字，也可以是`bigInt`。
*   `gid` —拥有文件的用户的数字组标识符。仅适用于 POSIX 系统。可以是数字，也可以是`bigInt`。
*   `rdev` —如果是特殊文件，文件的数字设备标识符。如果一个文件用于 I/O，它就是特殊的。例如，页面文件和休眠文件被认为是特殊的文件。可以是数字，也可以是`bigInt`。
*   `size` —文件的大小，以字节为单位。它可以是一个数字或一个`bigInt`。
*   `blksize` —文件系统 I/O 的块大小，可以是数字，也可以是`bigInt`。
*   `blocks` —分配给文件的块数。可以是数字，也可以是`bigInt`。
*   `atimeNs` —时间戳，指示自 POSIX 纪元(相对于 1970 年 1 月 1 日午夜的时间)以来最后一次以纳秒为单位访问文件的时间。可以是数字，也可以是`bigInt`。
*   `mtimeNs` —时间戳，指示自 POSIX 纪元(相对于 1970 年 1 月 1 日午夜的时间)以来文件的最后修改时间(以纳秒为单位)。可以是数字，也可以是`bigInt`。
*   `ctimeNs` —时间戳表示自 POSIX 纪元(相对于 1970 年 1 月 1 日午夜的时间)以来文件最后一次更改的时间，单位为纳秒。可以是数字，也可以是`bigInt`。
*   `birthtimeNs` —时间戳表示自 POSIX 纪元(相对于 1970 年 1 月 1 日午夜的时间)以来文件创建的时间，单位为纳秒。可以是数字，也可以是`bigInt`。
*   `atime` —时间戳，指示自 POSIX 纪元(相对于 1970 年 1 月 1 日午夜的时间)以来最后一次访问文件的时间(以毫秒计)。可以是数字，也可以是`bigInt`。
*   `mtime` —时间戳，指示自 POSIX 纪元(相对于 1970 年 1 月 1 日午夜的时间)以来最后一次修改文件的时间(毫秒)。可以是数字，也可以是`bigInt`。
*   `ctime` —时间戳，指示自 POSIX 纪元(相对于 1970 年 1 月 1 日午夜的时间)以来最后一次更改文件的时间(以毫秒为单位)。可以是数字，也可以是`bigInt`。
*   `birthtime` —时间戳，指示自 POSIX 纪元(相对于 1970 年 1 月 1 日午夜的时间)以来文件创建的时间(毫秒)。可以是数字，也可以是`bigInt`。

`Stats`对象还具有以下功能属性，用于检查文件的基本信息:

*   `isBlockDevice()` —这是一个带有布尔返回值的函数，如果文件是块设备，则返回`true`。块设备指的是代表以块存储文件并同样检索它们的设备的文件。
*   `isCharacterDevice()` —这是一个带有布尔返回值的函数，如果文件是字符设备，则返回`true`。字符设备指的是代表设备的文件，它提供对硬件设备的无缓冲直接访问。他们不需要允许程序一次读写一个字符。
*   `isDirectory()` —这是一个具有布尔返回值的函数，如果项目是目录，则返回`true`。
*   `isFIFO()` —这是一个具有布尔返回值的函数，如果项目是先进先出管道，则返回`true`。FIFO 管道意味着进入设备的文件的第一个位将与检索时出来的位相同。它只允许单向通信。
*   `isFile()` —这是一个具有布尔返回值的函数，如果项目是文件，则返回`true`。
*   `isSocket()` —这是一个具有布尔返回值的函数，如果项目是一个套接字，则返回`true`。套接字是一种特殊的文件，它支持两个进程之间的通信。它可以通过域套接字发送数据和文件描述符。它可以进行双向通信。
*   `isSymbolicLink()` —这是一个具有布尔返回值的函数，如果项目是符号链接，则返回`true`。符号链接是以绝对或相对路径的形式对另一个文件或目录的引用。

要使用同步版本的`stat`函数，即`statSync`函数，我们可以编写类似下面的代码:

```
const fs = require("fs");const stat = fs.statSync("./files/file.txt");
console.log(stat);
```

`Stat`对象直接从`statSync`函数返回。

要使用`fstat`函数，我们必须首先获得文件描述符，这可以通过`open`函数及其变体来实现。例如，如果我们想使用`open`函数来获取文件描述符，我们可以编写以下代码:

```
const fs = require("fs");fs.open("./files/file.txt", "r", (err, fd) => {
  if (err) throw err;
  fs.fstat(fd, (err, stat) => {
    if (err) throw err;
    console.log(stat);
    fs.close(fd, err => {
      if (err) throw err;
    });
  });
});
```

我们可以将`fstat`与`open`函数的 promise 版本一起使用，如以下代码所示:

```
const fsPromises = require("fs").promises;
const fs = require("fs");(async () => {
  const fdObj = await fsPromises.open("./files/file.txt", "r");
  fs.fstat(fdObj.fd, (err, stat) => {
    if (err) throw err;
    console.log(stat);
    fs.close(fdObj.fd, err => {
      if (err) throw err;
    });
  });
})();
```

`open`函数的 promise 版本返回一个 promise，该 promise 解析为一个包含文件描述符的对象。我们可以使用`fd`属性获取文件描述符，并将其传递给`fstat`函数。

同样，对于`lstat`函数，我们可以用下面的代码来调用它:

```
const fs = require("fs");fs.lstat("./files/file.txt", (err, stat) => {
  if (err) throw err;
  console.log(stat);
});
```

除了获取符号链接的数据而不是跟随它之外，`lstat`几乎做了和`stat`一样的事情，所以我们将得到与上面类似的输出。

`fs.stat()`、`fs.lstat()`和`fs.fstat()`函数对于获取关于文件、目录和符号链接的数据非常有用。

他们可以让我们获得`Stat`对象，该对象包含大量我们可以使用的信息，包括访问时间、修改时间、文件是否是特殊文件、存储该文件的设备以及通过其值和函数属性获得的许多其他信息。

数据可以显示为数字或`bigInt`(如果它们是数字的话),时间戳可以以毫秒和纳秒为单位，以获得额外的精度。