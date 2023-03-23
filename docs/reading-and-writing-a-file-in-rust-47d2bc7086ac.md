# 在 Rust 中读写文件

> 原文：<https://betterprogramming.pub/reading-and-writing-a-file-in-rust-47d2bc7086ac>

## 本文将回顾如何在 Rust 中创建、打开文件、读取其内容并写入其中。

![](img/3cc055fb32eee5627e0ec89c8c2a595b.png)

照片由[斯科特·格雷厄姆](https://unsplash.com/es/@homajob?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

作为一名软件工程师或对技术感兴趣的人，读和写(也称为输入和输出的 I/O)操作是一些你应该知道的基本概念。

阅读完本文后，您将能够创建、打开一个文件，阅读其内容，并向其中添加内容。所以让我们开始吧。

但是首先，让我们通过回顾角色在《铁锈》中是如何表现的来打下一些重要的基础。

# **字符串和字符串(又名字符串切片)**

我知道这个标题(☝🏿)似乎有点混乱…在 Rust 中，我们有两种类型来表示字符串:

*   `str` : string slice，核心 Rust 编程语言的唯一字符串类型。这种类型是静态的，不可变的，我们通常看到它的借用形式:`&str`。
*   `String`:由于有了标准库，Rust 提供了第二种字符串类型。这种类型更加灵活，因为它可以被拥有并且是可变的。

两种字符串类型都是 [UTF-8](https://en.wikipedia.org/wiki/UTF-8) 编码的(更多关于那个[在这里](https://doc.rust-lang.org/book/ch08-02-strings.html))；`String`在你需要拥有或改变你的字符串时使用，而`&str`在你想要可视化一个字符串时使用。

Rust 标准库允许我们执行不同的文件操作，这要归功于令人敬畏的 struct `[File](https://doc.rust-lang.org/std/fs/struct.File.html)`和相关的方法。我们将在本文的其余部分利用它。

我们现在都准备好了，所以让我们直接开始吧！！

[](https://blog.devgenius.io/reading-and-writing-a-json-file-in-rust-2731da8d6ad0) [## 在 Rust 中读写 JSON 文件

### 本文将向您介绍在 RUST 中解析和编写 JSON 文件。

blog.devgenius.io](https://blog.devgenius.io/reading-and-writing-a-json-file-in-rust-2731da8d6ad0) 

## 你如何创建一个文件？

为了创建一个文件，我们将利用`create`，struct `File`的一个方法，如下例所示:

在 Rust 中创建一个文件

`create`方法返回一个`Result`，因为我们可以成功地创建文件，或者我们可能有一个错误(整个磁盘，没有特权，等等)。，选择你的毒药💀).

`Result`是一个**枚举**也是 Rust 标准库提供的，允许我们管理错误(更多[此处](https://web.mit.edu/rust-lang_v1.25/arch/amd64_ubuntu1404/share/doc/rust/html/book/second-edition/ch09-02-recoverable-errors-with-result.html))。

## 如何打开一个文件？

为了打开一个文件，我们将使用结构`File`的`open`方法，它也返回一个`Result`。

用 Rust 打开一个文件

这里`open`将尝试在我们的 Rust 项目的根级别找到文件。如果该文件不存在，将会创建它。

我鼓励您使用现有和不存在的文件运行我们的最后一个示例，看看结果。

## 你如何阅读文件的内容？

读取文件内容的简单方法是打开它，并以`String`的形式读取其内容(现在您明白为什么我们在第一部分介绍字符串类型了)。

在 Rust 中读取文件的内容

如您所见，我们创建了一个名为`contents`的可变`String`变量，并通过使用方法`read_to_string()`在第 7 行传递文件内容来更新它。我鼓励你检查方法`read`，它允许你以字节向量的形式检索文件的内容(更多关于它的内容[在这里](https://doc.rust-lang.org/std/fs/fn.read.html))。

我更喜欢使用 struct `[BufReader<R>](https://doc.rust-lang.org/std/io/struct.BufReader.html)`来读取文件的内容，因为这样更有效也更好。*的诀窍*是`Bufreader`在从文件中读取时提供一个缓冲组件(更多关于那个[这里是](https://doc.rust-lang.org/std/io/struct.BufReader.html))。

在 Rust 中读取文件内容的更好方法

正如您所看到的，我们通过打开一个文件开始了我们的示例，正如我们前面所学的那样。然后在第 6 行，我们创建了`reader`变量；我们刚刚打开的文件上的一个`BufReader`。然后我们将使用这个`reader`来提取文件的内容(第 8 行)。

## 如何向文件中添加一些内容？

添加内容并不是一项简单的任务，因为我们可能想要向新文件添加一些内容，过载现有文件的内容，或者向现有文件添加一些内容。

几秒钟前看起来微不足道的事情现在变得更加复杂了…欢迎(回到)软件开发的美妙之处。

所以我们先从前两种情况说起；以下示例说明如何向新创建的文件添加内容，或者打开现有文件并覆盖其内容。

在 Rust 中向文件添加一些内容

在我们的例子中，如果文件`missy.txt`不存在，我们将在添加内容之前创建它。如果文件存在，我们的新内容将替换它的内容。

是的，我们再次使用`create`。`create`允许我们创建文件并以只写模式打开现有文件。

在现有文件上使用`create`确实可行，但是如果我们想要更精确，我们应该使用结构`OpenOptions`来确定如何访问文件。

在 Rust 中更新文件内容

在这个例子中，我们打开一个现有的文件，并在开头添加一些内容。如果文件中有一些内容，我们的新内容将被添加到开头，只替换所需的数据量(这里要小心；我们在第 9 行将`append`选项设置为`false`。

在下面的最后一个例子中，我们将把一些内容添加到一个文件中，方法是把它附加到任何现有数据的末尾。在这里看到第 9 行的`append`选项到`true`。

在 Rust 中将一些内容添加到文件中

与写入过程一样，Rust 也有一个`[BufWriter](https://doc.rust-lang.org/std/io/struct.BufWriter.html)` [结构](https://doc.rust-lang.org/std/io/struct.BufWriter.html)，它提供了写入流的最佳方式。

# 最后

我希望我能为您在 Rust 中执行一些文件 I/O 操作打下坚实的基础。这只是开始。我鼓励你研究一下 struct `File`的文档，然后深入研究`[BufReader](https://doc.rust-lang.org/std/io/struct.BufReader.html)`和`[BufWriter](https://doc.rust-lang.org/std/io/struct.BufWriter.html)`。

![](img/b221353c815e09e3648df8e760a31ae0.png)

像往常一样，米西向你问好

# 资源

*   更多关于结构`File` [这里](https://doc.rust-lang.org/std/fs/struct.File.html)。
*   更多关于`BufReaer` [这里](https://doc.rust-lang.org/std/io/struct.BufReader.html)。
*   更多关于`BufWriter` [这里](https://doc.rust-lang.org/std/io/struct.BufWriter.html)。

[](https://anismousse.medium.com/how-to-create-a-restful-web-service-in-rust-cae94aa0fc6f) [## 如何在 Rust 中创建 RESTful web 服务

### 距离我上一篇文章已经快三个月了，我已经错过写关于 Rust 的文章了！！

anismousse.medium.com](https://anismousse.medium.com/how-to-create-a-restful-web-service-in-rust-cae94aa0fc6f)