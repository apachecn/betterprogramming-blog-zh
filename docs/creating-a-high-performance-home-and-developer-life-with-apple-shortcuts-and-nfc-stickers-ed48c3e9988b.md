# 用苹果快捷方式和 NFC 贴纸创造高性能的家庭和开发者生活

> 原文：<https://betterprogramming.pub/creating-a-high-performance-home-and-developer-life-with-apple-shortcuts-and-nfc-stickers-ed48c3e9988b>

## 让你的家自动化比你想象的要容易

![](img/0c8f8daecc9ade31e02acff7abca8675.png)

来自 [Pexels](https://www.pexels.com/photo/home-real-estate-106399/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) 的 [Binyamin Mellish](https://www.pexels.com/@binyaminmellish?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) 摄影。

当苹果开发(或改进)某样东西时，它通常被描述为革命性的，并向所有人广播。虽然三星发布新手机时大多不为人知，但观看苹果的 keynote 直播已经成为一种现代生活方式，也是第二天在办公室跟上讨论的必要条件。

但是有两个产品是苹果一直没有注意到的:Home 和 Shortcuts。

# 家的快速介绍

[Home](https://www.apple.com/ios/home/) 是苹果统一智能家居应用丛林的尝试。每个灯泡，智能烟雾探测器，门铃，暖气控制都有它的应用程序。打开加热器和灯的时间比预期的要长。苹果面临的问题是，HomeKit 要求每个智能家电的应用程序开发人员创建一个界面。例如，如果没有飞利浦将他们的 Hue 应用程序扩展到 HomeKit，你就无法连接到你的灯泡。

其次，2014 年推出 Home。2014 年有多少人拥有智能家居产品？要么是极客(也就是我)要么是百万富翁在建造一个新的智能住宅。

# 通过快捷方式为家居增添动力

Home 本身是初级的，除了将不同的设备统一到一个应用程序中之外，它不能做太多事情。直到四年后，在 WWDC 2018 上，苹果公司才宣布蝙蝠侠的罗宾:捷径。

快捷方式将家中配件的简单连接扩展到触发器和更全面的自动化。IFTTT (If this then that)在 2010 年引入了这种连接编程。快捷方式可以在晚上 10 点关灯，同时在卧室的 HomePod 上播放播客。尽管使用像时间或位置这样的静态触发器很酷，但它没有给我创建高效自动化所需的灵活性。

由于第二次封锁让每个人都有更多的时间在家，我开始更广泛地研究家和快捷方式能为我做些什么。我发现了一个完美的匹配:NFC 标签！

# 欢迎来到快捷方式中的 NFC 标签

NFC 标签无处不在，在 iPhone 上已经出现了一段时间。但直到苹果在 2018 年 9 月为 iPhone XS 引入了对背景标签读取的支持后，它们才变得有益。这种能力意味着用户不再需要主动告诉他们的 iPhone 去寻找附近的 NFC 标签。这使得 NFC 标签成为一种有用的家庭配件。

让我举几个例子来说明 NFC 标签和快捷方式如何让我的家变得更加智能！

## 自动健身程序

由于 NFC 标签经常用于会员卡(用于大学、健身房、共同工作空间或办公室大门)，我们可以利用这一点，创造我们的第一个自动化！你可以阅读[我的完整教程](https://medium.com/better-programming/automating-your-iphone-using-nfc-tags-dba61538be09)关于如何在快捷方式中创建 NFC 自动化。我用我的健身房会员卡自动到达健身房:

1.  "在我离开之前，请不要打扰我."
2.  “以开放式目标开始功能性力量训练”——开
3.  “打开蓝牙”——连接到我的 AirPods
4.  “打开 Spotify”——这样我就可以开始播放锻炼音乐了。

## 烤箱预热定时器

我经常开始预热我的烤箱，然后等很久才检查它是否预热好。现在我的烤箱面板上有一个 NFC 贴纸。当我用手机点击它时，一个九分钟的计时器会自动启动。这是我的烤箱预热到 180 摄氏度的时间。

## 汽车模式

另一个非常酷的地方是我车里的手机支架。当我将手机放入支架时，Shortcuts 会自动开始播放 Apple Music，并将 Apple Watch 上的影院模式设置为打开(以避免它在转向时总是打开)。其次，我使用地图检查离家有多远。如果距离大于一英里，我打开谷歌地图导航主页。当然，您可以添加更多的场景。如果是早上 7 点到 8 点之间，我在家，我进入汽车模式，激活导航来工作。

## 工作日开始(技术)程序

为了改善我的工作日日常工作，我在我的工作表上添加了另一个 NFC 标签。当我早上到达我的办公桌时，我会快速地把手机放在桌子上，然后自动运行以下程序:

1.  检查是否是早上(否则，运行下面的工作日结束程序)。
2.  "在我离开之前，请不要打扰我."
3.  “打开 Spotify。”

其次，我运行一些技术程序。为此，我在 SSH 上创建了一个运行脚本(参见[教程](https://matsbauer.medium.com/how-to-run-ssh-terminal-commands-from-iphone-using-apple-shortcuts-ssh-29e868dccf22))。该脚本运行:

```
open -a "Google Chrome"
open -a "XCode.app" (if I closed it yesterday)cd [directory of code]
git pull (in case someone played with the repo)
```

通过 SSH 连接到您的 Mac，选项几乎是无限的！你甚至可以运行一个 [Automator](https://support.apple.com/en-gb/guide/automator/welcome/mac) 脚本，让你登录网站，查看邮件，自动向同事发送生日祝福，并将当前时间添加到 Excel 表格中(用于工作时间跟踪)。选择是无限的！

## 工作日结束(技术)程序

为了改善我工作日结束时的例行工作，我快速点击我的手机上与上面相同的桌面 NFC 标签，并运行以下自动操作:

*   首先，我给家里发一条 Whatsapp，上面写着“现在下班”，然后把家里的恒温器调到 22 摄氏度，这样当我到家的时候就暖和了。
*   其次，我们回到 SSH 脚本。这是我离开办公室前运行的(不碰电脑！)

```
pkill -x chrome (I don't want to come back to 120 tabs tomorrow)cd [directory of code]
git add .
git commit -m "auto-save"
git pushpmset sleepnow (putting my mac to sleep)
```

# 结论

感谢阅读。我希望我能给你一些灵感来创建你自己的自动化和节省时间！请随意评论您已经实现的任何进一步的想法或自动化！