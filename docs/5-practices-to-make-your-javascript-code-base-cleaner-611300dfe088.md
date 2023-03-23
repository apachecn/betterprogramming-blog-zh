# 让您的 JavaScript 代码库更整洁的 5 个实践

> 原文：<https://betterprogramming.pub/5-practices-to-make-your-javascript-code-base-cleaner-611300dfe088>

## 在你的下一个 JS 项目中尝试的事情

![](img/b1d3033393fddf1a2491fcddd7cd75e3.png)

[格雷格·拉科齐](https://unsplash.com/@grakozy?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/javascript?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍照。

JavaScript 无处不在，从浏览器到移动甚至后端。

在本文中，我将尝试记录一些实践，您可以遵循这些实践来使您的代码看起来更整洁。

# 1.使用默认参数，而不是短路或条件

默认参数通常比短路更简洁。

请尝试以下方法:

*免责声明:Falsy 值如* `*''*` *、* `*""*` *、* `*false*` *、* `*null*` *、* `*0*` *、* `*NaN*` *不会被替换为默认值:*

# 2.处理多种情况

更干净的方法是:

# 3.用动态键值对替换开关(即 O **对象文字)**

开关版本(或者用 if/else 替换开关) :

动态键值对版本:

# 4.避免过多的函数参数

这里有一个更干净的方法:

# 5.使用 Object.assign 设置默认对象

这看起来既糟糕又乏味:

这种方法看起来好得多:

感谢阅读。干杯！