# 与 Kitty 和 Zsh 一起释放您的终端

> 原文：<https://betterprogramming.pub/unleashing-your-terminal-with-kitty-and-zsh-102527d07a1c>

## 让您的发展变得轻而易举

![](img/534b764d4ee1721bf77204b777b207b3.png)

[张凯夫](https://unsplash.com/@zhangkaiyv?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/) 上的照片

# 介绍

我们中的许多人在一个叫做**终端**的东西上花费了相当多的时间，然而许多人仍然使用他们的操作系统自带的默认终端，并且无限期地坚持使用它，这并没有什么错，但是你有没有想过如果你花一点点时间对它进行微调，你的生活会变得多么简单？

在这里，我将尝试为您根据需求定制终端的永无止境之旅奠定基础。由于我不推荐我已经很长时间没有日常使用的东西，所以在本教程中，我将向你展示如何设置和定制 [Kitty](https://sw.kovidgoyal.net/kitty) 和 [Zsh](https://en.wikipedia.org/wiki/Z_shell) 来升级你的终端游戏。

# 凯蒂什么？

在终端模拟器的世界中有相当多的选项，其中每一个都可以吸引不同类型的用户，一些可以有更复古的感觉，而另一些则旨在最小化并专注于重要的东西，而不是有很多花里胡哨的东西。

以我的经验来看，Kitty 属于极简型，这并不意味着你不能用它做很多事情，不，你可以完全定制它，让它成为你自己的东西。通过最小化，我指的是它让你做那些调整的方式，在这种情况下很简单，只要继续阅读，你马上就会明白我在说什么。

除此之外, **Kitty** 使用你的 **GPU** 进行渲染，这使得渲染速度非常快，真的非常快，你可以通过输入和屏幕上出现的文本之间的延迟来注意到这一点，此外，如果你愿意，它们允许你进行更多的性能调整。

# 正在安装

您有许多选择，但为了简单起见，为了不使这个故事过于沉重，我将展示两种安装它的方法，一种是为那些喜欢稳定的人，另一种是为那些喜欢生活在边缘的人。

如果您在 macOS 或 Linux 上运行以下命令，请安装预构建的 **Kitty** 二进制文件:

```
$ curl -L https://sw.kovidgoyal.net/kitty/installer.sh | sh /dev/stdin
```

如果您愿意，您可以设置一些参数来指定不同的安装位置，或者告诉 **Kitty** 在安装后不要启动:

```
$ curl -L https://sw.kovidgoyal.net/kitty/installer.sh | sh /dev/stdin \\ dest=/some/other/location launch=n
```

如果出于好玩、好奇或疯狂的原因，您想要安装并使用夜间版本，您可以通过运行以下命令来实现:

```
$ curl -L https://sw.kovidgoyal.net/kitty/installer.sh | sh /dev/stdin \\ installer=nightly
```

如需进一步参考，请参考在 **Kitty** 工作的人员制作的[安装指南](https://sw.kovidgoyal.net/kitty/binary/#install-kitty)的制作精良的文档。

# 定制它

这一部分更倾向于个人化，因为每个人都有自己的偏好，所以我不打算进行全面的定制，而是向您展示基础知识，以便您可以在此基础上开始构建。

## 主题

当我开始摆弄 Linux 和它的终端时，让我恼火的一件事是不知道或者找不到一种简单的方法来改变它的配色方案，因此我在终端模拟器中寻找的一件事是轻松切换主题的能力。

**Kitty** 更确切地说，它的一个名为 [Dexpota](https://github.com/dexpota) 的社区成员在这个问题上做得非常出色，比如 [kitty-themes](https://github.com/dexpota/kitty-themes) ，它允许你下载和应用大量的主题。

从存储库中下载特定主题:

```
$ THEME=https://raw.githubusercontent.com/dexpota/kitty-themes/master/themes/Brogrammer.conf
$ wget "$THEME" -P ~/.config/kitty/kitty-themes/themes
```

创建下载主题的符号链接:

```
$ cd ~/.config/kitty
$ ln -s ./kitty-themes/themes/Brogrammer.conf ~/.config/kitty/theme.conf
```

创建一个 kitty.conf 文件并添加下载的主题:

```
$ echo "include ./theme.conf" > kitty.conf
```

就这样关闭你的 **Kitty** 终端，再打开看看变化，就这么简单，想再换个主题？重复步骤 1 和 2，关闭，打开，瞧！

## 字体

当谈到选择字体时，我没有明确的偏好，只要它是可读的和一致的，对我来说就很好，但随着时间的推移，我开始深入挖掘这个主题，并发现在某些旨在促进开发的字体中，你必须找到它们，但这并不太麻烦。

但是后来 [Ryanoasis](https://github.com/ryanoasis) 带来了一个叫做[书呆子字体](https://github.com/ryanoasis/nerd-fonts)的东西，这是一个为开发者准备的补丁字体集合，我们将用它在 **Kitty** 中设置一个自定义字体。

从存储库中下载特定目录。需要`Git v2.26`:

```
$ git clone --filter=blob:none --sparse git@github.com:ryanoasis/nerd-fonts
$ cd nerd-fonts
$ git sparse-checkout add patched-fonts/Hack
```

安装单一字体:

```
$ ./install.sh Hack
```

创建 font.conf 文件并添加下载的字体:

```
$ cd ~/.config/kitty
$ echo "font_family Hack Nerd Font Mono" > font.conf
```

最后，通过打开文件并添加一个`include ./font.conf`指令，可以将 font.conf 文件添加到 kitty.conf 文件中。关闭你的 **Kitty** 终端，再打开，新字体应该就在那里等你欣赏了。

显然，这只是冰山一角， **Kitty** 是非常可定制的，快速浏览他们的[配置 Kitty](https://sw.kovidgoyal.net/kitty/overview/#configuring-kitty) 部分可能会让你想知道你可以做些什么来改进它。

# Zsh 什么？

驱动程序已经准备好了，现在为了更上一层楼，你需要设置一个命令解释器来满足你的需求，并让你根据自己的喜好进行定制。不去争论哪个更好什么的，我将选择简单的路线，推荐我广泛使用的，就像我对 **Kitty** 做的那样。

Zsh 是一个强大的交互式命令解释器，它不仅可以作为一个外壳，还可以作为一个脚本语言，其中一个主要的好处是它可以很容易地用插件扩展，使你的终端看起来像是在类固醇上。

# 正在安装

像 **Kitty** 一样，有很多方法可以安装它，但是现在让我们看看如何为 macOS 和 Linux 安装它，它们不像 Windows 那样支持`bash`。

检查您是否已经安装了 **Zsh** :

```
$ zsh --version
```

安装 **Zsh** :

```
*# For macOS users*
$ brew install zsh*# For Ubuntu/Debian*
$ apt install zsh
```

使 **Zsh** 成为您的默认 shell:

```
*# For macOS users (latest versions)*
$ chsh -s /usr/local/bin/zsh*# For macOS users (High Sierra and older)*
$ chsh -s /bin/zsh*# For Linux users*
$ chsh -s $(which zsh)
```

之后，你所要做的就是从你的电脑账户注销/登录，下次你打开 Kitty 时，你会注意到一个包含几个选项的菜单，选择`0`不要惊慌，我知道这会儿看起来很糟糕，但这不会持续太久。

# 定制它

做好准备吧，你将会见证一个令人愉快的转变，这是由 [Oh My Zsh](https://ohmyz.sh/) 提供的，它是一个开源框架，捆绑了一些令人敬畏的特性，使开发变得更加容易和愉快。

## 安装我的天啊

由于我们已经安装了 **Zsh** 并正在运行，安装 **Oh My Zsh** 只需运行一个简单的命令。

```
*# Using curl*
$ sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"*# Using wget*
$ sh -c "$(wget -O- https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"*# Using fetch*
$ sh -c "$(fetch -o - https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

## 主题

默认情况下**哦，我的 Zsh** 带有一个名为`robbyrusell`的主题，这是创建者的名字，但是如果你看看[维基页面](https://github.com/ohmyzsh/ohmyzsh/wiki/Themes)的主题部分，有大量选项供你选择。

从一个转换到另一个很容易，就像重命名一个变量一样，让我演示给你看。

编辑`.zshrc`文件。

```
$ nano ~/.zshrc
```

**注意:**我用的是`nano`但是你可以用任何你想编辑的东西。

重命名`ZSH_THEME`变量。

```
ZSH_THEME="theme-name" *# e.g., half-life*
```

然后你要做的就是保存文件，或者重启 **Kitty** 或者通过运行下面的命令`source ~/.zshrc`刷新`.zshrc`文件。

## 插件

关于 **Oh My Zsh** 的另一个非常酷的特性是，它还可以作为一个插件管理器，允许你安装其他开发者制作的几乎无限量的有用工具。

我们将要安装的第一个工具将帮助您注意您是否键入了正确的命令，我说的是[zsh-syntax-highlighting](https://github.com/zsh-users/zsh-syntax-highlighting/)但是对于您的终端来说，这不是很酷吗！

安装`zsh-syntax-highlighting`插件。

```
$ git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```

激活`.zshrc`文件中的插件。

```
plugins=(
    *# other plugins...*
    zsh-syntax-highlighting
)
```

通过关闭或执行`source ~/.zshrc`重启 **Zsh**

我们要安装的第二个将帮助你完美地编写不同的命令，我说的是[zsh-自动建议](https://github.com/zsh-users/zsh-autosuggestions/)帮助你记住并自动完成你经常键入的东西，从而使你更有效率，更不容易键入错误的东西。

安装`zsh-autosuggestions`插件。

```
$ git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
```

激活`.zshrc`文件中的插件。

```
plugins=(
    *# other plugins...*
    zsh-autosuggestions
)
```

通过关闭或执行`source ~/.zshrc`重启 **Zsh**

# 快速回顾

唷！太激烈了。

您在这里学到了很多东西，我希望这些东西能够在您的道路上为您服务，并使您的生活更轻松，这个主题本身可以很快进入兔子洞体验，因为您可以使用 Kitty 和 Zsh 实现无限的定制。

在本教程中，你只是把你的脚趾尖放入水中，现在你有了基础，如果你想走得更远，就看你自己了。

我会给你一个你可能想看的东西的线索，那就是[小猫](https://sw.kovidgoyal.net/kitty/kittens/custom/)这是一种扩展 Kitty 的方法，但另一方面，如果你想保持原样，那完全没问题，你有一个相当高效的终端，它会带你去你想去的地方。

然而，分享我作为开发人员在日常工作中使用的工具是一件愉快的事情。感谢阅读。