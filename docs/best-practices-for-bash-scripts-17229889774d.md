# Bash 脚本的最佳实践

> 原文：<https://betterprogramming.pub/best-practices-for-bash-scripts-17229889774d>

## 可靠、高性能和可重用 Bash 脚本的 4 分钟指南

![](img/b6b4e46356cd6d6d4202ad4f995cca5c.png)

照片由[卢卡·布拉沃](https://unsplash.com/@lucabravo?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/scripts?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

调试 Bash 脚本无异于大海捞针，尤其是在没有及时考虑结构化、日志记录和可靠性的情况下，在现有代码基础上不断增加新代码时。我经常发现自己处于这种情况——有时是由于我自己的错误，有时是在处理复杂的连环车祸时。

遵循一些最佳实践，您肯定可以更好地编写、调试和维护您的脚本。信不信由你，没有什么能比编写干净的、随时可用的 Bash 代码更令人满意了。

在这篇文章中，我想强调一下我在过去几年中学到的东西，以及一些经常让我措手不及的常见错误。这很重要，因为每个软件开发人员，在他们职业生涯的某个阶段，肯定会使用脚本来自动化日常操作任务。

# 了解你的朋友

## 陷阱处理程序

我遇到的大多数 Bash 脚本在脚本执行过程中发生意外时都没有使用有效的清理机制。

外部环境也可能导致意外情况，例如从内核接收信号。处理这种情况对于确保脚本足够健壮以在生产系统中运行是非常重要的。我经常使用退出处理程序来应对这种情况。

Bash 中的陷阱处理程序

`trap` 是一个 shell 内置的，帮助你注册一个清理函数，这个函数在一些`signals`事件中被调用。然而，像`SIGINT`这样的处理程序应该格外小心，防止脚本中止。此外，在大多数情况下，你只需要抓住`EXIT`就可以了，但是这个想法是，你真的可以根据每个信号定制脚本行为。

## 设置“内置—快速失败”

一旦出现错误就及时捕捉并快速失败是非常重要的。没有什么比继续这样做更糟糕的了:

```
rm -rf ${directory_name}/*
```

注意变量名`directory_name`仍然没有定义。

为了处理这样的场景，在脚本开始时使用`set` 内置代码很重要，比如`set -o errexit`、`set -o pipefail`或`set -o nounset` 。这确保了脚本在遇到任何非零退出代码、使用未定义的变量、失败的管道命令等情况时会立即退出。

*“设置”快速故障场景的内置功能*

*注意:像* `*set -o errexit*` *这样的内置代码一旦出现“未处理”返回代码(大于零)，就会退出脚本。因此，引入自定义错误处理就更好了，比如:*

```
#!/bin/basherror_exit() {
 line=$1
 shift 1
 echo “ERROR: non zero return code from line: $line — $@”
 exit 1
}a=0
let a++ || error_exit “$LINENO” “let operation returned non 0 code”
echo “you will never see me”# run it, now we have useful debugging output
$ bash foo.sh 
ERROR: non zero return code from line: 9 — let operation returned non 0 code
```

这将迫使你意识到脚本中所有命令的行为，并在措手不及之前提前处理好这些场景

*(感谢&感谢* [*卢蒙森*](https://medium.com/u/57779bdbe11e?source=post_page-----17229889774d--------------------------------) *分享上面的例子)*

## *'* shellcheck '发现开发过程中的错误

在您的持续集成/测试管道中集成类似于`[shellcheck](https://github.com/koalaman/shellcheck)` 的东西是值得的，它会链接您的 Bash 代码并强制执行最佳实践。

我个人在我的本地开发环境中使用它来获得关于语法、语义和某些我在开发时可能忽略的代码警告的反馈。它是 Bash 脚本的静态分析工具，我强烈推荐使用它。

## 使用自定义退出代码

POSIX 强制返回代码不仅仅是*零*或*一—* ，而是*零*或*非零。*利用这些功能为各种错误情况返回自定义错误代码(在 201-254 之间)。然后，其他脚本(包装您的脚本)可以使用这些信息来准确理解发生了什么类型的错误，并做出相应的反应。

Bash 中的自定义错误代码

*注意:请格外小心你定义的变量名，这样你就不会不小心覆盖了来自系统的环境变量*

## 记录器功能

美观且结构化的日志记录对于轻松理解脚本执行的输出非常重要。就像其他高级编程语言一样，我总是使用自定义日志函数，比如`__msg_info`、`__msg_error`等。在我的 Bash 脚本中也是如此。这有助于通过在一个地方进行更改来实施标准化的日志记录结构。

我通常更喜欢在我的脚本中有某种类型的`__init`机制，这样的*记录器变量(和其他系统范围的)*被初始化或设置为默认值。它们也可能来自调用期间的命令行参数。

例如:`$ ./run-script.sh --debug`之类的

当脚本执行时，它确保这样的*系统范围的*设置被设置为缺省值(如果是强制的)或者至少用相关的东西初始化，如果需要的话。我通常基于用户体验和用户将/应该进入的配置细节之间的权衡来做出这个决定。

# 为重用和干净的系统状态而设计

## 模块化/可重用代码

```
├── framework
│   ├── common
│   │   ├── loggers.sh
│   │   ├── mail_reports.sh
│   │   └── slack_reports.sh
│   └── daily_database_operation.sh
```

维护一个单独的存储库总是有帮助的，这个存储库可以用来引导您想要开发的新 Bash 项目/脚本。所有可重用的东西都可以在这样的代码库中维护，并在其他想要使用这些功能的项目中使用。它极大地减少了其他脚本的大小，还确保了代码库是小的和可测试的。

就像上面的例子一样，所有的日志记录功能，像`__msg_info`、`__msg_error`，以及其他像报告空闲时间这样的事情，都可以在`common/*`下单独维护，并在其他像`daily_database_operations.sh`这样的脚本中动态获取。

## 留下干净的状态

例如，如果您在脚本执行期间下载一些资源，将所有这些数据存储在一个公共的、随机命名的目录下总是一个最佳实践——比如`/tmp/AlRhYbD97/*`。您可以在这里使用随机文本生成器。

```
rand_dir_name="$(cat /dev/urandom | tr -dc ‘a-zA-Z0–9’ | fold -w 16 | head -n 1)"
```

稍后，可以确保在`trap`处理程序中清理这些目录(如上所述)。如果这些事情没有得到处理，它们就会堆积起来，在某个阶段会给主机带来意想不到的问题。

## 使用锁定文件

在任何给定的时间点，强制主机上只执行一个脚本实例通常会有所帮助。这可以通过锁文件来管理。

我通常在`/tmp/project_name/*.lock` 下创建锁文件，并在脚本开始时检查它们是否存在。这有助于优雅地退出程序，并避免并行运行的其他脚本对系统状态的意外更改。如果您绝对需要在给定的主机上并行运行相同的脚本，则可能不需要这样做。

# 衡量和改进

我们经常需要处理长时间运行的脚本——例如，日常的数据库操作。此类操作通常包括一系列步骤，如下载数据、检查异常、导入数据、发送状态报告等。

在这种情况下，我总是尝试将这些步骤分解成单独的脚本，并使用以下方法测量/报告它们的执行状态和时间:

`time source "${filepath}" "${args}">> "${LOG_DIR}/RUN_LOG" 2>&1`

稍后，我用以下命令获取执行时间:

`tac "${LOG_DIR}/RUN_LOG.txt" | grep -m1 "real"`

这帮助我多次找出脚本中需要优化的有问题/慢的地方。

# 进一步学习

迄今为止，我遇到的最好的学习资源是苹果公司关于 [Shell 脚本基础](https://developer.apple.com/library/archive/documentation/OpenSource/Conceptual/ShellScripting/shell_scripts/shell_scripts.html)的文档——我强烈推荐它。

我希望这篇文章能给你一些思路，帮助你编写健壮的、高性能的脚本，可以安全地在关键系统上使用。下次见， *tschüss！*