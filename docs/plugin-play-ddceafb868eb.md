# 构建你的第一个插件系统的艺术

> 原文：<https://betterprogramming.pub/plugin-play-ddceafb868eb>

## 问题# 19——创建自己的插件

嘿大家好，

欢迎来到《咖啡字节》(原名《编程字节》)第 19 版，这是 Better Programming 的每日文摘。

在这一期中，我们选择了一些突出的帖子来帮助你理解构建插件系统的过程。首先，插件或附加组件或扩展是一种软件，它带来了新的特性，并为你的应用程序提供了急需的可定制性。

*   [**构建自定义 IntelliJ 代码检查插件**](/build-a-custom-intellij-code-inspection-plugin-c6973a6d3f3)**—by[Ruben Quadros](https://medium.com/u/d57aec05f9f9?source=post_page-----ddceafb868eb--------------------------------)—有没有错过标注数据类参数却得到一个 JSON 解析错误？作为一名 Android 开发人员，我也经历过这种情况。Ruben 分享了一种不使用 lint checks 的方法。他创建了一个 IntelliJ 插件，可以检查 Kotlin 数据类，并实时警告您缺少`[SerializedName](https://www.javadoc.io/doc/com.google.code.gson/gson/2.6.2/com/google/gson/annotations/SerializedName.html)`注释！**
*   **[**如何创建自己的 Sublime Text 插件**](/how-to-create-your-own-sublime-text-plugin-2731e75f52d5) —作者[陈余 z .](https://medium.com/u/a7cf331e878b?source=post_page-----ddceafb868eb--------------------------------)**—想要构建一个日志记录器，用类似 JSON 的响应记录 API 请求？陈余使用 Python 为 Sublime 文本构建了一个定制的日志格式化程序。****
*   ****[**编写 Flutter 插件包**](https://medium.com/swlh/writing-flutter-plugin-package-1-5b5bfafce60f) 作者[Kristian Tuusjarvi](https://medium.com/u/f8ab17c24405?source=post_page-----ddceafb868eb--------------------------------)—Kristian 找不到一个像样的从 PDF 中提取文本的 Flutter 包。所以，他创建了自己的插件。查看这个故事，了解 Flutter 包的结构，以及如何创建和发布它。****
*   ****[**从零开始为 Flutter 版本管理构建一个浪子插件**](/build-a-fastlane-plugin-for-flutter-version-management-from-scratch-5df82bce391f) —作者[周天浩](https://medium.com/u/9681ff29fecf?source=post_page-----ddceafb868eb--------------------------------)——在过去，浪子对 Flutter 版本管理的支持并不存在。你猜怎么着？天豪为浪子构建了一个 Flutter 版本管理插件，以摆脱手动版本更改。请阅读他的教程，了解完整的分步实现。****
*   ****[**构建你的第一个 VueJS 插件**](https://javascript.plainenglish.io/a-step-by-step-guide-to-building-your-first-vuejs-plugin-72a50b289dd6) 的逐步指南——作者[Matt Maribojoc](https://medium.com/u/ee5c447ef63e?source=post_page-----ddceafb868eb--------------------------------)——创建你自己的插件可能看起来是一项令人生畏的任务，但事实并非如此。Matt 探索了创建简单和高级 Vue.js 插件的方法、工具和技术。****
*   ****[**如何设计软件——插件系统**](/how-to-design-software-plugins-d051ce1099b2)——作者[Joseph GEF Roh](https://medium.com/u/49d9c4a16fc5?source=post_page-----ddceafb868eb--------------------------------)——Joseph 的文章深入探讨了插件系统的设计构建过程。他还实现了一个使用插件构建的聊天机器人。****
*   ****[**创建 Android Studio 插件以切换“显示布局界限”**](https://proandroiddev.com/create-android-studio-plugin-to-toggle-show-layout-bounds-part-1-91fd530d633a) —作者[Paulina sadow ska](https://medium.com/u/a69b989518a?source=post_page-----ddceafb868eb--------------------------------)—Android Studio 插件如此之多，但您是否曾想创建一个满足您特定需求的插件？Paulina 指导我们创建一个简单的 Android Studio 插件，它可以与连接的设备进行交互，并切换我们很少使用的开发人员选项之一。****

****这一期到此结束。感谢阅读。****

****直到你下次喝咖啡，****

****[Anupam](https://medium.com/u/9833cc01f515?source=post_page-----ddceafb868eb--------------------------------) 以及更好的编程团队。****