# 如何安全地国际化您的 React 应用程序

> 原文：<https://betterprogramming.pub/safely-internationalizing-react-ba5ea92ec42d>

## 不再有遗漏的翻译

![](img/a55bbd8ce8580f6da3d213b8a70259c8.png)

照片由[弗拉季斯拉夫·克拉平](https://unsplash.com/@lemonvlad)在 [Unsplash](https://unsplash.com/photos/YeO44yVTl20) 上拍摄。

为了达到尽可能多的`en.users`，应用程序应该国际化。

# 国际化库

有大量的国际化库可以帮助我们完成支持多种语言的繁琐任务。其中之一就是 [i18next](https://www.i18next.com/) 库。它提供了很多特性，非常适合 React 和 React Native([React-18 next](https://github.com/i18next/react-i18next))。

在库的[官方示例](https://github.com/i18next/react-i18next/tree/master/example)中，翻译存储在`.json`文件中:

JSON 中的翻译

有了这样定义的翻译，我们只需要在代码中使用它们。一个调用示例如下:`t('hello')`。对于选择的语言，显示相应的值(例如`Hello world`或`Witaj świecie`)。

然而，上面的例子有一个问题。

# 缺少值

假设我们给`en-translation.json` — `"dog": "dog"`增加了一个新的翻译。由于我们的大多数用户使用应用程序的英语版本，暂时没有人检查波兰语版本。

然后，有一天，有个杆子想用 app。不幸的是，由于缺少`dog`的翻译，他们不能完全享受这个应用程序！

![](img/9f24c40b3acb59cb5c7a8bff96b43057.png)

失踪的 pl-翻译“狗”:“馅饼”(照片由 [Charles Deluvio](https://unsplash.com/@charlesdeluvio) 在 [Unsplash](https://unsplash.com/photos/_p-DIj5LJKc) 上拍摄)

根据 i18next 的配置，将显示一些其他值(例如，缺少值的键或来自后备语言的值)。在这两种情况下，用户体验将远远低于预期。

# 拯救打字稿

多亏了 TypeScript，才可能避免如此糟糕的体验。

![](img/408c975ee4ec06df6d96d96ff820500d.png)

打字稿里的 S 代表超人？(图片由 [Elias Castillo](https://unsplash.com/@eli_j) 在 [Unsplash](https://unsplash.com/photos/7-ToFEHzMNw) 上拍摄)

首先，翻译文件必须保存为`.ts` —而不是`.json`。

然后我们可以用所有需要的键定义一个接口。但是，我觉得是多余的。

假设英语是我们的默认语言选项，我们可以简单地定义所有的英语翻译，然后提取一种类型的`en` -translation 对象。

最后，我们可以使用提取的类型作为`pl` -translation 对象的类型，让 TypeScript 编译器处理任何缺失的翻译！

打字稿中的翻译

感谢阅读！