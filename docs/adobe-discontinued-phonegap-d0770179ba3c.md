# Adobe 停止了 PhoneGap

> 原文：<https://betterprogramming.pub/adobe-discontinued-phonegap-d0770179ba3c>

## 这是混合 app 时代的终结吗？

![](img/4d93467781865a6868c4d4398d86ba2d.png)

Vojtech Bruzek 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

我已经是一名活跃的 IT 和编程培训师和教师超过 15 年了…在这段时间里发生了很多变化。我仍然记得我的第一门 [PhoneGap](https://phonegap.com/) 课程，它是为罗马一家重要的软件公司设计的，目的是实现那个时代最流行的目标:使用现有的编程语言开发移动跨平台应用。

这个目标在今天是否变得毫无意义或者根本不可能实现？

# 多年来的变化:混合应用、PWAs 等等

即使这听起来很奇怪，但事情与我使用 [Cordova](https://cordova.apache.org/) /PhoneGap 和 jQuery mobile 开发我的第一个应用程序时非常相似(是的！).如果你检查智能手机和平板电脑的功能，技术带来了很多硬件创新，但如果你看看软件工程方面，唯一真正的进步是工具、编程语言、框架、堆栈、ide 以及与软件开发相关的一切都变得更好了。好多了。在这种情况下并没有真正的突破。如今，原生应用是获得严肃应用的唯一解决方案。

等等，我不是说混合应用和进步应用是垃圾。我只是说，如果你需要一个企业应用程序，一个移动游戏，一个真正新鲜/干净/动画的 UI，让你的应用程序闪闪发光，并与其他应用程序区分开来，唯一的解决方案仍然是本地化。唯一真正突破的是，你可以在 Android 上更喜欢 Kotlin 而不是 Java，在 iOS 上更喜欢 Swift 而不是 Objective-C。

其他解决方案仍然不错，但并不惊人。是的，您可以基于 Angular 进行混合开发，它很酷，比 jQuery 好得多。是的，您可以基于 React Native 和 compile to native 进行开发。是的，你可以学飞镖，跟着现代的舞步走。是的，你甚至可以选择 NativeScript，但是…听我说。我使用混合技术和其他跨平台技术开发了许多应用程序。每次到最后，我还是觉得应该选择 native——即使它更贵，需要相当大的努力。

# 什么是科尔多瓦…和 PhoneGap？

科尔多瓦创造了奇迹。你在混合应用上看到的一切都是基于 Cordova 的。科尔多瓦是开启一切的开源项目的名字。它可以让你的 UI 基于像 HTML/CSS/JS 这样的 web 技术，甚至更进一步，它允许你使用像照相机、地址簿和其他任何东西这样的设备功能。

PhoneGap 只不过是 Adobe 为包装科尔多瓦而伪造的产品的商业名称。所以开源的项目是 Cordova，而 Adobe 的产品叫 PhoneGap。PhoneGap 根本不是猜测。这是一个真正的产品，包括企业支持、构建应用程序和在商店上发布的工具等等。这不仅仅是科尔多瓦的包装。这是一项包括科尔多瓦的技术，在它上面添加糖——大量的糖。

# Adobe 正在为其他人释放 Cordova 的功能

正如你在本文中看到的[，Adobe 将于 2020 年 10 月 1 日停止 PhoneGap。作为一名开发人员，这引发了一个问题:“如果 PhoneGap 不复存在，我如何才能开发基于‘替代’技术的应用？”](https://blog.phonegap.com/update-for-customers-using-phonegap-and-phonegap-build-cc701c77502c)

首先，我最近没用过 PhoneGap。我开发和制作的所有东西都是基于 Cordova 的，因为我真的没有机会使用 Adobe 的东西。此外，我经常使用 Ionic framework 和 Angular，它们比 Adobe 产品更喜欢 Cordova。如今，Ionic 实际上使用的是[电容](https://capacitorjs.com)，旧 Cordova JS 的 Ionic 包装。我必须明确这一点，因为电容器更接近于整个事情，而不仅仅是科尔多瓦的包装，并集成了几个功能和工具，以产生具有 Ionic 框架的混合应用程序。

# 最终考虑

Adobe 对 PhoneGap 的告别更像是一种解脱，为企业公司和框架打开了几个机会，并允许我们将“科尔多瓦魔法”封装到每一种现代定制技术中。

今天的移动应用程序开发更加开放，并为一个分支的变化做好了准备:Ionic，NativeScript，React Native，Progressive Web 应用程序，等等。看起来这个工具、框架和库的集合比以往任何时候都更加丰富…或者可能只是一团乱麻？