# 用终端工具管理混乱的开源库

> 原文：<https://betterprogramming.pub/manage-your-messy-open-source-repository-with-terminal-tools-bab9bbec43cc>

## 一个方便的终端工具箱，帮助开源维护者让他们的项目闪闪发光

![](img/22589b8de556b1e7444708748a1d1518.png)

作者插图

有效的协作，尤其是在开源软件开发中，始于有效的组织。为了确保不遗漏任何东西，一般规则“一个问题，一个拉取请求”是一个很好的经验法则。

与其像“修复文档中所有断开的链接”那样打开一个大范围的问题，开源项目更有可能通过几个更小、更易管理的问题吸引贡献者。

在前面的示例中，您可以按节或按页确定断开链接的范围。这允许更多的贡献者投入他们的时间，而不是等待一个人承担更大更乏味的贡献工作。

较小范围的问题也有助于项目维护人员看到哪里的工作已经完成，哪里还没有完成。这减少了问题的某些部分被遗漏、被认为已经完成，并在以后导致错误或安全漏洞的机会。

这一切都很好；但是，如果您已经打开了几个大范围的问题，一些 pr 已经提交或合并，而您目前不知道工作从哪里开始或停止，该怎么办？

要想让你的项目回到可控状态，需要做一些整理工作。幸运的是，有许多命令行工具可以帮助您扫描、排序和理解混乱的存储库。

这里是我使用的一个小选择。

跳转到:

*   [与](#interactive-search-and-replace-with-vim)交互搜索并替换`[vim](#interactive-search-and-replace-with-vim)`
*   [用节点模块找到降价文件中的死链接](#find-dead-links-in-markdown-files-with-a-node-module)
*   [用](#list-subdirectories-with-or-without-a-git-repository-with-find) `[find](#list-subdirectories-with-or-without-a-git-repository-with-find)`列出有或没有 git 库的子目录
*   [用](#pull-multiple-git-repositories-from-a-list-with-xargs)T2 从一个列表中拉出多个 git 库
*   [用](#list-issues-by-number-with-jot)和`[jot](#list-issues-by-number-with-jot)`按编号列出问题

# 交互式搜索-用 V 替换`im`

您可以在 [Vim](https://www.vim.org/) 中打开一个文件，然后交互搜索并替换为:

```
:%s/\<word\>/newword/gc
```

`%`表示查看当前文件的所有行；`s`代表替代品；`\<word\>`匹配整个单词；代表“全局”的`g`代表每一次事件。

最后的`c`可以让你在修改前查看和确认每个修改。你可以自动运行它，而且快得多，不用`c`；然而，如果您犯了模式匹配错误，您就将自己置于使事情复杂化的风险之中。

# 用节点模块查找降价文件中的死链接

[markdown-link-check](https://github.com/tcort/markdown-link-check) 节点模块有一个很棒的 [CLI 伙伴](https://github.com/tcort/markdown-link-check#command-line-tool)。

我经常使用它，我把它变成了一个 [Bash 别名函数](https://victoria.dev/blog/how-to-do-twice-as-much-with-half-the-keystrokes-using-.bashrc/#bash-functions)。为此，请将此添加到您的`.bashrc`:

```
# Markdown link check in a folder, recursive
function mlc () {
find $1 -name \*.md -exec markdown-link-check -p {} \;
}
```

然后用`mlc <filename>`跑。

# 用`find`列出有或没有 Git 存储库的子目录

打印作为 Git 存储库的所有子目录，或者换句话说，其中有一个`.git`:

```
find . -maxdepth 1 -type d -exec test -e '{}/.git' ';' -printf "is git repo: %p\n"
```

要打印所有不是 Git 存储库的子目录，用`!`否定测试:

```
find . -maxdepth 1 -type d -exec test '!' -e '{}/.git' ';' -printf "not git repo: %p\n"
```

# 用`xargs`从一个列表中拉出多个 Git 库

我最初用它作为用 Bash 脚本自动重新创建我的笔记本电脑的一部分，但是当你使用云实例或 docker 文件时，它非常方便。

给定一个文件`repos.txt`，每行都有一个存储库的 SSH 链接(并且设置了 SSH 密钥)，运行:

```
xargs -n1 git clone < repos.txt
```

如果您想要拉和推许多存储库，我以前写过关于如何使用 Bash 一行程序来管理您的存储库的文章。

# 用`jot`按编号列出问题

我是 [OWASP Web 安全测试指南](https://github.com/OWASP/wstg)库的合著者和维护者，我最近在那里接受了一个大问题(是的，它是“修复文档中所有损坏的链接”)——你是怎么猜到的？)并把它分成几个更小、更易管理的问题。

总共 37 个更小、更易管理的问题。

我想列举原始问题的所有问题，但是打出 37 个问题号(#275 到#312)的想法似乎非常乏味和耗时。

因此，按照自然程序员的方式，我花了与输入所有这些数字相同的时间，设计了一种自动化的方法。

`jot`实用程序(`apt install athena-jot`)是一个很小的工具，当你想打印出一些数字时，它是个很大的帮助。只要告诉它你想要多少，以及从哪里开始和停止。

```
# jot [ reps [ begin [ end ] ] ]
jot 37 275 312
```

这将在新的一行中打印从 275 到 312 的每个数字。为了使这些成为 GitHub 和许多其他平台自动识别并转化为链接的问题编号符号，您可以将输出通过管道传输到`awk`。

```
jot 37 275 312 | awk '{printf "#"$0", "}'
#275, #276, #277, #278, #279, #280, #281, #282, #283, #284, #285, #286, #287, #288, #289, #290, #291, #292, #293, #295, #296, #297, #298, #299, #300, #301, #302, #303, #304, #305, #306, #307, #308, #309, #310, #311, #312
```

您还可以使用`jot`来生成随机或冗余数据，主要用于开发或测试目的。

# CLI 支持的开源组织

一个组织良好的开源存储库是一个维护良好的开源项目。保存这篇文章作为方便的参考，好好使用你新发现的 CLI 超能力！