# 如何使用两个命令交互式搜索和替换多个文件

> 原文：<https://betterprogramming.pub/how-to-interactively-search-and-replace-across-many-files-with-2-commands-b133d1d82b86>

## 用 Vim 跨文件进行看似简单的搜索和替换

![](img/7ba76750db5960327f97eff61e9b21ab.png)

作者照片。

虽然有多种方法可以在单个文件中搜索和替换单词，但是当您有一个字符串要在多个名称不同的不相关文件中更新时，该怎么办呢？当然，您利用了命令行工具的强大功能！

首先，你需要`find`所有你想改变的文件。将对`find`有效的搜索查询串在一起实际上只受到你的想象力的限制。下面是一个查找 Python 文件的简单示例:

```
find . -name '*.py'
```

`-name`测试搜索一个模式，比如所有以`.py`结尾的文件，但是`find`可以用其他测试条件做更多的事情，包括`-regex`测试。运行`find --help`查看众多选项。

使用`grep`进一步调整您的搜索，只获取包含您想要更改的字符串的文件，例如添加:

```
grep -le '\<a whale\>'
```

`-l`选项只给出包含与“鲸鱼”匹配的模式(用`-e`表示)的所有文件的文件名。

使用 [Vim](https://github.com/vim/vim) 令人印象深刻的`:bufdo`可以让您跨多个缓冲区运行相同的命令，交互式地处理所有这些文件，而无需一次打开、保存和关闭一个文件的繁琐工作。

让我们将您强大的`find` + `grep`结果插入到 Vim 中:

```
vim `find . -name '*.py' \
-exec grep -le '\<a whale\>' {} \;`
```

使用 backtick-expansion 将我们的搜索传递给 Vim 会打开多个准备就绪的缓冲区(更多信息请参见 Vim 中的`:h backtick-expansion`)。现在您可以将 Vim 命令`:bufdo`应用于所有这些文件，并执行诸如交互式搜索和替换之类的操作:

```
:bufdo %s/a whale/a bowl of petunias/gce
```

“全局”的`g`将改变所有行上图案的出现。如果找不到图形，`e`将忽略错误。`c`选项使其具有交互性。如果你有信心，你可以省略它，不需要逐一检查就可以做出改变。

当你完成了所有的缓冲区，保存你完成的所有工作:

```
:bufdo wq!
```

然后享受你节省下来的时间和精力。