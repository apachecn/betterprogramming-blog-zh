# 使用 Conan:一个开源的 C/C++包管理器

> 原文：<https://betterprogramming.pub/use-conan-an-open-source-c-c-package-manager-678bf2b0e6d>

## 管理您的 C/C++包可能会很麻烦，但是它可以得到显著的改善

![](img/62680b8bf7cef2e11c580d0dd3f75034.png)

照片由[Dawid za wia](https://unsplash.com/@davealmine?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral) 上拍摄。

在这篇文章中，我将介绍 C/C++中的包管理问题，以及 Conan 如何帮助您解决这些问题。

# 从简单开始

让我们从一个简单的任务开始，从命令行参数中获取值，并将它们打印到标准输出中:

```
+root
    - mainapp.c
    - CMakeLists.txt
```

mainapp.c

CMakeLists.txt

我们使用标准库中的 [getops](https://www.gnu.org/software/libc/manual/html_node/Getopt.html) 函数来获取参数`-a`和`-b`及其相关值，然后打印它们。

然后我们使用 [CMake](https://cmake.org/) 将项目编译成一个名为`myproject`的可执行文件。

让我们配置这个项目:

```
cmake --build ./build --config Debug --target MyProject -j 10 --
```

现在让我们运行应用程序:

```
./build/MyProject -a foo -b bar
```

输出是:

```
a: foo
b: bar
```

这是可行的，但是“可行”并不总是足够的。
Getops 函数使用全局变量，例如`optarg`来存储参数关联值，`optopt`用于存储变量名，`optind`用于存储索引。

这意味着该函数不是线程安全的，您只能在一个线程中解析一个输入向量。

如果您有多个输入向量，您需要顺序地解析这些向量，更糟糕的是，您每次都必须重置解析器的状态。

getops 的另一个众所周知的问题是内部隐藏的错误消息，这使得重定向变得困难。

因此，为了解决这个问题，我决定使用 [parg](https://github.com/jibsen/parg/blob/master/parg.h) ，一个开源的 C 库，通过分配一个 struct 并使用它来监视解析器的状态，而不是全局变量，来解析具有线程安全性的参数。

# 但是我该如何使用这些代码呢？

## 备选方案 1。消费源代码

我们可以使用 [gits 子模块工具](https://git-scm.com/book/en/v2/Git-Tools-Submodules)将 parg 克隆到我们的项目目录，然后使用 cmake 编译并链接到我们的可执行文件。

```
git submodules add [https://github.com/jibsen/parg.git](https://github.com/jibsen/parg.git)
```

这将创建包含以下内容的文件`.gitsubmodules`:

```
[submodule "parg"]path = pargurl = https://github.com/jibsen/parg.git
```

现在我们可以获取回购及其源代码:

```
git submodules init
```

我们在`parg`目录中有所有的`parg`源代码。

现在我们需要将它添加到我们的`CMakeLists.txt`配置中:

我们已经添加了目录并将库链接到我们的可执行文件。
现在我们可以修改代码来使用`parg`库:

现在我们的代码是线程安全的，如果需要，我们可以很容易地解析多个参数向量。

我们来编译一下:

```
cmake --build ./build --config Debug --target MyProject -j 10 --
```

运行:

```
./build/MyProject -a foo -b bar
```

输出是:

```
a: foo
b: bar
```

添加源代码的主要限制是，每次编译可执行文件时，我们都需要编译它，这会导致编译时间延长。

## 选项 2。消费编译库

在这个选项中，我们将`parg`编译一次，保存库，然后直接链接库，不需要再次编译。

编译我们的`parg`项目将会创建文件`libaprg.a`。

现在，我们可以对 CMakeLists 和 C 代码进行一些修改以使其工作:

CMake:

现在是 C 代码:

对 C 代码的唯一更改是，现在头文件放在了链接库中。

在 CMakeLists 文件中，我们已经移除了`add_subdirectories`，取而代之的是，我们已经从它的路径链接了编译后的库，这样我们就不会在每次编译应用程序时都编译它。

现在让我们编译并再次运行。

```
cmake --build ./build --config Debug --target MyProject -j 10 --
```

运行:

```
./build/MyProject -a foo -b bar
```

输出是:

```
a: foo
b: bar
```

有用！更好，但还可以更好！

这种方法的问题是，它污染了工作环境，并且当您有许多依赖项时，您的依赖项有第二层甚至第三层依赖项，这变得非常难以管理。

## 选项 3。用柯南来大声哭吧！

[柯南](https://conan.io/)是 JFrog 开发的开源 C\C++包管理器。
它使用 JFrog artifactory 来管理依赖关系。
它主要由其软件即服务(SaaS)平台使用，但也可以用作公司内部需求的本地存储库。

作为先决条件，您需要安装 [Python](https://www.python.org/) 。

首先，我们需要使用以下命令从 [pip](https://pypi.org/project/conan/) 安装柯南:

```
pip install conan
```

现在我们需要为我们的配置创建一个配置文件:

```
conan profile new myprofile
```

输出将是:

```
Empty profile created: C:\Users\user\.conan\profiles\myprofile
```

现在我们可以转到概要文件，根据需要设置配置。
在我的例子中，我使用了以下代码:

```
[env]CC=C:/Program Files (x86)/mingw-w64/i686-8.1.0-posix-dwarf-rt_v6-rev0/mingw32/bin/gcc.exeCXX=C:/Program Files (x86)/mingw-w64/i686-8.1.0-posix-dwarf-rt_v6-rev0/mingw32/bin/g++.exe[settings]os_build=Windowsarch_build=x86os=Windowsarch=x86compiler=gcccompiler.version=8compiler.libcxx=libstdc++11build_type=Release
```

这将设置编译项目所需的所有路径和变量。

设置好概要文件后，现在我将在项目目录中创建`conanfile.txt`。
查看柯南中心的 [parg 库](https://conan.io/center/parg)，我可以找到我需要的所有细节。

```
[requires]
    parg/1.0.2[generators]
    cmake
```

现在我的项目依赖于`parg`，柯南知道这一点，所以应该为 cmake 提供它。

现在我可以运行柯南安装命令了:

```
conan install . -pr=myprofile
```

输出:

```
Configuration:
[settings]
arch=x86
arch_build=x86
build_type=Release
compiler=gcc
compiler.libcxx=libstdc++11
compiler.version=8
os=Windows
os_build=Windows
[options]
[build_requires]
[env]
CC=C:/Program Files (x86)/mingw-w64/i686-8.1.0-posix-dwarf-rt_v6-rev0/mingw32/bin/gcc.exe
CXX=C:/Program Files (x86)/mingw-w64/i686-8.1.0-posix-dwarf-rt_v6-rev0/mingw32/bin/g++.exe
conanfile.txt: Installing package
Requirements
    parg/1.0.2 from 'conancenter' - Cache
Packages
    parg/1.0.2:a955db98e980a5ab86ae50d6df8bfee361185c27 - CacheInstalling (downloading, building) binaries...
parg/1.0.2: Already installed!
conanfile.txt: Generator txt created conanbuildinfo.txt
conanfile.txt: Generator cmake created conanbuildinfo.cmake
conanfile.txt: Aggregating env generators
conanfile.txt: Generated conaninfo.txt
conanfile.txt: Generated graphinfo
```

酷！

现在让我们给`CMakeLists.txt`文件添加一些修改，这样它将获得包含这些依赖项的库:

所以我们包含了来自 sources 目录的构建信息(它是由 Conan install 创建的)并链接了库。

现在我们可以使用我们在第一个例子中使用的相同的 C 代码，如下所示:

不同的是，现在我们不会用 git 子模块污染我们的代码，我们也不必每次都编译代码。同样，我们不再需要自己管理依赖关系和工件。

现在让我们再次编译并运行这段代码:

```
cmake --build ./build --config Debug --target MyProject -j 10 --
```

运行:

```
./build/MyProject -a foo -b bar
```

输出是:

```
a: foo
b: bar
```

太棒了。