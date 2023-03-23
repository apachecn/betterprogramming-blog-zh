# 用 Rust 构建一个命令行工具来演奏吉他和弦

> 原文：<https://betterprogramming.pub/build-a-command-line-tool-with-rust-to-play-guitar-chords-d07df7b330b6>

## 打印吉他和弦的分步指南

![](img/12816a68eaa1191a810731c4b23754a2.png)

[黑暗骑士](https://unsplash.com/@darkrider?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

你弹吉他吗？弹一些不常用和弦的曲子是不是总要上网查吉他和弦？

一个[命令行工具(CLI)](https://en.wikipedia.org/wiki/Command-line_interface) 怎么样，它接受一个和弦的名称，并输出一个关于如何演奏它的图表？

今天我就和大家分享一下如何用上面的思路在 Rust 中从头开始搭建一个命令行工具。

# 最终目标

最后，我们希望构建一个名为`chord`的 CLI。它接受一个字符串作为输入——和弦的名称。它会打印和弦图。像这样:

```
$ chord Cx     ◯   ◯
┌─┬─┬─┬─┬─┐
│ │ │ │ ◯ │
├─┼─┼─┼─┼─┤
│ │ ◯ │ │ │
├─┼─┼─┼─┼─┤
│ ◯ │ │ │ │
└─┴─┴─┴─┴─┘
```

# 为什么生锈

在我的上一篇文章[用 Rust](/building-a-grpc-server-with-rust-be2c52f0860e) 构建 gRPC 服务器中，我已经分享了 Rust 为什么有趣以及为什么你应该学习 Rust。我想在这篇文章中强调一点:

> 基于[栈溢出调查](https://insights.stackoverflow.com/survey/2021#most-loved-dreaded-and-wanted-language-love-dread):“第六年，Rust 是最受喜爱的语言。”

![](img/06597edf917525b6bb3860940e2edd51.png)

铁锈是最受喜爱的语言。[2021 年 Stackoverflow 调查](https://insights.stackoverflow.com/survey/2021#most-loved-dreaded-and-wanted-language-love-dread)

Rust 不仅非常适合构建后端服务，还非常容易构建 CLI。让我们开始吧。

# 安装铁锈

用以下材料安装 Rust:

```
$ curl --proto '=https' --tlsv1.2 -sSf <https://sh.rustup.rs> | sh
```

有关安装的更多信息，请查看:[https://www.rust-lang.org/tools/install](https://www.rust-lang.org/tools/install)。

# 创建新的 Rust 项目

```
**$ cargo new chord --bin**Created binary (application) `chord` package
```

让我们编译并运行程序，确保一切都设置正确:

```
**$ cd chord
$ cargo run**

Hello, world!
```

这是我们目前掌握的情况:

```
|-Cargo.toml
|-Cargo.lock
|-src
|  |-main.rs
```

# 打印指板

一把吉他有 6 根弦，我们通常用[指板](https://en.wikipedia.org/wiki/Fingerboard)来代表手指的位置。

这是目前为止`main.rs`的样子:

```
fn main() {
    println!("Hello, world!");
}
```

让我们更新它以打印一个空指板:

```
const FRETBOARD: &str = "◯ ◯ ◯ ◯ ◯ ◯
┌─┬─┬─┬─┬─┐
│ │ │ │ │ │
├─┼─┼─┼─┼─┤
│ │ │ │ │ │
├─┼─┼─┼─┼─┤
│ │ │ │ │ │
└─┴─┴─┴─┴─┘";fn main() {
    println!("{}", FRETBOARD);
}
```

运行这个程序，这是我们目前所得到的:

```
**$ cargo run**◯ ◯ ◯ ◯ ◯ ◯ 
┌─┬─┬─┬─┬─┐
│ │ │ │ │ │
├─┼─┼─┼─┼─┤
│ │ │ │ │ │
├─┼─┼─┼─┼─┤
│ │ │ │ │ │
└─┴─┴─┴─┴─┘
```

# 拍手声

为了增加解析命令行参数的能力，我们将引入一个库[https://docs.rs/clap/latest/clap/](https://docs.rs/clap/latest/clap/)。

要将 clap 作为依赖项添加到我们的项目中:

```
$ cargo add clap --features derive
```

这只是将下面一行添加到`cargo.toml`，这是为 Rust 定义所有依赖项的地方:

```
clap = { version = "3.2.21", features = ["derive"] }
```

并将`main.rs`更新为:

有了 [Rust macros](https://doc.rust-lang.org/book/ch19-06-macros.html) ，clap 有可能让注释`Args`和免费获取参数解析变得极其简单。

```
/// A CLI to show you how to play a guitar chord
#[derive(Parser, Debug)]
#[clap(version, about)]
struct Args {
   /// Name of the chord
   #[clap()]
   name: String,
}
```

让我们试一试:

```
**$ cargo run -- --help**

chord 0.1.0
A CLI to show you how to play a guitar chordUSAGE:
    chord <NAME>ARGS:
    <NAME>    Name of the chordOPTIONS:
    -h, --help       Print help information
    -V, --version    Print version information**$ cargo run -- C**This is how you play 'C' chord: 
◯ ◯ ◯ ◯ ◯ ◯ 
┌─┬─┬─┬─┬─┐
│ │ │ │ │ │
├─┼─┼─┼─┼─┤
│ │ │ │ │ │
├─┼─┼─┼─┼─┤
│ │ │ │ │ │
└─┴─┴─┴─┴─┘
```

双破折号`--`是一种非常常见的方式来表示`cargo`选项的结束，而其余的则传递给 CLI 程序。不仅是`cargo`，还有[很多其他 shell 命令也是这样做的](https://unix.stackexchange.com/questions/11376/what-does-double-dash-mean)。

还有一个隐藏的特性值得强调。请注意,“向您展示如何弹奏吉他和弦的 CLI”和“和弦名称”都是我们源代码中的注释。它们也包含在帮助消息中。

从源代码来看:

```
**/// A CLI to show you how to play a guitar chord**
#[derive(Parser, Debug)]
#[clap(version, about)]
struct Args {
 **/// Name of the chord**
    #[clap()]
    name: String,
}
```

从输出来看:

```
chord 0.1.0
**A CLI to show you how to play a guitar chord**USAGE:
    chord <NAME>ARGS:
    <NAME>    **Name of the chord**
```

这就是我们需要了解的关于淋病的全部内容。接下来，让我们转到实际的 CLI 本身。

# CLI

将主函数更新为以下内容:

这里，如果输入的和弦名称未知，我们将打印“未知和弦”错误消息。否则，我们将手指放置在之前的空指板上。

进行测试:

```
**$ cargo run -- C** This is how you play 'C' chord: 
x     ◯   ◯
┌─┬─┬─┬─┬─┐
│ │ │ │ ◯ │
├─┼─┼─┼─┼─┤
│ │ ◯ │ │ │
├─┼─┼─┼─┼─┤
│ ◯ │ │ │ │
└─┴─┴─┴─┴─┘**$ cargo run -- Asus4**

Unknown chord 'Asus4'
```

一切看起来都很棒！又快又简单。

# 装置

到目前为止，我们一直在编译和运行 CLI。不仅处理构建、运行、库依赖管理，它还处理安装。

像这样安装`chord` CLI:

```
cargo install --path .
```

现在我们可以直接使用这个 CLI 了:

```
**$ chord G**

This is how you play 'G' chord: 
    ◯ ◯ ◯  
┌─┬─┬─┬─┬─┐
│ │ │ │ │ │
├─┼─┼─┼─┼─┤
│ ◯ │ │ │ │
├─┼─┼─┼─┼─┤
◯ │ │ │ │ ◯
└─┴─┴─┴─┴─┘
```

我的朋友，这就是我们如何在 Rust 中构建 CLI 的。

# 结束了

你忘了怎么弹和弦了吗？现在我们有了更好的工具:)感谢阅读！像往常一样，Github 中的源代码。

查看`blog-post-checkpoint`分支，获取本博客的代码参考:

[https://github . com/yzhong 52/ascii _ chord/tree/blog-post-check point](https://github.com/yzhong52/ascii_chord/tree/blog-post-checkpoint)

此外，查看`master`分支，获取最新版本的 CLI:

[https://github.com/yzhong52/ascii_chord](https://github.com/yzhong52/ascii_chord)