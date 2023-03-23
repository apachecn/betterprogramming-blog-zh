# 面向开发人员的 M1 MAC 现状——安装指南和基准测试

> 原文：<https://betterprogramming.pub/the-state-of-m1-macs-for-developers-481947dd5aaf>

欢迎来到第十版咖啡字节，更好的编程每日文摘。

早在 2020 年 11 月，苹果发布了其内部基于 ARM 的 Mac 芯片。一年和两个版本之后，随着过渡的顺利进行，苹果芯片的未来看起来更加光明。

如果您希望让您的 M1 Mac 为日常工作流做好准备，或者需要一些灵感来迁移到 beast，我们有一系列配置指南、基准测试帖子和开发人员的实验，让您做出改变。

![](img/6aa1f6f1c5d54c5401726050b0c9f365.png)

克莱顿·马尔奎斯特在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

*   对于历史爱好者来说，当 M1 MacBook 首次推出时， [Docker 不被支持](/macbook-m1-breaks-docker-development-14566ab6fa2e)。Andreas Heissenberger 的指南描述了这些问题以及中间解决方案。
*   [苹果新的 M1 芯片是一个机器学习怪兽](https://towardsdatascience.com/apples-new-m1-chip-is-a-machine-learning-beast-70ca8bfa6203)——[Daniel Bourke](https://medium.com/u/dbc019e228f5?source=post_page-----481947dd5aaf--------------------------------)通过对(几乎)顶级的基于英特尔的 16 英寸 MacBook Pro 与新的苹果硅 MacBook Air 和 13 英寸 MacBook Pro 进行速度测试，进行了彻底的分析。在试用 CreateML 应用程序进行模型训练时，M1 Mac 轻松获胜。
*   [基准测试 M1 vs 至强 vs 酷睿 i5 vs K80 和 T4](https://towardsdatascience.com/benchmark-m1-vs-xeon-vs-core-i5-vs-k80-and-t4-e3802f27421c) — [Fabrice Daniel](https://medium.com/u/926442548db0?source=post_page-----481947dd5aaf--------------------------------) 将苹果硅 M1 CPU 和 GPU 性能与 MLP、CNN 和 LSTM 机型上的高端配置进行比较。
*   接下来， [Martin Albrecht](https://medium.com/u/de0744a7d31d?source=post_page-----481947dd5aaf--------------------------------) 讨论了[在苹果硅 M1 MAC 电脑上运行 Ruby 程序时遇到的问题、发现和解决方法](/ruby-on-apple-silicon-m1-macs-fb159849b2f5)。
*   阅读 [Elye](https://medium.com/u/5742b4fcf89e?source=post_page-----481947dd5aaf--------------------------------) 的[对苹果 M1 芯片设备](https://medium.com/mobile-app-development-publication/first-view-for-android-development-on-apple-m1-chip-device-e9d3d52b27aa)上 Android 开发的第一个观点，他在[设置 Android 模拟器](https://medium.com/mobile-app-development-publication/my-journey-to-setup-android-emulator-for-m1-macbook-pro-b8365321b3e7)时面临的问题，以及[加速 Android 编译的技巧](https://medium.com/mobile-app-development-publication/speed-up-android-compilation-in-apple-m1-device-6dbca65b4bb9)。
*   从那以后，[面向安卓工程师的](https://gpeal.medium.com/the-m1-pro-for-android-engineers-a144093aa1ec)M1 Pro 走过了一段漫长的路。 [Gabriel Peal](https://medium.com/u/bfa26a83c4b6?source=post_page-----481947dd5aaf--------------------------------) 简洁地展示了性能改进。
*   等等！那`SonarQube`呢？ [Catalin Patrascu](https://medium.com/u/4c60ea699637?source=post_page-----481947dd5aaf--------------------------------) 的操作指南带领我们在 Apple Silicon Mac 上使用 Docker 对 SonarQube 和 Flutter 进行[设置。](/flutter-and-sonarqube-for-static-code-analysis-51368a85c51c)
*   与此同时， [David J Harding](https://medium.com/u/79218fc2278f?source=post_page-----481947dd5aaf--------------------------------) 的[在没有 Rosetta guide 的 M1 Mac 上开发 React 原生应用](https://medium.com/@davidjasonharding/developing-a-react-native-app-on-an-m1-mac-without-rosetta-29fcc7314d70)涵盖了跨平台生态系统的其他领域。
*   您现有的 x86–64 机器是使用[的流浪者](https://www.vagrantup.com/)和[的 Virtualbox](https://www.virtualbox.org/) 构建的吗？前往[在新的 Mac M1](/managing-virtual-machines-under-vagrant-on-a-mac-m1-aebc650bc12c) 上运行虚拟机。James Cundle 的指南，帮助你找到工作虚拟机的替代品。
*   [杰森·斯特奇斯](https://medium.com/u/bcfc16c73f97?source=post_page-----481947dd5aaf--------------------------------)为我们[提供了一个在采用 M1 架构的新 MacBook Pro 上设置游戏开发、编码和创作环境](/setting-up-a-macbook-m1-development-machine-cda22a92fa22)的指南。
*   前往 [Matt Wang](https://medium.com/u/6478e9f24f73?source=post_page-----481947dd5aaf--------------------------------) 的使用 `[minikube](https://matt-wxw.medium.com/kubernetes-on-apple-m1-using-minikube-21ea1b08e7d6)`在苹果 M1 上设置 [Kubernetes 的指南。](https://matt-wxw.medium.com/kubernetes-on-apple-m1-using-minikube-21ea1b08e7d6)
*   数据科学家会喜欢这些指南——1)[在苹果 M1 MAC 电脑上安装 XGBoost 和 light GBM](https://towardsdatascience.com/install-xgboost-and-lightgbm-on-apple-m1-macs-cb75180a2dda)。2) [用新的金属插件在苹果 M1 上安装 tensor flow](/installing-tensorflow-on-apple-m1-with-new-metal-plugin-6d3cb9cb00ca)和[由](/how-to-install-pytorch-on-apple-m1-series-512b3ad9bc6) [Nikos Kafritsas](https://medium.com/u/bec849d9e1d2?source=post_page-----481947dd5aaf--------------------------------) 在苹果 M1 系列上安装 PyTorch
*   需要更多吗？[Dario rade CII](https://medium.com/u/689ba04bb8be?source=post_page-----481947dd5aaf--------------------------------)向我们展示了[M1 与 i9–9880h](https://towardsdatascience.com/m1-macbook-pro-vs-intel-i9-macbook-pro-ultimate-data-science-comparison-dde8fc32b5df)之间的终极数据科学比较——与 synthetic benchmarks、Python、Numpy、Pandas 和 Scikit Learn 的性能比较。
*   如果您厌倦了顺序编程，想尝试并行计算，该怎么办？不要担心！ [Mihail-Iulian Pleșa](https://medium.com/u/169c5dc9497d?source=post_page-----481947dd5aaf--------------------------------) 为您提供了一份指南，帮助您[将 Open MPI 与 CLion for Apple M1](/integrating-open-mpi-with-clion-on-apple-m1-76b7815c27f2) 集成。
*   最后，[泰特·加尔布雷斯](https://medium.com/u/1d89fcc3b0d?source=post_page-----481947dd5aaf--------------------------------)草草记下了[开发者对新 M1 MAC 电脑](/5-crucial-developer-tweaks-for-new-m1-macs-a77c9990a985)的 5 处调整。它还包括将您现有的工作流程集成到新平台的一般提示。

仅此而已。感谢阅读。