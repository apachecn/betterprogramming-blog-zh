# 使用 Rust 应该知道的基本货物功能

> 原文：<https://betterprogramming.pub/rust-what-is-cargo-783504c5b5bd>

## 你的货物指南

![](img/dcbf3b436342c5013a7184261faf44e9.png)

克里斯·帕甘在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

Cargo 是 Rust 的一个构建系统和包管理器。比如 Python 中的`pip`或者 JavaScript 中的`npm`。

这意味着，cargo 是一个工具，使我们可以轻松下载外部板条箱或通过一行命令管理依赖关系。

很多人对以下三个工具感到困惑。

*   `rustup`:RUST 编程语言系统的工具链安装程序。
*   `rsutc`:铁锈的编译器
*   `cargo`:铁锈包的包经理

让我们来看看 cargo 有什么功能:

# 版本

该命令显示所安装货物的版本。以下三个命令显示了相同的结果。

```
$ cargo version
cargo 1.59.0 (49d8809dc 2022-02-10)$ cargo --version
cargo 1.59.0 (49d8809dc 2022-02-10)$ cargo -V
cargo 1.59.0 (49d8809dc 2022-02-10)
```

# 新的

该命令创建一个新的二进制或库 rust 包。

## 二进制

这个参数意味着创建一个可执行的二进制包。如果不指定包类型，cargo 会默认创建一个二进制包。

```
$ cargo new "package-name" (--bin)
```

**括号** `**()**` **表示可选参数，非强制。**

## 新二进制示例

```
$ cargo new hello-cargo
     Created binary (application) `hello-cargo` package$ cargo new hello-cargo2 --bin
     Created binary (application) `hello-cargo2` package
```

## -图书馆

此参数用于创建库项目。如果没有项目类型参数，如我所说，默认创建二进制包。

```
$ cargo new "binary-name" --lib
```

## 新库示例

```
$ cargo new hello-cargo3 --lib
     Created library `hello-cargo3` package
```

# -风投

cargo 创建的包是一个 git 目录。如果不想使用 vcs(版本控制系统)或者想改变 git 以外的东西，可以使用 VCS 参数。

```
$ cargo new "package-name" --vsc "vcs-name"
```

## 更改 vcs 示例

```
$ cargo new hello-cargo4 --vcs git
     Created binary (application) `hello-cargo4` package

$ cargo new hello-cargo5 --vcs none
     Created binary (application) `hello-cargo5` package
```

## -名字

如果你想有一个不同于目录名的包名，你可以使用`--name`参数。当然可以通过编辑`Cargo.toml`来改变

```
$ cargo new "folder-name" --name "package-name"
```

# 初始化

跟`new`差不多。唯一的区别是`new`创建一个新目录，而`init`在现有目录中创建一个包。

```
$ mkdir hello-cargo6 $ cd hello-cargo6$ cargo init
     Created binary (application) package
```

## -名字

这与`cargo new --name`相同

# 搜索

鲁斯塔西亚人用板条箱分享他们的板条箱。比如 Python 中的 PyPI

`cargo search`命令可以搜索包含特定关键字的板条箱。

```
cargo search "crate-name"
```

## 搜索示例

例如，如果您想找到一个 EXIF 解析器，您可以通过命令`cargo search exif`得到以下结果。

```
$ cargo search exif
exif = "0.0.1"            # Rust wrapper for libexif
kamadak-exif = "0.5.4"    # Exif parsing library written in pure Rust
s.
imagemeta = "0.1.0"       # Support for manipulating image metadata (exif, etc) in Rust.
img-parts = "0.2.3"       # Low level crate for reading and writing Jpeg, Png and RIFF image containers
exifmv = "0.1.3"          # Moves images into a folder hierarchy based on EXIF tags
gexiv2-sys = "1.1.2"      # This library provides Rust FFI declarations for the gexiv2 library, which is a GObject-based wrapper …
rexiv2 = "0.9.1"          # This library provides a Rust wrapper around the gexiv2 library, which is a GObject-based wrapper arou…
... and 24 crates more (use --limit N to see more)
```

# 安装

您可以通过`cargo install`命令轻松安装 crates.io 上的外部板条箱。

```
cargo install "binary-name"
```

## 安装示例

这是一个安装示例`ripgrep` crate，它是一个使用 Rust 实现的 grep 库

```
$ cargo install ripgrep
    Updating crates.io index
  Downloaded ripgrep v13.0.0
  Downloaded 1 crate (272.1 KB) in 0.85s
  Installing ripgrep v13.0.0
  .
  .
  .
    Finished release [optimized + debuginfo] target(s) in 32.99s
  Installing C:\Users\d2h10s\.cargo\bin\rg.exe
   Installed package `ripgrep v13.0.0` (executable `rg.exe`)
```

# Cargo.toml

新创建的包的树如下所示。

```
.
│  .gitignore
│  Cargo.toml
│
└─src
        main.rs
```

你总是可以看到`Cargo.toml`文件。这是货物包装的设置文件，包含您定义的`package name`、`package version`、`rust edition`等。

在`[dependencies]` 字段中，您可以描述您的包裹所依赖的板条箱。

```
[package]
name = "exif-fixer"
version = "0.1.0"
edition = "2021"# See more keys and their definitions at https://doc.rust-lang.org/cargo/reference/manifest.html[dependencies]
```

## 添加板条箱依赖性示例

比如你要用`kamadak-exif`板条箱，你得这么写。

```
[package]
name = "exif-fixer"
version = "0.1.0"
edition = "2021"# See more keys and their definitions at https://doc.rust-lang.org/cargo/reference/manifest.html[dependencies]kamadak-exif = "0.5.4" 
```

然后，当您运行`cargo build`时，cargo 会自动下载外部板条箱。

```
$ cargo build
    Updating crates.io index
  Downloaded mutate_once v0.1.1
  Downloaded kamadak-exif v0.5.4
  Downloaded 2 crates (56.0 KB) in 0.48s
   Compiling mutate_once v0.1.1
   Compiling kamadak-exif v0.5.4
   Compiling exif-fixer v0.1.0 (C:\Users\d2h10s\repository\exif-fixer)
    Finished dev [unoptimized + debuginfo] target(s) in 3.06s
```

# 支票

有时，我们需要在不编译的情况下检查语法，然后使用`cargo check`命令。

这个命令不编译和生成可执行的二进制文件。

就`check`语法快。

```
$ cargo check
    Checking mutate_once v0.1.1
    Checking kamadak-exif v0.5.4
    Checking exif-fixer v0.1.0 (C:\Users\d2h10s\repository\exif-fixer)
    Finished dev [unoptimized + debuginfo] target(s) in 0.72s
```

# 建设

最后，你可以用`cargo build`命令构建一个 rust 包。

> **构建是指创建一个可执行的二进制文件。**

```
$ cargo build
    Finished dev [unoptimized + debuginfo] target(s) in 0.01s
```

# 奔跑

`run`命令既能构建又能执行。当然，如果自上次构建以来代码没有改变，货物将只运行而不重建。

```
$ cargo run
    Finished dev [unoptimized + debuginfo] target(s) in 0.01s
     Running `target\debug\hello-world.exe`
Hello, world!
```