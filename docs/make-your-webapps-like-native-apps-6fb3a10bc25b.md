# 让您的 Web 应用程序像本机应用程序一样

> 原文：<https://betterprogramming.pub/make-your-webapps-like-native-apps-6fb3a10bc25b>

## 用 JavaScript 模糊本地和混合之间的界限

![](img/ec48097688835214c5ef5def95376fde.png)

Pixabay 发自 Pexels 的照片:[https://www . Pexels . com/photo/Facebook-application-icon-147413/](https://www.pexels.com/photo/facebook-application-icon-147413/)

当开发一个移动应用程序时，我们有两个主要的选择:创建原生的或者混合的。如果您的应用程序不需要大量的硬件能力，通常最好创建一个混合应用程序。这样，仅使用 JavaScript 和 HTML，我们就可以拥有一个同时服务于 web 和移动的应用程序。

这篇文章将向你展示在大多数现代浏览器中可用的十个 API，它们将模糊本地和混合应用之间的界限。

这些 API 并不是 100%兼容所有的网络浏览器，但是它们在大多数情况下都能很好地工作。

# 1.振动 API

这个 API 让 WebApps 能够访问设备的振动硬件(如果它存在的话),如果设备不支持它，它就什么也不做。你可以用这个 API 让一个设备在你需要的时候振动。

振动 API js 示例。

# 2.通知 API

该 API 用于向用户显示桌面通知，并在应用程序中发生重大事件时提醒用户。

要使用它，你首先要检查的是你是否有权限这样做。您可以通过下面的代码看到如何做到这一点:

通知 API js 示例。

如果您没有权限，可以使用`Notification.requestPermission()`向用户请求:

通知 API js 示例。

授予权限后，您可以使用以下代码创建通知:

通知 API js 示例。

# 3.电池 API

电池 API 可以监控设备的电池电量，并在电池电量或充电状态发生变化时发出通知。

电池 API 公开了用于处理电池电量变化的事件处理程序。

例如，为您的`WebApp`提供低功耗模式，或者警告用户(使用通知 API)电池电量即将耗尽，以便他们可以采取适当的措施，例如保存他们正在做的事情。

电池 API js 示例。

# 4.地理定位 API

地理位置 API 是一个基于浏览器的 API，允许网站请求用户的物理位置信息(纬度和经度)。

通过调用`navigator.geolocation`来访问地理定位 API 这将导致用户的浏览器询问他们是否允许访问他们的位置数据。

所有主流浏览器都支持地理定位 API。

这里有一个例子:

地理定位 API js 示例。

# 5.游戏手柄 API

游戏手柄 API 是浏览器访问和响应来自游戏手柄或其他控制器设备的信号的一种方式，通常通过 USB 连接。

游戏手柄 API 引入了窗口对象上的事件来读取游戏手柄和控制器的状态。除了这些事件，API 还添加了一个 gamepad 对象，您可以使用它来查询连接的 Gamepad 的状态。

连接到游戏手柄时要做什么:

游戏手柄事件包括事件对象上的一个`<gamepad>`属性，该属性返回一个`<GamePad>`对象。

游戏手柄 API js 示例。

如何断开游戏手柄:

游戏手柄 API js 示例。

当第一个按钮被按下时，如何使用它来开始游戏，下面是一个基本的例子:

游戏手柄 API js 示例。

我们保持第一个游戏手柄连接([0])，并查询游戏手柄的第一个按钮`(gp.buttons[0].pressed)`是否被按下。

# 6.页面可见性 API

页面可见性 API 检测页面对用户是否可见或处于焦点上。

它在不同的场景中很有用，比如当一个特定的页面不可见时停止向服务器请求数据。当用户最小化网页或移动到不同的标签时。在这些情况下，API 发送一个`visibilityChange`事件。

可能的值有:

*   `visible`
*   `hidden`
*   `prerender`
*   `unloaded`

在以下示例中，您可以看到如何检测可见性变化:

页面可见性 API js 示例。

# 7.网络蓝牙 API

网络蓝牙 API 是一种从网络浏览器连接到蓝牙低能耗设备的方式。这个 API 可以帮助连接各种各样的设备。

这个 API 非常复杂和强大，所以我给你留了一个到它的官方文档的链接，以防你需要使用它，因为它超出了这篇假装简单易懂的文章的范围。

[https://developer . Mozilla . org/en-US/docs/Web/API/Web _ Bluetooth _ API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Bluetooth_API)

# 8.屏幕捕获 API

屏幕捕获 API 提供了对设备屏幕的访问，能够对当前屏幕进行截图。此外，这个 API 允许您选择一个屏幕或屏幕的一部分(比如一个窗口)来捕获为媒体流。

这个 API 使用起来很简单。它只有一个方法:`MediaDevices.getDisplayMedia()`，它的工作是询问用户选择一个屏幕或屏幕的一部分来以`MediaStream`的形式捕获。

截屏 API js 示例。

这个异步方法返回一个解析为 MediaStream 的承诺，media stream 对捕获的媒体进行流式传输。

# 9.图像捕获 API

这个 API 提供了一个简单的接口，用于从相机或用户的照片库中捕获照片和视频。

API 被设计得简单易用。例如，它可以从相机或用户的照片库中捕捉照片或视频。

`MediaDevices.getUserMedia()`方法提示用户允许使用媒体输入，产生包含所请求的媒体类型的媒体流。该流可以包括例如视频轨道(由硬件或虚拟视频源产生，如照相机、视频记录设备、屏幕共享服务等。)

这里有一个如何使用它的例子:

图像捕获 API js 示例。

# 10.WebXR 设备 API

WebXR 设备 API 向 Web 公开 VR 和 AR 设备的能力。这个特性是实验性的，并且只在上下文中(比如 HTTPS，在一些或所有支持的浏览器中。)

有关更多信息:

[https://developer . Mozilla . org/en-US/docs/Web/API/WebXR _ Device _ API](https://developer.mozilla.org/en-US/docs/Web/API/WebXR_Device_API)

# 最后的想法

我希望这些 API 能够让您了解这组 API 有多强大，以及您可以用它们做什么。要了解更多信息，我将官方文档留在下面，在那里您可以找到目前可用的所有浏览器 API。

# 参考

[https://developer.mozilla.org/en-US/docs/Web/API](https://developer.mozilla.org/en-US/docs/Web/API)