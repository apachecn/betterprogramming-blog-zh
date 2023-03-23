# 为什么开发人员应该转向 Linux

> 原文：<https://betterprogramming.pub/why-developers-should-switch-to-linux-9e3dc8f6406e>

## 除了可定制性和自动化能力，Linux 还附带了许多开发工具

![](img/aa376a29ba27fb50745791461adf9636.png)

照片由[亚历克斯·库利科夫](https://unsplash.com/@burntime?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

尽管有无数的操作系统，但有一个明显的赢家几乎垄断了桌面领域:微软视窗系统。看一下这张图表:

操作系统市场份额基于 statcounter.com[提供的数据](https://gs.statcounter.com/os-market-share/desktop/worldwide)截至 2021 年 7 月。Windows 77.68%，macOS 17.76%。

请注意，尽管 Linux 是一个令人敬畏的替代品，但与其他巨头相比，它所占的份额非常小。但是为什么呢？

## 用户倾向于坚持使用预装的软件。

大多数计算机制造商出售预捆绑 Windows 的机器。此外，Windows 是一个伟大的操作系统，那么为什么用户会搜索其他东西呢？

其实我觉得普通用户不应该。安装一个新的操作系统可能会让人不知所措，而 Linux 可能不会给他们带来任何好处。

实际上，普通用户只需要一个网络浏览器、一个文件管理器和一个办公套件；没有什么新奇的东西会被闲置。

# 作为一名开发人员，你为什么要转到 Linux？

众所周知，许多开发人员使用 Linux(虽然不是大多数)。

根据 2020 年堆栈溢出[调查](https://insights.stackoverflow.com/survey/2020)，开发者使用的操作系统。Windows 45.8%，macOS 27.5，Linux 26.6

然而，相对于总桌面用户市场份额来说，这是一个非常受欢迎的增长(实际上是+1068%)。而且也不是因为开发者觉得用 Linux 很酷，看着他们的`[htop](https://htop.dev/)` [输出](https://htop.dev/)假装在矩阵里。

如你所见，macOS 在编程领域的份额要高得多(实际上是+65%)。这是因为出于安全和隐私的考虑，公司经常为员工配备苹果设备。此外，macOS 也是一个类似于 Unix 的操作系统，因此，我下面列出的许多好处也适用于它。

以下是我认为开发人员应该进行这种转换的原因，或者如果有可能的话，至少应该尝试一下。出于安全考虑或业务协议，一些公司可能会为员工选择操作系统。

# 强大的自动化功能

自动化长时间的、重复的任务就像编写一个脚本为您完成所有任务一样简单。

虽然其他流行的选择是 Python、Perl 和 JavaScript(带 NodeJS ),但是最广泛使用的脚本语言(和默认 shell)是 Bash。

然后，您可以使用 [Cron jobs](https://en.m.wikipedia.org/wiki/Cron) 进行任务调度。Cron 是一个在指定的时间、日期或间隔运行任务(shell 命令或程序)的程序。

另一个很大的好处是，您可以轻松地与脚本中的所有内容进行交互。你不需要任何花哨的插件或驱动程序来做这件事。

# 一切都是一个文件

这个概念非常适合任务自动化。类 Unix 操作系统使用的文件模型意味着您可以从命令行轻松访问每个物理设备或软件端点。设备通常存储在`/dev`文件夹中。

通过`mount <device> <location>`命令可以轻松安装存储设备。然后可以像访问文件系统的一部分一样访问它们。要卸载它们，使用下面的命令:`umount <mount point>`。

可以使用特定的实用程序从这些设备读取数据或向其写入数据，从而允许您访问 CPU、电源、硬件模块等并与之通信。

你也可以创建[虚拟设备](https://en.wikipedia.org/wiki/Virtual_device)作为你的程序与他人接口的方式。例如，您可以创建一个虚拟摄像机来传输其输出，并对原始视频应用一些特殊效果。

# Linux 无处不在

这可能看起来不明显，因为您通常看不到它，但是大多数服务器实际上都运行类似 Unix 的操作系统(其中一半以上是 Linux)。因为 macOS 的分数太低了，不到 0.1%，所以我懒得把它插入图表。

根据[w3techs.com](https://w3techs.com/technologies/overview/operating_system)，网站托管的操作系统份额。注意:一个网站可以使用一个以上的操作系统。类 UNIX 76.7%，Windows 23.5%

> 作为超级计算机的操作系统，Linux 发行版也占主导地位。截至 2017 年 11 月， [*500 强榜单*](https://en.wikipedia.org/wiki/TOP500) *上的所有超级计算机都运行 Linux 的某种变体。”(*[](https://en.wikipedia.org/wiki/Linux#Servers,_mainframes_and_supercomputers)**)**

*作为一名开发人员，您有时必须进行服务器配置或维护。这就是了解您的服务所基于的技术是一大优势的地方。如果你熟悉环境，你就知道在大多数情况下该怎么做(或者在哪里搜索你需要的信息)。*

*Linux 也存在于许多嵌入式设备中，如智能电视、家庭助理、汽车车载信息娱乐系统、路由器、航天器飞行软件、Raspberry Pis 等等。*

*你可能没有意识到，Android 也是基于 Linux 内核的，它占据了移动操作系统 72%的市场份额。([statcounter.com](https://gs.statcounter.com/os-market-share/mobile/worldwide))*

# *环境定制能力*

*在你自己的环境中感到舒适通常和你的实际技能效率一样重要。Linux 发行版是高度可定制的，如果你知道如何做正确的调整的话。*

*至于 GUI，你可以改变[桌面环境](https://en.wikipedia.org/wiki/Desktop_environment) (DE)或者[窗口管理器](https://wiki.archlinux.org/title/window_manager)。许多 DEs 实际上允许你随意修改、添加或删除任何种类的小部件(面板、栏、菜单等等)。*

*此外，可定制性不仅限于 GUI，还包括您可以通过安装第三方软件来实现或获得的有用功能，这些功能可以改善整体用户体验和系统功能。例如，您可以设置强大的键绑定和快捷键，甚至可以覆盖系统默认设置。您可以通过编辑正确的文件或向现有流程添加脚本化任务来调整系统的工作和行为。*

*大多数发行版很少甚至没有膨胀软件，但如果有，如果你真的想的话，你可以很容易地摆脱它。不过，这无关紧要，你可以不去管它。*

# *通常附带许多开发工具*

*大多数 Linux 发行版都默认带有许多开发者可能需要的有用程序。几乎总是有某种 C/C++编译器用于在安装时构建程序。它们通常附带一个已经安装了核心库的 Python 解释器。 [Perl](https://www.perl.org/) 、 [Ruby](https://www.ruby-lang.org/en/) 和 [Apache](https://www.apache.org/) 也是默认常来。一些更加用户友好的发行版，比如 [PeppermintOS](https://peppermintos.com/) ，实际上附带了一个办公套件、一个 [torrent](https://en.wikipedia.org/wiki/BitTorrent) 客户端和许多其他实用程序。*

*总有一些新的工具可以让你的生活变得更轻松。*

# *易于安装*

*您可以通过[软件包管理器](https://en.wikipedia.org/wiki/Package_manager)在一个简短的 shell 命令中轻松安装您需要的大部分软件。最常见的有:*

*   *`apt install <package name>`*
*   *`pacman -S <package name>`*
*   *`rpm -i <package name>.rpm`*

*如果没有可以安装的软件包，您可以添加一个包含该软件包的存储库，或者选择安装程序脚本，这些脚本通常可以在软件的页面上找到。*

*如果你运气不好，你总是可以从源代码安装。这个过程可能有点乏味，尽管在 Linux 上比在其他系统上更容易。还有其他安装软件的方式，比如用 [Wine](https://www.winehq.org/) 直接运行 Windows `.exe`程序。*

# *用于 Linux 的 Windows 子系统(WSL)*

*[WSL](https://en.wikipedia.org/wiki/Windows_Subsystem_for_Linux) 是一个兼容层，允许您在 Windows 系统上本地运行 Linux 可执行文件。它基本上是一个可以通过 Bash shell 访问的 Linux 环境，您可以从微软商店安装它，并将其用作常规应用程序。如果您不喜欢直接在磁盘上安装操作系统(有破坏的风险)，您真的应该尝试一下 WSL。*

# *结论*

*不管你使用什么工具，软件工程和开发都是你可以获得的技能。尽管如此，环境、实用程序和操作流程对高效开发高度负责，并且可以极大地改善您的体验。*

*这篇文章并不意味着强迫任何人转向 Linux。毕竟，舒适度和生产率完全是主观的。尽管如此，我还是强烈建议你尝试一下新技术。*

> *"如果你只有一把锤子，所有的东西看起来都像钉子."*
> 
> *—亚伯拉罕·卡普兰*

*我想补充如下:*

*即使你可以用锤子敲进一颗螺丝钉，你也可以用螺丝刀。*

*如果我错过了什么，一定要让我知道。*

*感谢阅读！*