# 使用 macOS 和 HomeBrew 的 12 个终端提示和技巧

> 原文：<https://betterprogramming.pub/12-terminal-tips-and-tricks-using-macos-and-homebrew-4e89c2ccb2fb>

## 有用和有趣的自制公式

![](img/46a11cefa5575f58bf824cb198311a4c.png)

[潘卡杰·帕特尔](https://unsplash.com/@pankajpatel?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片。

终端是一个简单的命令行系统，它为您提供了一个用代码控制 macOS 底层系统的界面。

在终端内，你可以做很多很酷的事情，比如下载应用程序，玩*俄罗斯方块*，[看*星球大战*](https://www.macworld.co.uk/how-to/mac-software/mac-terminal-projects-tutorial-3613813/) 等等。如果你是终端新手，这里有一份来自苹果的用户指南[帮助你入门。](https://support.apple.com/en-my/guide/terminal/welcome/mac)

# 公司自产自用

然而，这篇文章的明星是[家酿](https://brew.sh/)。Homebrew 是一个用于 macOS 的开源软件包管理器。众所周知，它是 macOS 的缺失包管理器。Homebrew 也是用 Ruby 编程语言编写的。

包管理器之所以重要，是因为当开发人员用管道、i/o 重定向或脚本连接他们的应用程序时，软件包变得高度相互依赖。有了包管理器，检查系统兼容性和依赖性的痛苦就消除了，安装应用程序也变得容易多了。

# 安装自制软件

现在你知道了一点什么是家酿啤酒，让我们开始安装。

快速打开终端的方法是用`cmd + space`，然后输入终端，点击`enter`，就会弹出来。

要安装 Homebrew，你必须先安装 Xcode 工具，或者你可以选择安装 Xcode 的完整版本。

## 安装 Xcode 工具

```
$ xcode-select — install 
```

## 安装自制软件

```
$ ruby -e “$(curl -fsSL [https://raw.githubusercontent.com/Homebrew/install/master/install](https://raw.githubusercontent.com/Homebrew/install/master/install))
```

## 一些自制程序命令

```
$ brew --version # display version
$ brew help  # print help info
$ brew doctor # checking for problems 
```

既然您的 macOS 中已经有了自制软件，那么是时候了解一些技巧和窍门了。

> 我写关于数据科学和人工智能的文章。如果你想在我发帖时得到通知，现在就订阅我的媒体简讯吧！
> 
> 如果你不是普通会员，考虑一下今天只需 5 美元就可以订阅[。你将直接支持我，你将获得大量的优秀作品！](https://benedictxneo.medium.com/subscribe)

![](img/b36849e69686f6a5504c5cb0649dbf09.png)

克里斯多夫·高尔在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片。

# 1.桶

`Cask`为在 macOS 上安装和管理 GUI 应用程序带来了简单性和速度。简单来说，这是一种在 Mac 上安装应用程序的方式，而不需要谷歌搜索和在线安装的麻烦。

```
$ brew install cask
```

*   列出应用:`brew search —- casks`
*   搜索应用:`brew search (app_name)`
*   安装:`brew cask install (app_name)`
*   更新:`brew cask upgrade`
*   帮助:`brew cask help`

[](https://github.com/Homebrew/homebrew-cask) [## 自制/自制桶

### 家酿木桶扩展了家酿，并带来了它的优雅，简单，快速的安装和管理的 GUI…

github.com](https://github.com/Homebrew/homebrew-cask) 

# 2.Youtube-dl

`Youtube-dl`是一个命令行程序，用于下载 YouTube 视频或来自任何平台的高分辨率视频。你还可以尝试很多其他功能，你可以在下面的 GitHub 资源库中找到它们。

```
$ brew install youtube-dl
```

*   安装 ffmpeg: `brew install youtube-dl ffmpeg`
*   下载最高分辨率:`youtube-dl -f bestvideo+bestaudio 'link'`
*   帮助:`youtube-dl —- help`

[](https://github.com/ytdl-org/youtube-dl) [## ytdl-org/youtube-dl

### youtube-dl -从 youtube.com 或其他视频平台下载视频，为所有 UNIX 用户立即安装…

github.com](https://github.com/ytdl-org/youtube-dl) 

# 3.速度测试

`Speedtest-cli`是测试互联网带宽的命令行界面。它允许你对特定的服务器进行测试，甚至为你提供一个网址，这样你就可以向你的朋友吹嘘你的网速。

```
$ brew install speedtest-cli
```

*   运行:`speedtest-cli`

[](https://github.com/sivel/speedtest-cli) [## sivel/speedtest-cli

### 使用 speedtest.net speed test 测试互联网带宽的命令行界面-cli 与 Python 2.4-3.7 pip 一起使用…

github.com](https://github.com/sivel/speedtest-cli) 

# 4.图像魔术

`Imagemagick`是一款多功能工具，可用于将一种图像格式转换为另一种格式(如 JPEG 转换为 PNG)。它也可以用来添加边框，效果，以及调整大小。使用`convert help`浏览更多用途。

```
$ brew install imagemagick
```

*   添加边框(示例):`convert testing.png -border 1x1 -bordercolor black result.png`
*   添加效果(示例):`convert testing.png -charcoal 2 example.png`
*   调整大小(示例):`convert testing.png -resize 1920 (or x1080) example.png`
*   帮助:`convert help`

[](https://imagemagick.org/index.php) [## 图像魔术

### 特性和功能*新闻*社区使用 ImageMagick 创建、编辑、合成或转换位图图像。它可以…

imagemagick.org](https://imagemagick.org/index.php) 

# 5.节日

`mas`是一个 Mac App Store 命令行界面，可让您直接从命令行从 App Store 安装 Mac 应用程序。

您可以搜索应用程序、安装所有现有更新、打印应用程序在商店中的版本号等等。甚至有一个有趣的选项叫做 lucky，它会安装第一个搜索结果。有本事你试试。

```
$ brew install mas
```

*   列出所有应用:`mas list`
*   搜索应用:`mas search Xcode`
*   安装应用:`mas install 497799835`(应用的版本号)
*   待定更新应用:`mas outdated`
*   更新应用:`mas upgrade`

[](https://github.com/mas-cli/mas) [## mas-cli/mas

### Mac App Store 的简单命令行界面。专为脚本和自动化而设计。家酿是首选…

github.com](https://github.com/mas-cli/mas) 

# 6.wifi-密码

`wifi-password`顾名思义，它会显示您当前连接的网络的 Wi-Fi 密码。输入您的钥匙串鉴定后，密码以亮绿色显示。

```
$ brew install wifi-password
```

*   运行:`wifi-password`

[](https://github.com/nvm-sh/nvm) [## nvm-sh/nvm

### nvm 是 node.js 的版本管理器，设计为按用户安装，按 shell 调用。nvm 可以在任何…

github.com](https://github.com/nvm-sh/nvm) 

# 7.午夜指挥官

`Midnight-commander`是一个融合了 finder 和命令行功能的工具，在终端中创建一个交互式文件管理器。在`Midnight-commander`中，您可以通过点击或键盘箭头来导航您的目录结构。

```
$ brew install midnight-commander
```

*   运行:`mc`

[](https://www.midnight-commander.org/) [## 午夜指挥官

### 当前版本为:4 . 8 . 24；有什么新鲜事。即将发布:4 . 8 . 25；有什么新鲜事。我们的发布指南。GNU 午夜指挥官…

www.midnight-commander.org](https://www.midnight-commander.org/) 

# 8.Zsh

`Zsh`或 Z shell，是一个具有吸引人的颜色的 Unix shell，用作交互式登录 shell 和 shell 脚本的命令解释器。在 GitHub 上阅读更多关于它的用法[。](https://github.com/hmml/awesome-zsh)

```
$ brew install zsh
```

*   将 Zsh 设置为默认 shell: `chsh -s /bin/zsh`

 [## Zsh

### 该网站提供了 Zsh 信息和档案的索引。Zsh 是一个为交互使用而设计的 shell，尽管它…

www.zsh.org](https://www.zsh.org/) 

# 9.htop

`htop`是一个用于 Unix 的交互式系统监视器、进程查看器和进程管理器，据说它是 Unix 程序`top`的继承者。它显示在 Mac 上运行的进程的更新列表，并按 CPU 使用量排序。

```
$ brew install htop
```

*   运行:`sudo htop`

[](https://hisham.hm/htop/) [## htop——用于 Unix 的交互式进程查看器

### 这是 htop，一个用于 Unix 系统的交互式进程查看器。它是一个文本模式的应用程序(用于控制台或 X…

希沙姆](https://hisham.hm/htop/) 

# 10.树

`tree`是一个以树状格式列出文件夹中目录内容的工具。这个有用的技巧对于那些想要快速直观地展示项目文件结构的人来说是一个救命稻草。

```
$ brew install tree
```

*   运行:`tree`

 [## Linux 主页的树命令

### 描述:Tree 是一个递归目录列表命令，它产生一个深度缩进的文件列表，这是…

mama.indstate.edu](http://mama.indstate.edu/users/ice/tree/) 

# 11.阿奇

`archey`在终端内显示你的系统信息，以及一个基于文本的复古风格的苹果图标。

```
$ brew install archey
```

*   运行:`archey`
*   颜色:`archey -c`
*   黑白:`archey -b`

 [## Archey-osx

### osXa 的 EAn archey 脚本

奥比汉. github.io](https://obihann.github.io/archey-osx/) 

# 12.废物

是一个简单的程序，将文件或文件夹移动到垃圾桶。

```
$ brew install trash
```

*   运行:`trash [-vlesyF] <file> [<file> …]`
*   求助:`trash —- help`

 [## 废物

### 这个页面使用了一些 Javascript 请在浏览器中打开它。这是一个用于 OS X 的小型命令行程序…

hasseg.org](https://hasseg.org/trash/) 

# 行动计划

![](img/cbdb95798962053159f4e95a52baf0bc.png)

布莱克·康纳利在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

现在，您已经阅读了这些技巧和窍门，并有希望测试了它们中的每一个，接下来还有更多。除了上面列出的命令，Homebrew 还有很多其他的技巧，你可以尝试一下。所以不要犹豫，用它们做实验。谁知道呢？您可能会发现一些有用的东西，可以使您的编码会话更有成效。

如果你花很多时间在终端上，一个额外的提示是下载 iTerm 2。据说比终端更好，更可定制。

要浏览更多的自制命令，请查看下面他们的网站！测试每一个命令，体验其中的乐趣。

或者你可以看看这个[自制备忘单](https://dev.to/code2bits/homebrew---basics--cheatsheet-3a3n)。

[](https://formulae.brew.sh/formula/) [## 自制配方

### 家酿啤酒包装索引

formulas . brew . sh](https://formulae.brew.sh/formula/) 

我希望这篇文章对您有用，并让您了解了如何在日常编码工作中使用自制软件。

我也写数据科学的文章，如果你感兴趣，你可以在这里找到一些！

**数据科学工具箱—数据科学介绍系列**

[](https://towardsdatascience.com/the-data-scientists-toolbox-part-1-c214adcc859f) [## 数据科学简介

### 关于什么是数据科学、大数据、数据和数据科学过程及其应用。

towardsdatascience.com](https://towardsdatascience.com/the-data-scientists-toolbox-part-1-c214adcc859f) 

**如何“超学习”数据科学——快速有效学习的技巧**

[](https://medium.com/better-programming/how-to-ultralearn-data-science-part-1-92e143b7257b) [## 如何“超级学习”数据科学—第 1 部分

### 这是一个简短的指南，基于《超学习》一书，应用于数据科学

medium.com](https://medium.com/better-programming/how-to-ultralearn-data-science-part-1-92e143b7257b) 

**海量数据科学资源**

[](https://towardsdatascience.com/top-20-youtube-channels-for-data-science-in-2020-2ef4fb0d3d5) [## 2020 年你应该订阅的 25 大数据科学 YouTube 频道

### 以下是你应该关注的学习编程、机器学习和人工智能、数学和数据的最佳 YouTubers

towardsdatascience.com](https://towardsdatascience.com/top-20-youtube-channels-for-data-science-in-2020-2ef4fb0d3d5) [](https://towardsdatascience.com/top-20-free-data-science-ml-and-ai-moocs-on-the-internet-4036bd0aac12) [## 互联网上 20 大免费数据科学、ML 和 AI MOOCs

### 以下是关于数据科学、机器学习、深度学习和人工智能的最佳在线课程列表

towardsdatascience.com](https://towardsdatascience.com/top-20-free-data-science-ml-and-ai-moocs-on-the-internet-4036bd0aac12) [](https://towardsdatascience.com/10-data-science-competitions-for-you-to-hone-your-skills-for-2020-32d87ee19cc9) [## 10 场数据科学竞赛让你磨练 2020 年的技能

### 这里有一个网站列表，供您练习数据科学技能，参加在线黑客马拉松和…

towardsdatascience.com](https://towardsdatascience.com/10-data-science-competitions-for-you-to-hone-your-skills-for-2020-32d87ee19cc9) 

[关注我](https://medium.com/@benthecoder07)获取更多类似的文章！