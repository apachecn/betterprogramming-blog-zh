# JavaScript 中的编译与多填充

> 原文：<https://betterprogramming.pub/compiling-vs-polyfilling-in-javascript-6bbc5707a253>

## 区别是什么，为什么重要？

![](img/c889a30624b35689f401a7c181890813.png)

斯科特·韦伯在 Unsplash[拍摄的照片](https://unsplash.com?utm_source=medium&utm_medium=referral)

> “巴别塔的目的是让你的代码使用浏览器可能还不支持的新功能，并把它转换成你所关心的任何浏览器都能理解的代码。”—[编译 vs Polyfills](https://tylermcginnis.com/compiling-polyfills/) 中的[数组#findIndex](https://medium.com/u/c52389e3ee63#find</li><li id=)
> 
> *   [函数#名称](https://medium.com/u/c52389e3ee63#find</li><li id=)*   [地图](https://medium.com/u/c52389e3ee63#find</li><li id=)*   [号码.伊斯南](https://medium.com/u/c52389e3ee63#find</li><li id=)*   [对象.分配](https://medium.com/u/c52389e3ee63#find</li><li id=)*   [对象.条目](https://medium.com/u/c52389e3ee63#find</li><li id=)*   [对象.值](https://medium.com/u/c52389e3ee63#find</li><li id=)*   [承诺](https://medium.com/u/c52389e3ee63#find</li><li id=)*   [一组](https://medium.com/u/c52389e3ee63#find</li><li id=)*   [字符串#包含](https://medium.com/u/c52389e3ee63#find</li><li id=)*   [标志](https://medium.com/u/c52389e3ee63#find</li><li id=)*   [WeakMap](https://medium.com/u/c52389e3ee63#find</li><li id=)*   [WeakSet](https://medium.com/u/c52389e3ee63#find</li><li id=)
> 
> [*来源*:了不起的](https://medium.com/u/c52389e3ee63#find</li><li id=)[泰勒·麦金尼斯](https://medium.com/u/c52389e3ee63?source=post_page-----6bbc5707a253--------------------------------)在他的在线课程[现代 Javascript](https://learn.tylermcginnis.com/courses/51206) 中，具体来说[这个讲座](https://learn.tylermcginnis.com/courses/modern-javascript/lectures/3167447)(可以在他的博客上免费[获得)在 3:54。](https://tylermcginnis.com/compiling-polyfills/)
> 
> ![](img/269c9986448ef19885af14243df5b334.png)
> 
> (塞缪尔·泽勒在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄)

# 太多记不住？

如果在 JavaScript 中需要新的属性或原语，您就会知道有些东西需要 polyfill。

但是，如果变化是简单的语法糖(如箭头函数)，可以使用 Babel 之类的工具进行编译。

当你有疑问时，你总是可以简单地用谷歌搜索你想做的事情和关键字 polyfill，例如[箭头函数 polyfill](https://www.google.com/search?q=arrow+functions+polyfill) 或 [array.from polyfill](https://www.google.com/search?q=array.from+polyfill) 。

![](img/6626496aafb013fd37164a7bfe9aafc3.png)

(Photo by [贝莉儿 NG](https://unsplash.com/@danist07?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral))

# 额外资源

*   [David Gilbertson](https://medium.com/u/f735d3b0f2f3?source=post_page-----6bbc5707a253--------------------------------) [深入探讨了](https://medium.com/hackernoon/polyfills-everything-you-ever-wanted-to-know-or-maybe-a-bit-less-7c8de164e423) polyfills 和 transpiling(编译)。
*   官方[巴别塔文档](https://babeljs.io/docs/en/)对理解编译很有用。
*   [Remy Sharp](https://medium.com/u/f0cddc899d04?source=post_page-----6bbc5707a253--------------------------------) [早在 2010 年就创造了 polyfill](https://remysharp.com/2010/10/08/what-is-a-polyfill) 这个术语，他是这样解释的:

> 把浏览器想象成一堵有裂缝的墙。这些聚合填充物有助于消除裂缝，给我们提供一个光滑的浏览器界面。

![](img/867f6473ad27cd78989d9f5b4e9a9fe4.png)

(Pierre chtel-Innocenti 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片)

[Derek Austin](https://www.linkedin.com/in/derek-austin/)博士是《职业编程:如何在 6 个月内成为一名成功的 6 位数程序员 》一书的作者，该书现已在亚马逊上架。