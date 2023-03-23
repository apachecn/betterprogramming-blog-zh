# Locate:一个用 Rust 编写的递归搜索工具

> 原文：<https://betterprogramming.pub/locate-a-recursive-search-tool-6e497d0134ba>

## 一个用 Rust 编写的工具，可以递归搜索文件夹中的所有文件

![](img/8268ea37cf419de9687c64a64e592a36.png)

Evgeni Tcherkasski 在 [Unsplash](https://unsplash.com/s/photos/search?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

有多少次你在文件中寻找某个字符串或函数的起源？

Locate 是用 [Rust](https://www.rust-lang.org/) 编写的工具，它递归地遍历每个文件，并给出文件中查询字符串的确切位置。

它的速度快得惊人，因为它是用 Rust 编写的，而且它可以递归地遍历目录。

# 动机

我想学习 Rust，我认为学习一门新语言的最好方法是创建一个项目。

我一直使用 VS 代码中的 search 函数来查找函数的起源或者函数被使用的地方。如果处理大型项目，有时很难准确定位使用函数的所有点。当然，人们可以通过导入来追溯函数的起源，但是拥有一个工具有时会很方便。

注意:由于这是我在 Rust 中的第一个项目，有可能我做了一些不应该做的事情。

我们开始吧。

# 创建新的包

为了开始一个新的包，我们做`cargo new`，因为这是一个二进制程序:

`cargo new locate --bin`

这将创建一个文件夹结构:

```
. 
├── Cargo.toml 
└── src 
    └── main.rs 
1 directory, 2 files
```

# 我们需要的包裹

```
fstream = "0.1.2"
walkdir = "2.3.1"
argparse = "0.2.2"
colored = "1.9"
```

*   `**fstream**` 将用于读取文件。
*   `**walkdir**` 将用于迭代所有文件夹和文件。
*   `**argparse**` 是一个简单的参数解析器，用来解析参数。
*   `**colored**`将用于给终端输出着色。

将这些添加到您的`cargo.toml`中。

现在我们的`cargo.toml`看起来会像这样:

# 密码

## 主代码

我们的基本工作流程如下。首先，我们将从用户处获取`path`和`query`。然后，我们需要一个函数来遍历路径中的所有文件夹，并检查查询字符串是否存在于任何文件中。如果是这样，文件的路径将被传递给另一个函数，查询字符串的确切位置将位于该函数中。

遍历目录很容易，因为我们将使用`walkdir`来完成。

## c 的代码`hecking dir`

我们在给定的路径上创建一个新的`Walkdir`，并应用一个过滤器来消除权限错误。

接下来，我们检查`iter`是否是一个文件。然后我们使用`fstrem`来检查查询字符串是否在文件中。

一旦我们得到包含查询的文件，它就被传递给另一个函数，该函数定位准确的行号。

## 检查文件的代码

很简单:我们逐行读取整个文件。由于它返回一个字符串向量，我们可以很容易地遍历每一行并检查查询字符串是否存在。

如果存在，我们就简单地打印它。有打印格式(如`.green()`、`.bold()`等)。)这是在我们之前进口的`colored`板条箱的帮助下完成的。

仅此而已。我们有两个部分的代码，读取文件和找到文件。

剩下的工作是从命令行解析参数。库参数解析器的灵感来自 Python 中的`argparse`。

## 参数分析器的代码

这部分也很简单。我们正在创建两个可变变量(Rust 中的变量在默认情况下是不可变的)。然后我们将创建一个新的 argpaser 对象，设置它的描述，并创建我们的选项。最棒的是，它使用我们对每个选项的描述自动生成帮助消息。

完成了。我们试试吧！

类型`cargo build`。您会发现在`target/debug`中创建了一个二进制文件。您可以直接通过`cargo run`或者使用您刚刚创建的二进制文件来运行它。

# 演示

![](img/6653c737135af2d6fa9b9dc199657408.png)

你可以在 [GitHub](https://github.com/pr4k/locate/) 找到完整的源代码。

还有，如果你不想做这一切，可以直接用:`cargo install locate`安装。或者可以在 [cargo@locate](https://crates.io/crates/locate) 查看。

感谢阅读。编码快乐！