# Android 开发中的 8 个常见错误

> 原文：<https://betterprogramming.pub/8-common-mistakes-in-android-development-2edcf5179ec0>

## 构建优秀应用程序的陷阱

![](img/3118f3bb4fa822e0c809f92335a99f2b.png)

由[川上正风](https://unsplash.com/@loli_oni?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

# 1.东西应该在它们应该在的地方(线、颜色)

随着社区的多样化，Android 生态系统在全球范围内有了巨大的增长。来自不同背景的人，残疾人，希望拥有夜间模式等奇特功能的人，以及更多在日常生活中使用 Android 应用的人。

为如此多样化的社区开发应用并不是一件容易的事情。这里我不是在谈论高层架构。相比之下，它是关于简单的东西，如字符串，颜色，尺寸等。这将极大地影响现代 Android 开发。

人们通常觉得使用母语的应用程序很舒服。至关重要的一步是将所有的字符串保存在一个文件中(通常是`strings.xml`)，以便快速添加不同语言的字符串文件。

这也适用于颜色、尺寸和样式，所以当你决定做一些事情，如支持黑暗模式或调整布局以适应平板电脑视图时，很容易处理它们。底线是:在一个地方维护代码以便重用，而不是在任何需要的地方硬编码。

# 2.不使用片段

在 Android 开发的早期，建议每个屏幕使用单独的活动。随着时间的推移，开发人员会因此面临不同的问题。此外，活动作为应用程序的入口点也是一个漏洞。

如果你已经是一名 Android 开发人员，那么你会知道几年前使用 activities 是有意义的，那时 Fragments API 还没有那么成熟。

快进到 2020 年，Android 团队建议使用片段来设计每个屏幕，并在整个应用程序中维护单个或少数几个活动来托管片段。这就是众所周知的单活动架构。

遵循这种架构将会减少来自应用程序外部的大量交互。新的 [Jetpack 导航组件](https://medium.com/better-programming/jitpack-navigation-component-in-android-944165c35f54)主要基于单活动架构。Fragments API 将使您的生活更加简单。也许几年后，Android 开发将会从活动转向片断。

# 3.不使用数据绑定或视图绑定

Android 开发环境从一开始就基于三种类型的文件:XML、Kotlin 和 Java。XML 文件包含与设计相关的一切，而 Kotlin/Java 文件包含除设计部分之外的任何内容。

最终，UI 和业务逻辑需要链接起来，这就是数据绑定和视图绑定发挥关键作用的地方。UI 和业务逻辑之间的链接是从臭名昭著的`[findviewbyid](https://medium.com/better-programming/the-evolution-of-view-linking-in-android-d6219678740d)`函数开始的。

但是[视图绑定](https://medium.com/better-programming/everything-you-should-know-about-viewbinding-in-android-52552af9e8ba)和数据绑定是解决这个问题的推荐方案。视图绑定的主要目的是解决运行时类型和空安全的链接问题。

[数据绑定](https://medium.com/@sgkantamani/data-binding-in-android-4ff7bba93a2c)是为了更大的利益。它允许您使用声明性格式而不是编程方式将布局中的 UI 组件绑定到数据源。

# 4.不使用 Kotlin 和协程

谷歌宣布推荐 Kotlin 作为开发 Android 应用程序的语言已经有几年了。这是一个变革性的决定:Kotlin 解决了 Java 中的痛点，可以减轻开发人员的负担。

使用 Kotlin 进行 Android 开发打开了新的大门，提供了诸如[扩展](https://medium.com/better-programming/advanced-android-programming-with-kotlin-5e40b1be22bb)、作用域函数、数据类、对象关键字、空安全等特性。除了 Android 开发，还可以用 Kotlin 进入多平台和服务器端开发。

异步编程在移动开发中起着关键作用。在早期阶段，我们使用 AsyncTask。随着时间的推移，RxJava 出现了，这是一个转型性的变化。但是 RxJava 有一个深度学习曲线和一个完全不同的回调方法。

然后是[协程](https://medium.com/better-programming/kotlin-coroutines-from-basics-to-advanced-ad3eb1421006)，一个用简单方法实现异步编程的 Kotlin-y 解决方案。如今，协程已经成为实现异步任务的标准解决方案。强大的功能和简单的实现使其适应性更强。

Kotlin 使您的开发变得简单明了，而协程允许您顺序执行异步任务，而无需学习任何新东西。在您的开发中使用它们只会产生更有生产力和更有效的输出。

# 5.设计错误

## 低估 ConstraintLayout

`[ConstraintLayout](https://medium.com/android-dev-hacks/exploring-constraint-layout-in-jetpack-compose-67b82123c28b)`结合了许多便利的功能，如指南、障碍、群组、纵横比、流动、图层等。有了所有这些特性，`ConstraintLayout`几乎可以绘制每一个屏幕(从简单到复杂的用例)。

`ConstraintLayout`不同于相对和线性布局。停止用同样的方式对待他们。我们可以创建没有嵌套层次的平面布局。平面布局设计导致在视图上绘制较少的图层。

## 过度使用 ConstraintLayout

强大的功能有被滥用的危险。在可以用`FrameLayout`或`LinearLayout`设计的 UI 中使用`ConstraintLayout`是一种荒谬的方法。

## 害怕运动布局

`ConstraintLayout`是设计复杂用例的正确选择，但不是实现动画和过渡。`[MotionLayout](https://medium.com/better-programming/beginners-guide-to-motion-layout-732395a7de7e)`是做这件事的有效办法。

`MotionLayout`是`ConstraintLayout`的一个子类，包含了它所有突出的特性，它是完全声明性的，能够在 XML 中实现复杂的转换。它向后兼容 API level 14，这意味着它覆盖了 99%的用例。

Android Studio 4.0 中新的`MotionLayout`编辑器使得使用`MotionLayout`变得更加容易。它提供了一个奇特的环境来实现过渡、`MotionScenes`等等。

MotionLayout 不包含复杂的计算和算法。相反，它是一种简单的声明性方法，通过 Android Studio 中一个新的花哨编辑器来实现动画和过渡。

# 6.没有意识到安全缺陷

## 存储敏感数据

在 Android 共享偏好设置、数据库或本地存储中存储敏感数据存在风险。很容易被黑。许多开发人员并没有意识到这一点，而是使用共享偏好来存储用户敏感数据。

这可以通过使用新的[数据存储库](https://medium.com/better-programming/jetpack-datastore-improved-data-storage-system-adec129b6e48)库或[加密首选项](https://medium.com/android-dev-hacks/securing-sensitive-data-in-android-e261687ab66e)库或通过自己实现加密来解决。

## 安全通信

许多 Android 开发者认为使用 HTTPS 可以使他们与服务器的通信安全。但事实并非如此。许多黑客通过干扰通信来误导服务器和客户端。这被称为[中间人攻击](https://medium.com/better-programming/secure-communication-with-the-server-from-your-android-client-with-certificate-pinning-5f53cea55972)。

为了建立到服务器的安全线路，我们需要实现[证书锁定](https://medium.com/better-programming/secure-communication-with-the-server-from-your-android-client-with-certificate-pinning-5f53cea55972)。

# 7.不了解 Android Studio 的功能

除非你学会如何正确使用，否则武器有多强大并不重要。作为开发人员，我们很幸运有一个像 Android Studio 这样强大的工具，但你应该知道如何有效地使用它。

有许多隐藏的功能，如方便的[快捷方式](https://medium.com/android-dev-hacks/android-studio-tips-tricks-for-beginners-703bc9a36259)、[实时模板、文件模板](https://medium.com/@sgkantamani/constraint-layout-with-live-templates-do-less-finish-more-b62397725835)、预定义的项目结构、代码生成器插件、[定制](https://medium.com/better-programming/customize-the-logcat-in-android-studio-8f58d39222ef)等等。我们还有一个[数据库检查器](https://medium.com/better-programming/exploring-the-database-inspector-in-android-studio-de0026024895)、[布局检查器](https://medium.com/better-programming/explore-the-new-layout-inspector-in-android-studio-4-0-6f9ffb1505f2)、剖析器等等，以便在运行时提高效率。

Android Studio 还为几个库提供了工具支持，如查看应用程序导航图的导航编辑器和以奇特方式实现有效动画和过渡的运动编辑器。

# 8.不使用 Jetpack 库

> “Jetpack 是一套库，可帮助开发人员遵循最佳实践，减少样板代码，并编写跨 Android 版本和设备一致工作的代码，以便开发人员可以专注于他们关心的代码。”— [Android Jetpack](https://developer.android.com/jetpack?hl=fr)

JetPack 库涵盖了主要功能，如用于分页的 [paging3](https://medium.com/android-dev-hacks/exploring-paging3-part-1-653dc537a69a) 、用于本地数据库的 [Room](https://medium.com/better-programming/how-to-use-the-room-persistence-library-with-kotlin-flow-c73f461a0819) 、用于长期运行后台任务的 [WorkManager](https://medium.com/swlh/workmanager-basics-how-to-use-workmanager-with-rxjava2-kotlin-coroutines-c2a317197038) 、用于改进数据存储的 [DataStore](https://medium.com/better-programming/jetpack-datastore-improved-data-storage-system-adec129b6e48) 、用于 DI 的[handle](https://medium.com/better-programming/hilt-a-new-dependency-injection-library-for-android-e6e00e719aeb)、用于在应用 UI 中导航的[导航组件](https://medium.com/better-programming/jitpack-navigation-component-in-android-944165c35f54)、用于减少应用启动时间的[应用启动](https://medium.com/better-programming/app-startup-new-architecture-component-d115b062a701)等等。

所有这些库都是为了保持性能和易用性而构建的，以便用更少的代码实现复杂的任务。

目前就这些。希望你学到了有用的东西。感谢阅读！