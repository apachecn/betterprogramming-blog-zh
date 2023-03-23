# 用 Next.js 构建自己的 useTranslation 挂钩

> 原文：<https://betterprogramming.pub/build-your-own-usetranslation-hook-with-next-js-2c65017d323a>

## 这就是我如何建立我的网站

![](img/c133af678247c136eba1e806ab590903.png)

如果你曾经做过 React 应用程序的本地化工作，那么你可能知道 [react-18next](https://react.i18next.com/) 这是解决这类问题的标准方法。

另一方面，对于 Next.js，我们可以使用具有类似概念的 [next-translate](https://github.com/vinissimus/next-translate) 。我在建立自己的网站时发现了这个库，因为我想支持意大利语和英语，所以我必须了解这个库是如何工作的，但是在阅读文档时，我认为像我这样的小网站使用这样一个复杂的库是多余的。

为什么不开发我自己的`useTranslation`钩子，只使用一堆 JSON 文件来存储翻译呢？我就是这样建立我的网站的。

让我解释一下我是怎么做的，这比你想象的要简单。

## 在 Next.js 中添加本地化

首先，我们需要添加我们想要支持的语言环境。在本例中，我将为意大利语和英语添加国际化，并将英语设置为用户访问网站时的默认语言。

这是预期的行为:

*   英语翻译
*   `mywebsite.com/en` = >英文翻译
*   `mywebsite.com/it` = >意大利语翻译

在 Next.js 中，我们有一个`next.config.js`文件，应该是这样的:

现在，有了`it`和`en`地区，它就变成了

## 准备翻译

下一步，让我们为要在网站上展示的文本写翻译。

在路径`public/static/locales`中创建两个文件:`it.js`和`en.js`

```
pages/
public/
├─ static/
│  ├─ locales/
│  │  ├─ it.js
│  │  ├─ en.js
next.config.js/
....
```

正如您所猜测的，`it.js`文件将包含所有意大利语翻译和`en.js`英语翻译。

当然，如果您只有文本字符串，您可以使用 JSON 文件而不是 Javascript 来存储翻译。在我的例子中，我使用 Javascript 文件来更好地控制数据，您将在最后一节中读到更多相关内容。

## 创建 useTranslation 挂钩

现在是时候创建我们的自定义 React 钩子来使用这些翻译了。在项目和文件`useTranslation.jsx`的根目录下创建`hooks`文件夹

仅此而已，这是本教程中你将看到的最难的部分！如果你已经用过 React 钩子，你就已经知道是怎么回事了，否则，让我来解释一下。

如您所见，我们正在导入意大利语和英语翻译文件，并将它们存储到`TRANSLATIONS`常量中。

```
import en from "../public/static/locales/en";
import it from "../public/static/locales/it";

const TRANSLATIONS = { en, it };
```

我们需要检测用户的当前地区，为此我们使用从`next/router`导入的 Next.js 路由器。`locale`常量包含当前的区域设置，可以是`it`或`en`。

```
const router = useRouter();
const { locale, asPath } = router;
```

函数`setLocale`只是通过使用 Next.js 路由器改变 URL 的区域设置，这意味着`setLocale("it")`将用户导航到`mywebsite.com/it`并将所有网站切换到意大利语。

```
const setLocale = (locale) => router.push(asPath, asPath, { locale });
```

现在这个`useTranslation`钩子的核心思想是`t`函数:它接收一个键，并使用该键和当前的区域设置访问`TRANSLATIONS`对象。

```
const t = (keyString) => TRANSLATIONS[locale][keyString];
```

因此，如果当前区域设置是`en`并且我们传递了键`WELCOME_MSG`，它将访问存储在`TRANSLATIONS[en][WELCOME_MSG]`中的值，该值等于`"Welcome to my website!"`

## 如何使用钩子

最后，要显示`WELCOME_MSG`文本，我们可以写:

现在，如果用户更改了网站的本地化，此文本将自动刷新以显示正确的字符串。

## 最佳化

还有一些优化的空间，例如我们可以使用 React `useCallback` hook 来包装内部的 useTranslation 函数，这样如果没有任何变化，它们就不会重新呈现。

## 高级用例

正如我已经提到的，我选择使用 Javascript 文件来存储所有的翻译，以便对数据有更多的控制。你可能在想“为什么？只是文字！”

嗯，在我的情况下，我需要根据当前用户的本地化显示不同的组件，例如意大利语翻译应该是红色，而英语翻译应该是绿色。这种行为很容易获得，只需将 React 组件代码写入本地化文件，如下所示:

然后我们可以这样使用`t`:

伙计们，我希望这是一个有趣的教程，我想帮助新手，让他们知道即使像国际化这样“晦涩”的东西也可以变得容易，不需要复杂的库。

最后，如果你想看看这个代码的真实例子，你可以看看 GitHub 库，里面有我的个人网站 [achris.me](https://github.com/a-chris/achris.me) 的代码，我一直在使用我自己的`useTranslation`钩子。

本教程的代码可以在我的存储库中找到:

[](https://github.com/a-chris/use-translation-example) [## GitHub-a-Chris/use-translation-example:如何在 Next.js 中构建自己的 useTranslation 钩子…

### 这是一个用 create-next-app 引导的 Next.js 项目。这个项目已经建成的一个例子，而我是…

github.com](https://github.com/a-chris/use-translation-example)