# 在 C 和 C++项目中成功使用 vcpkg 的技巧

> 原文：<https://betterprogramming.pub/tricks-for-successfully-using-vcpkg-in-c-and-c-projects-b775cc836d81>

## 了解如何有效地使用 vcpkg 来管理库依赖项

![](img/fb33678ca99b1ab01cd8e22ec484e966.png)

照片由[克劳迪奥·施瓦茨](https://unsplash.com/@purzlbaum?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/package-truck?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

# 介绍

今天，现代语言(例如 [Go](https://golang.org) )经常提供集成的包管理来提取库的所有依赖项。然而，许多软件是用 C/C++创建和维护的，没有现成的包管理器。

将一个软件移植到另一个目标平台(macOS、Windows、Linux)通常非常困难。有帮助的是，有第三方的包管理器。其中一个叫做 [vcpkg](https://vcpkg.io) ，是微软提供的开源项目。在续集中，我展示了一些技巧，涵盖了 vcpkg 中的一些困难。

*从源代码下载、修补和编译 vcpkg 中的所有包。所以，当使用大型库时，比如 boost、ITK 或 OpenCV，这需要一些时间！*

# vcpkg 入门

首先，通过使用 [Git](https://git-scm.com/) 克隆存储库来检查 vcpkg 的最新版本。除此之外，你还需要一个 C++编译器(GCC、MinGW、clang、Visual Studio cl、Xcode)，具体取决于你的平台(参见[VC pkg](https://vcpkg.io/en/getting-started.html)入门)和 [CMake](https://cmake.org/) 。在 macOS 上，我推荐[自制](https://brew.sh/)的依赖项:

```
brew install cmake
```

现在您可以安装 vcpkg:

```
git clone https://github.com/Microsoft/vcpkg.git
cd vcpkg
./bootstrap-vcpkg.sh
```

如果您想要减小存储库的大小(这样您就不会看到旧的历史)，那么您可以将选项`--depth=1`添加到 clone 命令读数中

```
git clone --depth=1 [https://github.com/Microsoft/vcpkg.git](https://github.com/Microsoft/vcpkg.git)
```

更新 vcpkg 现在很容易:调用 vcpkg 安装路径中的`git pull`。但是当你着急的时候，要小心不要那样做。在这一步之后，您的项目和依赖的 vcpkg 库需要重新编译！

建议保持 vcpkg 安装目录的路径简短并且没有空格(例如，`/opt/vcpkg`或`C:\opt\vcpkg`)，特别是对于 Windows，因为有些包(boost)具有相当深的层次结构。

我们将在续集中调用 vcpkg 安装目录`VCPK_ROOT`。以下内容将使使用更简单、更便携:

*   创建一个指向 vcpkg 安装目录的环境变量`VCPK_ROOT`。
*   请在您的 CMake 文件中参考有利于硬编码路径的内容。

或者，您可以通过以下对 CMake 的调用来安装 CMake: `cmake -B cmake-build -DCMAKE_TOOLCHAIN_FILE=<VCPKG_ROOT>/scripts/buildsystems/vcpkg.cmake`。所以你最好使用我提供的 [vcpkg 模板](https://github.com/andremueller/vcpkg-template)。

然后，将 vcpkg 安装目录添加到您的搜索中`PATH`。

# 使用 vcpkg 命令行工具

使用库的最重要的 vcpkg 命令是:

`vcpkg help <command>`在命令行中显示一些帮助

`vcpkg search`用于查找库(称为端口)

`vcpkg install`用于安装库

一个例子是:

```
vcpkg search catch
```

它返回

```
catch                    alias#1          Deprecated alias for Catch2 unit testing framework
catch-classic            1.12.2#1         A modern, header-only test framework for unit tests
catch2                   3.0.1#1          A modern, header-only test framework for unit testing.
The result may be outdated. Run `git pull` to get the latest results.If your port is not listed, please open an issue at and/or consider making a pull request:
    [https://github.com/Microsoft/vcpkg/issues](https://github.com/Microsoft/vcpkg/issues)
```

如果您决定使用`catch2`，您可以使用以下命令安装该软件包:

```
vcpkg install catch2
```

该库将被编译并安装在您的`VCPKG_ROOT`中，从而登陆到`$VCPKG_ROOT/packages/<LIBRARY>_<TRIPLET>`中。在安装命令的末尾，会出现一个用法提示:

```
catch2 provides CMake targets:
    # this is heuristically generated, and may not be correct
    find_package(Catch2 CONFIG REQUIRED)
    target_link_libraries(main PRIVATE Catch2::Catch2 Catch2::Catch2WithMain)
```

这有时就是问题所在。如果不检查已安装的包，可能会产生误导，并且不清楚哪些 CMake 目标可以在`target_link_libraries`中使用。

什么是三胞胎？

三元组反映编译的目标系统(见[三元组文件](https://github.com/microsoft/vcpkg/blob/master/docs/users/triplets.md))。对我来说，是`x64-osx`。另一个三元组是例如`x64-windows-static`。调用`vcpkg help triplet`列出可能的值。

当你对默认的三个一组不满意时，你可以通过在你的 CMake 文件中指定`VCPKG_TARGET_TRIPLET`来控制`vcpkg install`将会做什么，或者像这样安装带有`--triplet`选项的包(以下内容在 macOS 上不起作用):

```
vcpkg install catch2 --triplet x64-windows
```

`-static`三元组创建了一个库的静态构建版本。

# 安装模式

安装包有两种不同的模式:在经典模式下，可以通过调用`vcpkg install <port-name>`全局安装新的库，如上一节所示。在清单模式下，您需要在项目目录中创建一个`vcpkg.json`文件，列出所有依赖项。详见[清单模式](https://github.com/microsoft/vcpkg/blob/master/docs/users/manifests.md)。

这有一个很大的优势，即项目所需的所有信息都在一个地方，并且在编译项目时会自动下载和安装所需的包。这类似于 Go mod 文件。以下是我的 [vcpkg 模板](https://github.com/andremueller/vcpkg-template)中的小`vcpkg.json`文件:

```
{
  "name": "vcpkg-template",
  "version-string": "0.0.1",
  "dependencies": ["catch2"]
}
```

# 清理库

现在当你通过调用`git clean -dxf`清理`VCPKG_ROOT`目录时，所有安装的库和下载的文件都将消失，需要重新安装。除此之外，在我的系统(macOS)上，vcpkg 在`$HOME/.cache/vcpkg`下创建缓存文件，必须删除这些文件才能进行清理。

在清单模式下，清理项目目录也会移除所有已安装的库。所以在下一个版本中，一切都将被重新编译。

# 控制 vcpkg 版本

可以使用 CMake 变量控制 vcpkg 的行为，这些变量可以是:

*   在`CMakelists.txt`文件中设置(在`project()`子句之前)或
*   在命令行上用`cmake -D <VARIABLE>=<VALUE>`定义，或者
*   像`<VARIABLE>=<VALUE> make -C`一样设置为环境变量

以下变量对于目标架构和链接过程(静态或动态)是最重要的:`VCPKG_TARGET_TRIPLET` -控制构建的目标，例如`x64-windows-static`更多信息请参见 [CMake Integration](https://github.com/microsoft/vcpkg/blob/master/docs/users/buildsystems/cmake-integration.md) 。

# 包装的五个维度

在 vcpkg 中，使用库需要知道不同的名称。这并不总是简单的，需要一点搜索！

所以有:

1.`vcpkg.json`依赖部分或`vcpkg install <PORT>`命令中的 vcpkg 端口名称字段:

```
{  
    "dependencies": [ "boost-asio" ]
}
```

请注意，端口名不得包含除`[a-zA-Z]`和破折号`-`之外的其他字符。

2.在`vcpkg.json`中选择的特征看起来像这样:

```
{
    "dependencies": [
        {  
         "name": "opencv",  
         "features": [ "png" ]
        }
    ]
}
```

3.由`find_package(<PKG> REQUIRED)`找到的 CMake 包名

4.`find_package(<PKG> REQUIRED COMPONENTS <COMP1> <COMP2>)`中的 CMake 组件名

5.`target_link_library(myApp PRIVATE <DEP1> <DEP2>`依赖目标名称。除此之外有时还必须设置`target_include_directories()`！

这种差异的一个例子是以下情况:

*   vcpkg 端口名:`boost-asio`
*   CMake 包名:`find_package(boost_asio CONFIG REQUIRED)` -要为链接`target_link_library(myApp PRIVATE Boost::asio)`找到的目标别名`Boost::asio`。谷歌后，你发现你可能需要与`${Boost_LIBRARIES}`链接，这不是来自于增强端口使用帮助。

当 CMake 运行时，一些软件包会发送或多或少有用的消息，试图支持用户如何像这样包含特定的库:

```
The package boost is compatible with built-in CMake targets: find_package(Boost REQUIRED [COMPONENTS <libs>...])
    target_link_libraries(main PRIVATE Boost::boost Boost::<lib1> Boost::<lib2> ...)
```

但是你不会轻易找到目标的名字！有趣的是，您会在`ports`目录中找到一个用法文件，它显示了`target_link_libraries()`的正确方法。

```
/opt/vcpkg/ports/boost> cat usage
The package boost is compatible with built-in CMake targets:

    find_package(Boost REQUIRED [COMPONENTS <libs>...])
    target_link_libraries(main PRIVATE ${Boost_LIBRARIES})
    target_include_directories(main PRIVATE ${Boost_INCLUDE_DIRS}) find_package(Boost REQUIRED [COMPONENTS <libs>...])
    target_link_libraries(main PRIVATE ${Boost_LIBRARIES})
    target_include_directories(main PRIVATE ${Boost_INCLUDE_DIRS})
```

还有另一篇很好的文章(参见[如何使用 CMake 查找包:基础知识](https://izzys.casa/2020/12/how-to-find-packages-with-cmake-the-basics/))解决了在没有 vcpkg 的情况下在 CMake 中查找包的一般问题。一件事是检查 vcpkg 安装目录中的`Find<Package>.cmake`文件。

然而，boost 库中的 CMake 文件要比这复杂得多。这些包并不都是同质和简单的。好的一面是，所有具有良好的本地 CMake 支持的包通常更容易添加。一个例子是用于 JSON 流的伟大的`nlohmann-json`库。

这里安装后的用法打印非常有用:

```
The package nlohmann-json provides CMake targets:

    find_package(nlohmann_json CONFIG REQUIRED)
    target_link_libraries(main PRIVATE nlohmann_json::nlohmann_json)

The package nlohmann-json can be configured to not provide implicit conversions via a custom triplet file:

    set(nlohmann-json_IMPLICIT_CONVERSIONS OFF)

For more information, see the docs here:

    [https://json.nlohmann.me/features/macros/#json_use_implicit_conversions](https://json.nlohmann.me/features/macros/#json_use_implicit_conversions)
```

# 信息来源

所以问题是:

> 我从哪里得到这些名字的？

端口名可以在网上找到[https://vcpkg.io/en/packages.html](https://vcpkg.io/en/packages.html)或者更好的[https://vcpkg.info/](https://vcpkg.info/)，或者使用 vcpkg 命令行实用程序`vcpkg search <name>`。一旦安装了端口`<PORT>`(我们假设您使用`vcpkg install`全局安装了它)，您需要检查`$VCPKG_ROOT/ports/<PORT>/portfile.cmake`文件，就像下面的`boost-asio`文件一样:

```
# Automatically generated by scripts/boost/generate-ports.ps1

vcpkg_from_github(
    OUT_SOURCE_PATH SOURCE_PATH
    REPO boostorg/asio
    REF boost-1.78.0
    SHA512 78c58a64d669eaeabb5ba003200c581065412d33912e641143186ee95c11e0fb0411ed8dbb9a9acced8c8ecd258e0de33872b2e22dfc4a572315cd9a665db8a6
    HEAD_REF master
    PATCHES windows_alloca_header.patch
)

include(${CURRENT_INSTALLED_DIR}/share/boost-vcpkg-helpers/boost-modular-headers.cmake)
boost_modular_headers(SOURCE_PATH ${SOURCE_PATH})
```

除了查找可能的特性名称，您还可以查看这个包的`$VCPKG_ROOT/ports/<PORT>/vcpkg.json`文件:

出于好奇，您可以通过调用以下命令列出`boost-asio`的所有依赖项:

```
vcpkg depend-info boost-asio
```

# 默认功能

当在`vcpkg.json`中仅指定端口名称时，安装的功能是所谓的默认功能。可通过设置`dependencies`部分的`vcpkg.json`文件中的`"default-features": false`选项来禁用此行为。因此，`vcpkg.json`文件可能是这样的:

```
{
  "name": "vcpkg-template",
  "version-string": "0.0.1",
  "dependencies": [
    {
        "name": "boost",
        "default-features": false
    }
  ]
}
```

参见[https://VC pkg . readthedocs . io/en/latest/users/selecting-library-features/](https://vcpkg.readthedocs.io/en/latest/users/selecting-library-features/)。

# 结论

在本文中，我给出了一个使用 vcpkg 的简短调查，并提供了一些提示和技巧。vcpkg 的用法并不总是那么简单。我为新项目创建了一个小的 vcpkg 模板。

您可以在 GitHub 上检查并重用我的 [vcpkg 模板，以获得 C++项目的基本布局。所以，只要`git clone https://github.com/andremueller/vcpkg-template`就出发了。](https://github.com/andremueller/vcpkg-template)

另一个有趣的主题是 vcpkg 的版本支持(参见[通过版本支持控制你的 vcpkg 依赖关系](https://devblogs.microsoft.com/cppblog/take-control-of-your-vcpkg-dependencies-with-versioning-support/))。还有 vcpkg 的替代品比如[柯南](https://conan.io/)或者 [build2](https://build2.org/) 。

感谢阅读。敬请期待更多。