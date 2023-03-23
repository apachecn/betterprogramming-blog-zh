# 苹果硅 M1 苹果电脑上的红宝石:问题、发现和解决办法

> 原文：<https://betterprogramming.pub/ruby-on-apple-silicon-m1-macs-fb159849b2f5>

## 让你的 Ruby 程序在苹果基于 ARM 的处理器上运行

![](img/d08d9bb940f1bc968a261cc1908c91a8.png)

由 [Karthikeya GS](https://unsplash.com/@karthikeya_gs07?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 问题是

有了苹果硅 M1 MAC 电脑，我们有了全新的 macOS、通用二进制文件和一套新的 Xcode 命令行工具。和往常一样，这个包附带了 Ruby 的安装，但这是第一次，它附带了一个通用的二进制文件来支持 ARM 和 Intel CPU——一段旅程就这样开始了。

对于有经验的 Ruby 开发人员来说，这个故事可能并不新鲜和/或不必要的复杂。我只想在这里分享我的经验、见解和成果。

在玩 M1 的时候，我很快就遇到了一些运行 Ruby 程序的问题。就像椰子恐龙，哲基尔，或者快车道。起初，它似乎总是来自包含在许多项目中的一个依赖项。

在网上搜索发现，我不是唯一一个有这个问题的人，因为有些人似乎在 M1 上有一些问题:

[](https://github.com/ffi/ffi/issues/870) [## 1 . 14 . 2:M1 Mac mini 问题#870 ffi/ffi 上的 ffi_c.bundle 中缺少兼容的 arch

### 我正试着用这个指南在我的 M1 mac Mini 上本地建立我的 Github Pages 网站。现在，我试着创造一个…

github.com](https://github.com/ffi/ffi/issues/870) [](https://github.com/CocoaPods/CocoaPods/issues/9907#issuecomment-742102069) [## CocoaPods 与苹果 DTK(苹果硅)的兼容性问题#9907 CocoaPods/CocoaPods

### 引用于 2020 年 10 月 27 日引用于 2020 年 12 月 17 日引用于 2020 年 12 月 24 日引用于…

github.com](https://github.com/CocoaPods/CocoaPods/issues/9907#issuecomment-742102069) [](https://github.com/CocoaPods/CocoaPods/issues/10220) [## 尝试 pod 安装时出错问题#10220 CocoaPods/CocoaPods

### 命令/usr/local/bin/pod 安装报告你做了什么？吊舱安装你预计会发生什么？正在安装我的…

github.com](https://github.com/CocoaPods/CocoaPods/issues/10220) 

…以及其他。

尝试运行某些 Ruby 程序似乎总是会导致相同的问题:

```
LoadError - dlopen(/Library/Ruby/Gems/2.6.0/gems/ffi-1.13.1/lib/ffi_c.bundle, 0x0009): missing compatible arch in /Library/Ruby/Gems/2.6.0/gems/ffi-1.13.1/lib/ffi_c.bundle
```

起初，我以为是 [FFI 库](https://luajit.org/ext_ffi.html)本身以及与 ARM 架构的兼容性缺失。但是我发现我完全错了。

# 变通办法

当我开始分析二进制文件时，我发现在 x86 模式下安装和运行 FFI 效果很好，因此我可以运行:

```
~ arch -arch x86_64 bundle install
~ arch -arch x86_64 bundle exec jekyll serve
```

网上有些人提到，安装一个定制的 Ruby 二进制文件(例如，通过 homebrew 或 rbenv)对他们来说也很好。

所以我们有了一个变通办法——但是问题并没有让我一个人呆着，我不得不更深入地研究它。

# 分析

我深入研究这个问题的第一步是分析 Ruby 二进制文件本身，它位于 macOS 系统上的`/usr/bin`:

```
~ ruby -vruby 2.6.3p62 (2019-04-16 revision 67580) [universal.arm64e-darwin20]
```

和苹果预装的版本一样，这是一个旧版本，但有趣的是，它是一个通用的二进制文件，而不是一个模拟的 x86 版本。

为了分析通用二进制文件，苹果提供了 Lipo，一个命令行程序来创建或操作通用文件。

我转储了关于二进制文件的详细信息:

```
~ lipo -detailed_info /usr/bin/ruby
Fat header in: /usr/bin/ruby
fat_magic 0xcafebabe
nfat_arch 2
architecture x86_64
    cputype CPU_TYPE_X86_64
    cpusubtype CPU_SUBTYPE_X86_64_ALL
    capabilities 0x0
    offset 16384
    size 56624
    align 2^14 (16384)
architecture arm64e
    cputype CPU_TYPE_ARM64
    cpusubtype CPU_SUBTYPE_ARM64E
    capabilities PTR_AUTH_VERSION USERSPACE 0
    offset 81920
    size 56432
    align 2^14 (16384)
```

因此，看起来我们有一个普通的通用二进制文件，它会自动为系统架构选择正确的二进制文件——在我的例子中，是 ARM64 的二进制文件，但运行的是:

```
~ ruby -e 'require "rbconfig"; pp RbConfig::CONFIG' | grep "host"
```

这显示了一些有趣的东西。

```
 "host_os"=>"darwin20",
 "host_vendor"=>"apple",
 **"host_cpu"=>"x86_64",**
 "host"=>"x86_64-apple-darwin20",
 "host_alias"=>"",
```

除了清单第 3 行的`host_cpu`字段中的`arm64`或`arm64e`之外，一切似乎都很好，因为我们在 ARM 处理器上运行程序。

**注意:**许多程序似乎使用并依赖于 [RbConfig](https://www.rubydoc.info/github/rubyworks/facets/RbConfig) 模块来获取关于它们正在运行的系统的信息。

在那之后，很明显问题不在于 FFI 库本身，而在于苹果自己提供的 Ruby 安装中更深层的东西。

# 通用二进制

有了这些新的见解，我怀疑通用二进制没有为它运行的架构选择正确的二进制部分。为了验证这一点，我想确保我的测试是用 ARM Ruby 二进制文件运行的——但是如何做到这一点呢？

Lipo 工具还有另一个有趣的特性:你可以把一个通用的二进制文件精简成它的架构二进制部分。

Lipo 的主页上写道:

> ”`*thin arch_type*`
> 
> 取一个输入文件，用指定的`*arch_type*`创建一个瘦输出文件。该命令需要`*-output*`选项。"

所以我把苹果的二进制文件变薄，试着去掉 ARM 部分，以确保我只使用 ARM 代码。

运行这个:

```
~ lipo -thin arm64e -output ruby-thin /usr/bin/ruby
```

…生成了一个新文件(`ruby-thin`)，看起来是纯 ARM 二进制代码:

```
~ lipo -archs ruby-thin
arm64e
```

但是使用这个新的二进制文件再次运行之前的相同测试:

```
~ ./ruby-thin -e 'require "rbconfig"; pp RbConfig::CONFIG' | grep "host"
```

…仍然导致:

```
"host_os"=>"darwin20",
"host_vendor"=>"apple",
**"host_cpu"=>"x86_64",**
"host"=>"x86_64-apple-darwin20",
"host_alias"=>"",
```

而且它还在识别主机 CPU 为`x86_64`！

# 走漫长的路

我不是 Ruby 开发人员，对这种语言本身也没有什么经验，这只会让事情变得更加困难，让我更加束手无策。

我想到了自己编译 Ruby 的想法，因为这应该是获得 ARM 二进制文件的确定方式，所以我下载了源代码并开始制作自己的 Ruby 二进制文件。

我不希望生成的二进制文件安装在系统上，所以我只运行:

```
~ ./configure && make
```

这产生了一个新的 Ruby 二进制文件。

对二进制文件的分析表明，它是一个纯 ARM64 二进制文件:

```
~ ./ruby -v
ruby 3.0.0p0 (2020-12-25 revision 95aff21468) [arm64-darwin20]~ lipo -detailed_info ruby
input file ruby is not a fat file
Non-fat file: ruby is architecture: arm64
```

不幸的是，运行这个二进制文件没有我想象的那么容易，并导致了一个错误:

```
~ ./ruby -e 'require "rbconfig"; pp RbConfig::CONFIG' | grep "host"
`RubyGems' were not loaded.
`did_you_mean' was not loaded.
-e:1:in `require': cannot load such file -- rbconfig (LoadError)
from -e:1:in `<main>'
```

Ruby 运行起来更复杂，并且需要以我想避免的方式安装。

然而，还有另一个线索:我在编译二进制文件的目录中发现了`rbconfig.rb`文件。我发现这个文件负责`rbconfig`模块读取和显示的值，因为它包含了许多程序使用的所有键/值对。

在之前编译的 Ruby 的`rbconfig.rb`文件里面，我发现:

```
~ ruby-3.0.0 cat rbconfig.rb | grep "_cpu"CONFIG["target_cpu"] = "arm64"
CONFIG["host_cpu"] = "arm64"
CONFIG["build_cpu"] = "aarch64"
```

这不仅是另一条线索，而且似乎是我所有未决问题的最终答案！

# 结论 aka TL 速度三角形定位法(dead reckoning)

所以最后的结论比我最初想的要简单得多。问题出在`rbconfig.rb`本身，它是在编译时构建的。

通用二进制文件或多或少只是两个独立二进制文件的组合，因此每个二进制文件都需要一个独立的`rbconfig.rb`配置文件。当然，`rbconfig`模块和配置既不支持也不打算这样做，当然也永远不会支持。

这个问题似乎没有最终的解决方案——除了安装一个定制的 Ruby 安装。

在我发现的几天后，我收到了 GitHub 上的一条评论:

[](https://github.com/ffi/ffi/issues/870#issuecomment-756989318) [## 1 . 14 . 2:M1 Mac mini 上的 ffi_c.bundle 中缺少兼容 arch，系统 Ruby 问题#870 …

### 我正试着用这个指南在我的 M1 mac Mini 上本地建立我的 Github Pages 网站。现在，我试着创造一个…

github.com](https://github.com/ffi/ffi/issues/870#issuecomment-756989318) 

所以一直以来，苹果都在计划弃用 Ruby 二进制程序。

有了这些信息，几乎可以肯定永远不会有修复，我们开发人员需要自己关心 Ruby。