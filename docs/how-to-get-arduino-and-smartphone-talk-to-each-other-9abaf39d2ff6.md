# 如何让 Arduino 和您的智能手机相互对话

> 原文：<https://betterprogramming.pub/how-to-get-arduino-and-smartphone-talk-to-each-other-9abaf39d2ff6>

## 各种 Arduino 通信方法的比较

![](img/cd594228276e14a352fe5834b8a7e216.png)

作者照片。用 [Canva](https://www.canva.com/) 创建。

想象一下，能够从世界任何地方与任何设备进行通信。你可以控制你的电视、灯、洗衣机、洗碗机、烤面包机、咖啡机——任何你想要的东西——只需要几块钱。

你可以用 Arduino 来做。我房间里有很多这样的东西，准备用来自动化我的另一个设备。

在我之前的一篇[文章](https://codeburst.io/4-simple-steps-to-make-your-home-come-to-life-for-under-10-585bb50d3f3)中，我介绍了如何使用 Arduino 作为开关来打开或关闭任何设备。

今天，我将讨论用于在 Arduino 和智能手机之间创建通信的各种无线方法的利弊。这不仅可以让你把手机当作一个开关，还可以让它发送特定的操作命令，并从 Arduino 获取状态。

# 发送和接收数据的各种方式

有多种方法可以在 Arduino 和智能手机之间建立通信，每种方法在某些情况下都很有用。以下是其中的几个。

## HTTP 请求

您可以使用 [HTTP 协议](https://www.geeksforgeeks.org/what-is-web-socket-and-how-it-is-different-from-the-http/#:~:text=HTTP%20can%20run%20on%20the,protocol%20such%20as%20TCP,%20SCTP.&text=WebSocket:%20WebSocket%20is%20bidirectional,%20a,ws://%20or%20wss://.)发送和接收数据。HTTP POST 和 GET 请求用于建立通信。为此，您可以使用一个 [ESP8266-NodeMCU](https://www.amazon.in/Lolin-NodeMCU-ESP8266-CP2102-Wireless/dp/B010O1G1ES/ref=sr_1_1?dchild=1&keywords=ESP8266+WiFi+Module&qid=1603484310&sr=8-1) 模块。这个模块可以方便连接，甚至有 GPIO 端口。

## HTTP(优点)

*   它是一个无状态平台(即，当消息被接收者接收时，设备之间的通信被关闭)。HTTP 最适合不需要频繁获取数据的情况。
*   它需要更少的计算能力。

## HTTP(缺点)

*   HTTP 相对来说比 web 套接字要慢。每次需要发送或接收数据时，都必须建立新的连接。

一个很好的类比是聊天:你发送一条信息，然后等待回复发送另一条信息。

[](https://randomnerdtutorials.com/esp32-http-get-post-arduino/) [## ESP32 HTTP GET 和 HTTP POST 与 Arduino IDE |随机书呆子教程

### 在本指南中，您将学习如何使用带有 Arduino IDE 的 ESP32 板进行 HTTP GET 和 HTTP POST 请求。我们会…

randomnerdtutorials.com](https://randomnerdtutorials.com/esp32-http-get-post-arduino/) 

## 蓝牙(优势)

*   你可以在[亚马逊](https://www.amazon.in/RG-Retail-Bluetooth-Module-Arduino/dp/B071LHLLDC)或[易贝](https://www.ebay.com/c/1964725420)上以非常便宜的价格购买 Arduino 的蓝牙模块，并将它们连接到你的智能手机、PC 或智能手表上。
*   蓝牙比大多数无线通信技术消耗更少的能量( [2.5 mW](https://docplayer.net/45928637-An-introduction-to-bluetooth-modules-hc-05-hc-06.html) )。
*   它不太容易受到数据的干扰，而其他无线方法可能会受到数据的干扰。最适合短距离无线通信。

## 蓝牙(缺点)

*   蓝牙是一种短距离通信技术，有时甚至在室外也无法正常工作。
*   它不允许互联网连接。这违背了真正的随时随地通信的目的。
*   如果你有一群讨厌的朋友，这是最糟糕的选择，因为它容易被拦截和攻击。

[](https://create.arduino.cc/projecthub/lightthedreams/control-led-rgb-ws2812b-using-bluetooth-and-android-76b0ec) [## 使用蓝牙和 Android 控制 LED RGB WS2812B

### 短教程如何通过蓝牙通信用 Android 应用控制 LED RGB WS2812B？这个项目是…

create.arduino.cc](https://create.arduino.cc/projecthub/lightthedreams/control-led-rgb-ws2812b-using-bluetooth-and-android-76b0ec) 

## 在线服务

你也可以使用诸如 [Blynk](https://blynk.io/) 这样的服务，它有一个用于智能手机的移动应用程序和一个用于 Arduino 建立通信的库。

## 在线服务(优势)

*   在线服务通常不需要太多的编码和艰苦的工作。一切都为你准备好了。你只需要把这些点联系起来。
*   付费服务将定期更新，并随着时间的推移带来更多的功能。
*   允许您随时随地控制设备。

## 在线服务(缺点)

*   这些服务通常不是免费的，或者有限制或摩擦。
*   该服务可能没有您需要的某些功能。
*   你受到第三方服务的支配，如果你在寻找商业应用，这可能并不好。

## WebSocket(优势)

*   WebSocket 是一个有状态的协议(即，在其中一个设备断开连接之前，连接是打开的)。与 HTTP 不同，web 套接字不需要在每次想要发送数据时建立新的连接。
*   它是实时应用的最佳选择，例如游戏和交易，在这些应用中有连续的数据流。

一个很好的类比是一个电话:在一方断开连接之前，双方都可以通话和交流。

## WebSocket(缺点)

*   与 HTTP 协议相比，它的计算开销很大，因此仅在需要实时连接时使用。

[](https://randomnerdtutorials.com/esp8266-nodemcu-websocket-server-arduino/) [## ESP8266 NodeMCU WebSocket 服务器:控制输出(Arduino IDE) |随机书呆子教程

### 在本教程中，您将学习如何使用 WebSocket 通信协议构建带有 ESP8266 的 web 服务器。作为一个…

randomnerdtutorials.com](https://randomnerdtutorials.com/esp8266-nodemcu-websocket-server-arduino/) 

# 结论

要使用这些技术，您可以创建一个移动应用程序，或者使用 [ESP8266 作为 web 服务器](http://randomnerdtutorials.com)(用于 HTTP 和 WebSockets)。

如果你需要帮助，请告诉我。