# 如何用 JavaScript 精确检测网站访问者的设备型号

> 原文：<https://betterprogramming.pub/how-to-precisely-detect-the-device-model-of-your-websites-visitors-with-javascript-959ab031c4d7>

## **使用设备检测 API 个性化他们的体验**

![](img/407b9301340dfbcb60663e53860cc8f2.png)

2020 年，我们生活在一个比以往任何时候都更加个性化的世界。为了使网页符合用户的个人需求，需要关于访问者的信息。作为一名数据科学家和网站开发人员，我很难收集网站访问者的数据。其中一个难题是确定设备类型。但是现在有了一点帮助。

让我们以销售手机壳的个性化网店为例。想象一下，网上商店可以识别用户拥有的移动设备，然后只显示与该特定设备相关的案例！那不是很棒吗？

嗯，有了这个很酷的[设备检测 API](https://theapicompany.com/) ，未来比你想象的更近了！我将向您展示这个 API 的特性以及如何实现它。

# 设备检测 API 功能

到目前为止，许多 JavaScript 设备检测 API 都不能像这个一样检测设备(例如 iPhone 11 Pro)。一旦实现，API 将检测并访问以下特性:

*   **device API . device type**//例如手机
*   **设备 API .设备品牌** //例如苹果
*   **device API . device name**//例如 iPhone XS
*   **device API . screen width**//例如 1600
*   **device API . screen height**//例如 800
*   **device API . screen ratio**//例如 2
*   **设备 API。GPU** //例如苹果 GPU
*   **device API . browser name**//例如 Safari
*   **设备应用编程接口.浏览器版本** //例如 13.0.1
*   **deviceAPI.osName** //例如 iOS
*   **deviceAPI.osVersion** //例如 13.1
*   **设备 API .触摸屏** //例如 TRUE

# **同步实现**

一会儿我们将看看如何异步实现 API*。但是首先我将解释 API 如何同步实现*:**

*   *在处获取[上试用版的唯一代码。](https://theapicompany.com)*
*   *将`<script src=”https://theapicompany.com/deviceAPI.js?id=deviceAPI-123456"></script>`放在网站的头部标签中*
*   *用您的唯一 id 替换该 id*
*   *如下所示，从`deviceAPI`变量访问特征*

*然后，个性化您的网页:*

# ***异步实现***

*为了加快网站的加载速度，还有一个异步版本。要实现这一点，您应该执行以下操作:*

*   *在此获取试用版[的唯一代码。](https://theapicompany.com)*
*   *将`<script async src=”https://theapicompany.com/deviceAPI2.js?id=deviceAPI-123456"></script>`放在你网站的头部标签中。*
*   *用您的唯一 ID 替换该 ID。*
*   *从`deviceAPI`变量访问特征，如下所示。*

*很简单，对吧？*

*现在，当一个拥有 iPhone XS 的用户进入你的网站时，他们会被重定向到有 iPhone XS 手机套的页面。*

# ***结论***

*个性化和 CXO(客户体验优化)是目前 web 开发中最热门的话题之一。有了这个设备检测 API，你可以用一行代码来个性化你的网站。所以继续吧，快乐编码！*