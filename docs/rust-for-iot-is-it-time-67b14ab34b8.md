# 物联网生锈:是时候了吗？

> 原文：<https://betterprogramming.pub/rust-for-iot-is-it-time-67b14ab34b8>

## 探索 esp32-c3 上的锈迹

![](img/f134d553d54dc05af6fd86fc83d5e55d.png)

托马斯·博尔曼斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

Rust 已经存在一段时间了，我们都听说过这种语言的好处，特别是在内存安全方面。在过去的几年里，我写了相当多的 C 代码，但一直想使用一种更现代的语言。所以，当我第一次听说 Rust 的时候，我希望它能改变游戏规则。现在这种语言似乎正在加速发展，微软 Azure 的 CTO 甚至说[我们不应该再用 C 或 C++开始新的项目](https://www.zdnet.com/article/programming-languages-its-time-to-stop-using-c-and-c-for-new-projects-says-microsoft-azure-cto/)。

我有一些问题:

*   那么，在物联网领域，特别是在裸机上进行嵌入式开发时，在实践中启动一个基于 Rust 的新项目会是什么样子呢？
*   现在是时候了吗？我们可以在生产环境中的新项目中开始使用这种有前途的语言吗，或者我们仍然缺乏对关键特性的支持吗？更重要的是，2022 年的开发体验相比 C 语言编程如何？
*   Rust 是否让我们在保持代码安全的同时，更快地开发更易维护的应用程序？

在本文中，我将探讨这些问题，并通过放大当今最受欢迎的硬件平台之一，并(尝试)为其创建一个简单的物联网应用程序，收集我对 2022 年物联网 Rust 状态的想法。

如果你想跟随代码，完整的代码清单可以在这里找到: [rust-iot-2022](https://github.com/mfiumara/rust-iot-2022) 。

# 硬件和设置

首先，我们需要一些硬件。在当今所有物联网硬件平台中，Espressif 的 ESP32 系列肯定是最受欢迎的平台之一。随着业余爱好者社区的大型聚会，它也在工业物联网环境中赢得了坚实的基础。我决定试用一下 [ESP32 C3 开发套件](https://docs.espressif.com/projects/esp-idf/en/latest/esp32c3/hw-reference/esp32c3/user-guide-devkitm-1.html)，这是一个基于 RISC-V 的架构，具有 Wi-Fi 和蓝牙功能。

当开始四处寻找支持时，您很快就会以 ESP IDF(物联网开发框架)作为主要开发框架，它拥有一些由开源 ESP 社区为其编写的可靠的 [Rust 绑定](https://github.com/esp-rs/esp-idf-svc)。为了进行设置，我简单地遵循了来自[的这个模板项目](https://github.com/esp-rs/esp-idf-template)的指南(确保您的系统中安装了那里列出的依赖项)并执行了以下命令:

Rust 设置和项目生成

这个命令包安装了 Rust 的夜间工具链以及编译和运行我们的应用程序所需的所有依赖项。毕竟，安装了依赖项之后，我们生成演示项目，编译，刷新，最后运行应用程序，所有这些都在六分钟的时间内完成。与我在 C 或 C++中进行的一些项目设置相比，这种设置非常容易——在 C 或 c++中，我必须手动搜索并安装正确的工具链——这是 Rust 的第一次胜利，并显示了 Rust 的构建系统是如何工作的。

# 开发工作流程

为了简单起见，我决定从 VScode 开始，并安装了 [Rust analyzer 扩展](https://marketplace.visualstudio.com/items?itemName=rust-lang.rust-analyzer)，这就产生了一个开箱即用的自动完成器。不用再使用[编译命令](https://clangd.llvm.org/design/compile-commands)生成编译数据库，也不会总是缺少一个系统库:Rust 语言服务器简单地完成这项工作，并在您习惯 Rust 语法的同时帮助您。

现在，让我们看看导航 ESP-IDF 的生锈绑定是什么样子的。我通读了文档，并让 Rust 语言服务器完成它的工作，为每个函数参数建议应该填写什么。为了连接到您的本地 WiFi 网络，我使用了以下代码:

相当简单。让我们看看我们的二进制文件有多大，编译->闪存->运行循环有多快:

```
$ time cargo buildCompiling wireprobe-rs v0.1.0 (/Users/mfiumara/repos/esp-rust)Finished dev [optimized + debuginfo] target(s) in 1.27s
        1.31 real         1.15 user         0.15 sys$ time espflash --speed 406800 target/riscv32imc-esp-espidf/debug/wireprobe-rs
Serial port: /dev/tty.usbserial-10
Connecting...WARN setting baud rate higher than 115200 can cause issues.
Chip type:         ESP32-C3 (revision 3)
Crystal frequency: 40MHz
Flash size:        4MB
Features:          WiFi
MAC address:       10:91:a8:36:53:04
App/part. size:    1037024/4128768 bytes, 25.12%
[00:00:00] ########################################      12/12      segment 0x0
[00:00:00] ########################################       1/1       segment 0x8000
[00:00:25] ########################################     584/584     segment 0x10000
Flashing has completed!
       33.33 real         0.14 user         0.16 sys
```

我们的二进制文件变成大约 1MB，编译和刷新大约需要 40 秒。这是一个相当大的二进制图像，这可以解释，因为我们在 WiFi 堆栈中链接。关于闪烁的速度，这受到 UART 接口的最大速度 406800 位/秒的限制。这并不令人惊讶，但这是我们必须面对的问题:这似乎不是 Rust 的限制，而是硬件的限制。

# 做一些有用的事情

既然我们已经成功地在目标上运行了代码，那么让我们尝试做一些实际有用的事情，例如，连接到 MQTT 代理并尝试发送和接收一些消息。为此，我在初始化 WiFi 堆栈后添加了这几行代码来初始化和启动 MQTT 客户端:

我使用的是 emqx.io 的 public broker，它有一个很好的在线客户端，我们可以在那里看到我们的消息，并且可以使用一个简单的 GUI 向我们的设备发送消息，以测试我们的功能。

![](img/4b6cad7f2b37e734cc2470d7f1610fef.png)

在 emqx.io 上接收和发送 MQTT 消息

现在，为了实际发送和接收消息，我不得不做一些调查，因为我真的不知道如何仅从文档中实现这一点。幸运的是，围绕`esp-rs`绑定有一个相当活跃的[社区聊天](https://matrix.to/#/#esp-rs:matrix.org)，这个话题在聊天历史中经过。在我的第一个实现中，我试图在不处理任何来自 MQTT 堆栈的事件的情况下发布消息，这完全冻结了我的应用程序。根据一些参考资料，我想出了下面这段代码:

乍一看这段代码有点令人生畏，但是一旦你通读了它的语法，就会发现与更传统的 C 编程工作流相比，Rust 是多么强大:

1.  首先，我们使用`std::thread`生成一个线程，在其中监听传入的 MQTT 事件。没有代码花费在定义堆栈大小、配置线程优先级、定义调度行为、定义入口函数和传递变量上。我们只需`thread::spawn`，传递一个闭包，`move`我们的连接变量进入线程，我们就设置好了。
2.  在新生成的线程中，我们处理 MQTT 事件，并使用`match`专门寻找一个`Received`事件。我们在控制台中将内容打印为一个字符串，这次利用了`std::str`。在 C 语言中，我们必须手动操作`memcpy`，不要忘记字符串结束字符，否则会有打印出目标内存索引的风险，这说明了 Rust 的一些安全特性。
3.  最后，回到我们的`main`线程，我们订阅了一个主题并创建了一个无限循环，在这个循环中，我们连续发布到一个 MQTT 主题，中间有一秒钟的停顿。

这里还有改进的空间:我们没有处理任何不可预见的错误(只是简单地`unwrap()` -ing 它们),我们的嵌套 match 语句可以进行重构，但是作为概念的证明，这已经足够了，并且显示了我们可以在很少的几行代码中做多少事情。意识到我们仍然在微控制器上编程，感觉几乎是超现实的，更像是在编写云服务。这是好事。

# 是时候了吗？

所以…我们在 esp32-c3 上设置了一个简单的 Rust 应用程序，它可以在很短的时间内成功地向云应用程序发送和接收消息。我们使用 Rust 的标准库来更快地开发代码，同时保持安全性。我们没有做的事情:讨论无线更新、安全和加密、调试功能或内存优化，所有这些主题都需要在全力投入 Rust 之前进行评估(更不用说测试了，但这本身就是一篇文章)。然而，回顾到目前为止的经验，并从这个不起眼的项目中推断，我确实认为上述主题无需过多的努力就可以解决。所以我称之为:

是时候了。

现在，这并不意味着明天你需要开始把你的整个代码库从 C / C++重写到 Rust。这在很大程度上取决于您的芯片组所能获得的支持类型:是否有一个活跃的社区/源代码是否被积极地维护，是否有制造商对开源努力的官方支持，以及是否有良好的绑定？那就想尽一切办法去争取吧。

有些情况下，你可能需要自己解决问题，并为开源社区做出贡献，但是，如果你不介意，那么从长远来看，现在采用 Rust 将会是一个不错的投资回报。

感谢阅读到最后！

![](img/75ecb9b0b2ae55903066ff4427b8ceea.png)

# 资源

*   参考代号:[https://github.com/mfiumara/rust-iot-2022](https://github.com/mfiumara/rust-iot-2022)
*   ESP Rust Github 资源:[https://github.com/esp-rs](https://github.com/esp-rs)
*   ESP Rust 社区聊天:[https://matrix.to/#/#esp-rs:matrix.org](https://matrix.to/#/#esp-rs:matrix.org)