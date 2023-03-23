# Jarvis 1.2.0 —新增功能和使用方法

> 原文：<https://betterprogramming.pub/jarvis-1-2-0-whats-new-how-to-use-it-175673b9f326>

## 跨平台功能、新特性等等

![](img/ce11e7b99c0a778c1a01b634e245b008.png)

好消息，随着 1.2.0 版本的发布，Jarvis 现在是跨平台的了。现在，每个 Jarvis 命令都可以在 macOS、Windows 和 Linux 上运行。Jarvis 1.2.0 中还有两个新特性，以及一些错误修复和改进。

如果你还不熟悉 Jarvis，那么你就错过了。Jarvis 是一个 CLI，可以帮助完成日常任务，如管理配置、部署应用程序和初始化项目。如果你想了解更多关于贾维斯的事情，以及我为什么建造他，那么你应该阅读[这个故事](https://medium.com/swlh/building-a-cli-to-help-manage-github-accounts-react-projects-and-more-2755259be493)，如果你想知道如何设置和使用他，那么[这个故事](https://medium.com/better-programming/how-to-set-up-use-jarvis-the-cli-that-makes-your-life-easier-54fa6d7e83b)将指导你。

要将 Jarvis 更新到新版本，运行:

```
npm install -g @willptswan/jarvis
```

# 小事情

让我们从小事开始，值得注意的是`site-open`和`site-search`命令现在使用你的默认浏览器，而不是谷歌浏览器。您将看到的另一个值得注意的变化是日志系统已经过彻底检查，主要是为了使错误更清晰、更容易阅读。小东西就讲到这里，让我们来看看新的特性。

# 新功能

## 证明文件

在 1.2.0 版本中，Jarvis 现在有了一个`documentation`命令。此命令在默认浏览器中打开语言、框架或平台的文档。例如，如果您运行以下命令，那么 JavaScript 的文档将会打开:

```
jarvis documentation javascript
```

还有一个`list`命令，这样您就可以看到所有可用的文档:

```
jarvis documentation-list
```

添加文档功能有助于加快您的工作流程。现在，当您不确定某个东西是如何工作的时候，不用花几分钟时间去浏览、搜索文档，也不用决定哪个网站有正确的文档，您只需输入一个简单的命令，一切都会在几秒钟内为您完成。

## 备忘单

你有没有发现自己在写一些代码，而你的大脑一片空白，你想不起来一个函数叫什么，或者它是否存在。这种情况就是 Jarvis 中加入备忘单的原因。目前，有 24 个可用。假设您不记得 JavaScript 中的`RegExp`语法，您需要做的就是运行以下命令:

```
jarvis cs javascript
```

这个命令将显示 Javascript 中所有可用的函数、方法和参数。使用`cs`命令，您甚至可以更进一步。运行以下命令:

```
jarvis cs javascript regexp
```

现在，只会显示与`RegExp`相关的位，所以你不必滚动所有内容来找到你想要的。

Jarvis 还有两个与备忘单相关的命令。

`types`命令将显示所有可用的备忘单:

```
jarvis cs-types
```

并且`sections`命令将显示特定备忘单的所有可用子部分:

```
jarvis cs-sections <type>
```

# 结论

Jarvis 1.2.0 到此为止，除了两个新特性和跨平台兼容性，大部分更新都在幕后进行。我希望你喜欢使用最新的版本，像往常一样，我们非常欢迎反馈、建议和贡献。

GitHub:[https://github.com/willptswan/jarvis](https://github.com/willptswan/jarvis)

https://www.npmjs.com/package/@willptswan/jarvis:[NPM](https://www.npmjs.com/package/@willptswan/jarvis)