# 4 个你可能还没用过的很棒的浏览器 API

> 原文：<https://betterprogramming.pub/4-awesome-browser-apis-you-might-not-be-using-yet-726d3e3237d2>

把它们放在手边。它们可以超级有用！

![](img/a52af85fd556505a20095cddbcb7d5f9.png)

照片由[克里斯蒂安·卡斯蒂略](https://unsplash.com/@castillcc?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

浏览器 API 是包含在你的网络浏览器中的 HTML 的非常重要的特性。这些 Web APIs 比您想象的要强大得多。有一些伟大的 API 被低估了！

这是每个有经验的 web 开发人员都应该很好掌握的话题，了解他们会带来很多好处。

虽然有一些非常著名的 API，如 [Web 存储 API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Storage_API) 或 [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) ，但也有一些被大大低估，在普通教程中很少提及！

现在就让我们来看看它们吧！

# 1.电池 API

[电池状态 API](https://developer.mozilla.org/en-US/docs/Web/API/Battery_Status_API) ，有时也称为[电池 API](https://developer.mozilla.org/en-US/docs/Web/API/Battery_Status_API) ，提供关于计算机电池的信息。它显示电池的剩余容量和充电状态。它还可以估计电池完全充电或放电需要多长时间，具体取决于电池是在充电还是在放电。

[电池状态 API](https://developer.mozilla.org/en-US/docs/Web/API/Battery_Status_API) 不是第三方应用。可以在`navigator`中找到。导航器对象的内置`window.navigator`属性有一个`getBattery()`方法。

这个 API 快速查询用户的电池状态，并在用户电池电量低时使用它来实现电池节省功能，如杀死一些更快耗尽电池的后台进程。

例如:

```
navigator.getBattery().then((battery) => {
    console.log(battery);
});
```

这是上面代码的示例响应。

```
{
    charging: false,
    chargingTime: Infinity,
    dischargingTime: 35040,
    level: 0.53,
    onchargingchange: null,
    onchargingtimechange: null,
    ondischargingtimechange: null,
    onlevelchange: null
}
```

# 2.IndexedDB API

在 HTML5 规范中， [Web 存储 API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Storage_API) 被弃用，由 [IndexedDB API](https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API) 取而代之。(然而，网络存储仍然受到许多流行浏览器的支持，包括苹果的 Safari 和 Opera。)索引、事务和复杂的查询功能只是 IndexedDB 优于 Web 存储的几个优点。

IndexedDB 允许您在本地存储大量对象，并使用复杂的数据访问协议检索它们。

每当我们需要在本地为用户存储数据时，我们可以使用 [IndexedDB API](https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API) ，这对于本地存储来说太复杂了。

我们甚至可以通过仅使用 IndexDB 在本地存储数据来创建完整的 CRUD 实现，这也是我们在为 PWA 存储复杂数据时可以使用的东西。

这是我们访问 IndexDB 的方式:

```
let indexedDB = window.indexedDB;
```

要了解更多关于这个 API 的信息，请[访问这里](https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API)。

# 3.剪贴板 API

想知道流行网站如何通过点击按钮将文本复制到你的剪贴板吗？他们正在使用[剪贴板 API](https://developer.mozilla.org/en-US/docs/Web/API/Clipboard_API) 。

[剪贴板 API](https://developer.mozilla.org/en-US/docs/Web/API/Clipboard_API) 允许您异步读取和写入系统剪贴板，以及响应剪贴板操作(剪切、复制和粘贴)。

我们可以使用这个 API 执行以下操作。

```
// Copy an array of ClipboardItem objects to the clipboard
navigator.clipboard.write();// Writes a text string to the user's clipboard
navigator.clipboard.writeText();// Read the clipboard as an array of ClipboardItem(s)
navigator.clipboard.read();            // -> returns a promise// Read the current clipboard content in text format
navigator.clipboard.readText();       // -> returns string
```

# 4.页面可见性 API

当使用浏览器标签浏览网页时，有可能无法同时访问部分(或全部)标签。[页面可见性 API](https://developer.mozilla.org/en-US/docs/Web/API/Page_Visibility_API) 是一个浏览器 API，允许您查看哪些浏览器选项卡是活动的，哪些是不活动的。

我们可以使用[页面可见性 API](https://developer.mozilla.org/en-US/docs/Web/API/Page_Visibility_API) 来检测用户何时离开或进入你的网页。我们可以使用它来运行某些操作，如当用户返回页面时更新一些数据，或者检测像测验应用程序这样的应用程序的页面。

我们可以使用一个特殊的事件侦听器来检测页面可见性状态的变化。

下面是一个使用页面可见性检测的示例:

```
const handlePageChange = () => {
    document.hidden
        ? console.log(`User left the page at ${new Date()}`)
        : console.log(`User opened the page at ${new Date()}`)
}document.addEventListener("visibilitychange", handlePageChange);
```

其他非常酷的 API 你应该看看，如果你还没有的话，是地理位置 API，网络动画 API，[全屏 API](https://javascript.plainenglish.io/fullscreen-api-a-complete-guide-9be8614cbd39?sk=15672020a56602ffeea6905cd3a05cd2) ，还有画布 API。我们也可以用这些做很多事情。

```
**Want to Connect?**Connect with me on [LinkedIn](https://www.linkedin.com/in/gouravkajal/).
```

感谢阅读！