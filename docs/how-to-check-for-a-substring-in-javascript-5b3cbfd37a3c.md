# 如何在 JavaScript 中检查子字符串

> 原文：<https://betterprogramming.pub/how-to-check-for-a-substring-in-javascript-5b3cbfd37a3c>

## 学会使用。indexOf()和。包括()

![](img/0390cbb1c605dc915d17fa434a11e96c.png)

照片由[杰米街](https://unsplash.com/@jamie452?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/android?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

检查子串似乎是一项相当简单的任务…

然而，与许多编程工作一样，有一些容易被忽略的考虑因素以及解决同一问题的多种方法。

在 JavaScript 中，有两个内置函数——`.indexOf()`和`.includes()`——可以完成手头的任务。

# 的。indexOf()方法

这种方法是搜索子字符串最普遍可用的(因此也是最可靠的)技术。

使用`indexOf()`将返回子串开始的索引。当索引从`0`开始时，如果没有找到子串，该方法将返回`-1`。

我们可以创建一个非常简单的函数来获取这个返回值，并将其解释为一个`true`或`false`来明确地回答这个问题:“子串存在吗？”

```
function foundSubstring(haystack,needle) {
   if(haystack.indexOf(needle) >= 0) {
      return true;
   } else {
      return false;
}var text = "Hello World";console.log(foundSubstring(text,"llo")) // true
```

# 的。includes()方法

这个方法更直接地回答了这个问题，因为它返回一个`true`或`false`。结果是实现读起来更清楚。

```
let text = "Hello World";console.log(text.includes("llo")); // true
```

使用这种方法的挑战是它没有得到普遍支持。在 ECMAScript6 中发布的`.includes()`已经被广泛使用，但并不普遍。