# React 中轻量级和类型安全的本地化

> 原文：<https://betterprogramming.pub/lightweight-and-type-safe-localisation-in-react-493b2609c282>

## 使用 React 上下文 API 和钩子

![](img/05f0446fa5e8f71a181c8c8c617cb567.png)

图片由 [Dmitry Ratushny](https://unsplash.com/@ratushny) 从 [unsplash](https://unsplash.com/photos/wpi3sDUrSEk) 拍摄

本地化(或多语言支持)是几乎所有生产就绪应用的核心部分。大多数情况下，保持所有翻译最新是一项耗时的任务，如果您需要在编写代码时保持翻译最新，甚至可能会停止您的编码流程。

在我的经验中，我经常在引用一个丢失的语言键时遇到语言文件中的错别字和未翻译的字符串。我观察到混合键或添加重复键太容易了。

因此，我为 React 实现了一个轻量级、类型安全的本地化。它使用 React 上下文 API 和钩子来简化功能组件内部的使用，并使用 Typescript 强制转换和键查找是类型安全的。如果您感兴趣，让我们深入研究一下实现吧！

# 在上下文中实现本地化

首先，让我们看看用户选择的当前地区的实际商店。我决定将这些信息保存在 React 上下文中，因为它应该可以在整个应用程序中访问，并且在当前语言环境中预期的更改较少，所以不用担心性能问题。

所选的语言环境保存在 Localstorage 中，并在上下文首次装载时加载，以保存用户的决定。如果无法从 Localstorage 中加载任何语言环境，则使用默认语言环境(在我的例子中主要是英语)。在 Localstorage 中，只存储`localeId` (如 en_US)而不是整个语言环境。

上下文文件导出一个`Provider` ,我们将它放在整个示例应用程序中。在这个上下文中，我提供了可用的语言环境、当前语言环境和一个函数，用于设置要在组件中访问的当前语言环境。

为了将`LocaleContextProvider`注入到 React 组件树中的任何位置，`props.children`被简单地设置为 React 子组件，因此被传递。整个上下文显示在以下要点中:

这就是上下文方面，现在让我们切换到语言资源。

# 语言文件

作为语言文件，我们使用`simple.ts`文件，它导出一个包含查找关键字及其翻译的 JS 对象。对于每个语言环境，都会添加一个导出特定翻译对象的文件。对于输入性能，我体验到如果所有的 lang 键都以一个前缀开始，例如 lang，那么自动补全会显著提高。这对我来说效果很好，很容易区分翻译和其他变量。

## 添加类型安全

因为我将 JS 对象用于我们的翻译映射，所以实现可以受益于通过 Typescript 启用类型检查。为了避免创建包含完成翻译所需的所有键的专用类型(相信我，我这样做了，这很烦人……)，我使用`typeof`操作符来获取我们默认的英语翻译的类型，并强制所有其他翻译具有相同的类型。

在这种情况下，这意味着默认翻译的所有键都需要在另一个翻译中可用。因此，这会导致正确的语法突出显示，并为丢失或错误的翻译键生成错误。

## 在应用中的使用

实现翻译和本地化后，最后一步是在我们的应用程序中包含翻译。因此，我定义了一些有用的钩子，可以用在每个组件中(在`LocaleContext`的组件树内部)。以下钩子在我的默认实现中可用，并且可以很容易地扩展:

*   `useLanguage`:返回当前地区的翻译对象
*   `useLocales`:返回可用的语言环境
*   `useSetLocale`:返回一个设置新语言环境的函数

在下面的例子中，描述了所有这三个钩子的用法。通过一个按钮点击，应用程序遍历所有可用的语言环境，并相应地更改当前的语言环境。翻译可以在任何 jsx 或其他函数逻辑中用作 string 类型的普通变量。

这里是使用 Typescript 的主要线索:您不能在语言键中做任何打字错误，因为翻译是键入的，只允许访问现有的键，并且所有的翻译都需要完整，所以没有半生不熟的翻译可用。

# 摘要

在这个故事中，我想分享我用 React 上下文和钩子实现的轻量级、类型安全的本地化。该实现允许轻松使用翻译后的字符串，强制完成翻译(否则 typescript 会责怪您)，并消除了使用翻译时输入错误的可能性。即使翻译的设置看起来相对复杂，但它的好处对我来说相当不错。

整个代码可在 [stackblitz](https://stackblitz.com/edit/react-starter-typescript-b7m7ct?file=App.tsx) 上获得。

我希望您喜欢我在
React 中对本地化方法的小小探索。

请随时分享任何反馈，我会很感激，并相应地更新故事。