# 编写健壮的 Bash 脚本的 10 个好技巧

> 原文：<https://betterprogramming.pub/10-cool-tips-for-writing-robust-bash-scripts-7333531ca5fb>

## 工作多年后，我收集了一些有用的建议和模板，我总是在我的项目中重用它们来编写稳定、可移植和可读的 Bash 脚本

![](img/855438644ed5679c2763f8824db67297.png)

克里斯蒂安·斯特兰德在 [Unsplash](https://unsplash.com/s/photos/type?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

无论您是在运行 Linux、macOS 甚至 Windows 的本地计算机上实现自动化，还是远程实现自动化，比如构建过程、cron 作业和 docker 构建，大多数开发人员都需要脚本。然而，有时发现脚本失败或脚本失败的地方并不简单。因此，在脚本中集成健壮性至关重要。通常，脚本需要从一个系统、用户、容器移动到另一个位置，然后突然失败。

当谈到脚本时，我们应该区分两种用例:

1.  操作系统的交互式命令行界面(称为 REPL-读取-评估-打印-循环)。参见[1]。
2.  用于编译、部署、更新或做其他事情的自动化脚本。

对于交互式 REPL，我推荐 zsh 和 [oh-my-zsh](https://github.com/ohmyzsh/ohmyzsh) 配置框架。对于自动化，我推荐使用 Bash，因为它可以在许多不同的系统上运行，甚至可以在容器中运行。本文主要关注后一种用例。

可以使用以下 bash 变体——Windows[git-Bash](https://gitforwindows.org/),它有效地使用了 [MSYS2](https://www.msys2.org/) 的一部分——MAC OS 集成 Bash(注意:它通常是一个古老的版本),或者使用[自制软件](https://brew.sh/)进行更好的更新。所有的 Linux 变种通常都配有 Bash，或者可以简单地安装。

# 提示#1:使用稳定的东西

Shebang 是以`#!`开头的脚本文件中的第一行，调用解释器读取它以知道调用哪个可执行文件。许多 shell 脚本使用 shebang 头`#!/bin/sh`。但是要小心:`/bin/sh`可能是指向 POSIX shell、Bash 或 Dash 或其他 shell 的符号链接。这取决于当前的系统外壳。因此，将自己的脚本依赖于`/bin/sh`并不是一个好主意；如果你不是系统的一部分！所以把`#!/bin/bash`放在你文件的开头。然而，一些系统在`/usr/bin/bash`中有 Bash，上面的 shebang 将不工作。另一种可能是使用`/usr/bin/env`工具，它根据当前搜索`$PATH`搜索一个二进制文件并返回第一个匹配。那么 shebang 就是`#!/usr/bin/env bash`。有一场关于这样做是否是个好主意的激烈讨论。如果你写系统脚本，那么在 shebang 行写解释器的绝对路径，不要使用`env`命令。

# 技巧 2:让脚本可重定位

通常，您会将脚本与其他文件和其他相关脚本放在一个文件夹结构中。我希望脚本使用**相对路径**来找到相对于自己路径的依赖数据。因此，找出当前脚本的路径至关重要。第一种可能是使用`readlink`实用程序:

```
SCRIPT_PATH=$(dirname "$(readlink -f "${BASH_SOURCE[0]}")")
```

`-f`开关将路径规范化并遵循符号链接。

不幸的是，macOS 没有支持`-f`开关的 gnu 兼容的`readlink`工具，您可以使用下面的解决方法(您可以在所有系统上使用它):

```
SCRIPT_PATH="$(cd "$( dirname "${BASH_SOURCE[0]}" )" && pwd -P)"
```

或者，你可以在 macOS 上安装带有[自制软件](https://brew.sh/)的`coreutils`。但是接下来您需要在您的`~/.profile`中添加别名来将`readlink`、`id`、`uname`、`env`映射到相应的`greadlink`、`gid`、`guname`和`genv`工具，如下所示:

```
# install with 'brew install coreutils'
if which greadlink > /dev/null ; then
    echo "Installing coreutils aliases"
    alias readlink=greadlink
    alias id=gid
    alias uname=guname
    alias env=genv
fi
```

有一个真正的[硬栈溢出讨论](https://stackoverflow.com/questions/1055671/how-can-i-get-the-behavior-of-gnus-readlink-f-on-a-mac)，有许多疯狂的解决方法，使用 a.o. Python 脚本和 C 程序来解决这个问题。然而，我会在这部分推荐自制或系统中立的解决方案。

# 技巧 3:检查错误

当一个命令有一个非零的退出代码，并且在此之后将执行更多的命令时，可能会发生不好的事情！错误检查总是必不可少的。你想知道你的 cron 工作失败了吗！

## 出错时中止

为此，激活选项`set -o errexit`(相当于`set -e`)。这种模式在命令失败时退出 Bash 脚本，并且不使用`if`语句进行显式检查。

```
set -o errexit
ls /not/existent # --> exits the script with an error codeset -o errexit
# does not abort
if ls /not/existent ; then
    echo "exists"
else
    echo "does not exist"
fi
```

## 显示错误位置

但是现在我们只知道脚本失败是因为它的返回代码。然而，找出失败的原因是很好的。因此，我们安装了一个陷阱处理程序来显示当前行:

```
#!/usr/bin/env bash
set -o errexit
set -o nounset
SCRIPT_PATH="$(cd "$( dirname "${BASH_SOURCE[0]}" )" && pwd -P)"
function die() {
  echo "ERROR $? IN ${BASH_SOURCE[0]} AT LINE ${BASH_LINENO[0]}" 1>&2
  exit 1
}
trap die ERR
```

## 在 Bash 中创建一个堆栈转储

除此之外，您甚至可以为 Bash 创建一个堆栈转储。如果创建更复杂的函数层次结构，这可能会很有趣。

```
function log_stack()
{
     local i=0
     local FRAMES=${#BASH_LINENO[@]}
     # FRAMES-2 skips main, the last one in arrays
     for ((i=FRAMES-2; i>=0; i--)); do
         echo "  File \"${BASH_SOURCE[i+1]}\", line ${BASH_LINENO[i]}, in ${FUNCNAME[i+1]}"
         # Grab the source code of the line
         #sed -n "${BASH_LINENO[i]}{s/^/    /;p}" "${BASH_SOURCE[i+1]}"
     done
     return 0
}
```

您可以将对`log_stack()`函数的调用添加到`die()`函数中，如下所示:

```
function die() {
    echo "ERROR $? IN ${BASH_SOURCE[0]} AT LINE ${BASH_LINENO[0]}" 1>&2
    log_stack
    exit 1
}
```

# 技巧 4:检查未定义的变量

当使用未定义的变量时，可能会发生灾难。为此，您可以激活`set -o nounset`选项。每当您使用一个未定义的变量时，脚本都会以一个错误结束。有时您想使用环境变量作为脚本的输入。如果变量丢失，你肯定不喜欢失败。为了指定一个**默认值**，可以使用以下语法:

```
set -o nounset

# here we set VERBOSE to "0" if it is not defined!
VERBOSE="${VERBOSE:-0}"

if [[ "$VERBOSE" = "1" ]] ; then
    echo "Oh verbose is on!"
fi
```

# 技巧 5:逃生路径

脚本中的一个问题通常是传递包含空格的参数和路径。因此，我建议—始终使用`"`转义路径，如:

```
inputFile="$1"
ls "$inputFile"
```

如果你有一个`rm -rf`命令，不逃避争论可能是绝对危险的。观察`rm -rf "/ my /file"`和`rm -rf / my /file`。后者将删除系统中的所有内容，而前者将从`my`目录中删除`file`。

# 技巧 6:使用 Bash 数组进行更清晰的调用

对于具有许多参数的更复杂的命令，我建议使用 Bash 数组来组合多个选项，如下面的 docker 示例所示:

```
set -o errexit
set -o nounset
SCRIPT_PATH="$(cd "$( dirname "${BASH_SOURCE[0]}" )" && pwd -P)"
version="$(git -C "$SCRIPT_PATH" describe --tags --always)"
version="${version#v}"
commit="$(git -C "$SCRIPT_PATH" rev-parse HEAD)"
container="my-app"

## HERE we create an array of options
opts=()
opts+=(--tag "$container":latest)
opts+=(--tag "$container":"$version")
opts+=(--build-arg VERSION="$version")
opts+=(--build-arg COMMIT="$commit")
opts+=(--file "$SCRIPT_PATH/${container}/Dockerfile")
docker build "${opts[@]}" .
```

**注意**:对 docker 的调用需要对 opts 数组进行转义`"${opts[@]}"`。

# 技巧 7:激活健壮的 Globbing

当 for 循环迭代一个空的 globbing 表达式时，循环和整个脚本都会因出错而中止。为了使它更加健壮，您可以激活 **null globbing** 。那么当表达式为空时，for 循环将**而不是**被执行(在这种情况下`/tmp`中不存在 txt 文件)。

```
shopt -s nullglob
for f in /tmp/*.txt ; do
    echo "$f"
done
```

# 技巧 8:解析命令行参数

对于 Bash 脚本中命令行的简单解析，我推荐以下结构。

```
function usage() {
cat <<EOF
Usage $0 OPTIONS
Options:
    --version=N  sets the version
    -d           start in daemon mode
    --help, -h   this help
EOF
}

# parse arguments
version=""
daemon=0
args=()
while [[ $# -gt 0 ]] ; do
    case "$1" in
    -v=*|--version=*)
        version="${1#*=}"
        ;;

    -d|--daemon)
        daemon=1
        ;;

    -h|--help)
        usage
        exit 0
        ;;
    --)
        # forward all arguments after `--` to the command
        shift
        args+=("${@}")
        break
        ;;
    *)
        echo "ERROR: unknown option $1"
        exit 1
        ;;
    esac
    shift
done
# OK do here something with "${args[@]}"
```

# 技巧 9:使用字符串函数

在进入`awk`或`tr`之前，您可以使用 Bash 进行现成的简单字符串处理。一个不太为人所知的特性是大写和小写语法。###大写和小写使用`^`字符您可以将变量`x`的第一个字母大写，就像`${x^}`一样，使用`^^`所有字符都是大写的`${x^^}`，使用`,`和`,,`小写也是一样(所以不要忘记 Bash 在使用时是直观和漂亮的)。

小写转换

```
x="HELLO"
echo ${x,,}
# result: hello
```

大写转换

```
x="hello"
echo ${x^^}
# result: HELLO
```

[](https://linuxhint.com/bash_lowercase_uppercase_strings/) [## Bash 小写和大写字符串

### 通过使用 Bash 4 的新特性，可以更容易地转换字符串的大小写。'^'符号用于转换…

linuxhint.com](https://linuxhint.com/bash_lowercase_uppercase_strings/) 

# 提示#10:使用 ShellCheck

显然，使用 [ShellCheck](https://www.shellcheck.net/) 工具可以避免很多问题，该工具可以集成到您喜欢的 IDE 中，例如 Visual Studio 代码。

我在这里提供了一个创建 bash 脚本的小模板:

我希望这篇文章对你有用。

## 推荐阅读

[1] A. Müller，“您应该学习使用命令行的原因”，2022 年 3 月 10 日。[https://medium . com/@ jumping kiwi/reasons-why-you-should-learn-to-use-the-command-line-91823128 c0ad](https://medium.com/@jumpingkiwi/reasons-why-you-should-learn-to-use-the-command-line-91823128c0ad)(2022 年 04 月 02 日访问)。

【迪伦】 [*纯粹痛击圣经*](https://github.com/dylanaraps/pure-bash-bible) 。2022.

如果我正在编写 bash 脚本，我为什么要关心 POSIX 呢？— Unix & Linux 堆栈交换