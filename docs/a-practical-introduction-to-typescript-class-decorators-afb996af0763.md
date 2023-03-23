# TypeScript 类装饰器实用介绍

> 原文：<https://betterprogramming.pub/a-practical-introduction-to-typescript-class-decorators-afb996af0763>

## 使用类装饰器的 TypeScript 中着火的类

![](img/93f8260cda025e4187570f64f55ae29d.png)

马库斯·斯皮斯克在 [Unsplash](https://unsplash.com/s/photos/decorate?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

很多时候，有你不知道的特性，所以也许，在你的情况下，“装饰者”就是其中之一。

这是关于 TypeScript Decorators 系列的第一篇文章，我想在这里给你一个关于它们的实用介绍。

什么是室内设计师？有哪些类型？你为什么想要使用装饰器，在什么样的情况下你会使用它？

目前，装饰器是 JavaScript 的第二阶段提案，是 TypeScript 的一个实验性特性。

如果你想了解更多关于这些规范的内容，这些规范非常有趣，而且写得很好，请查看 GitHub repo:[JavaScript Decorators](https://github.com/tc39/proposal-decorators)。

# 什么是室内设计师？

什么是装修工？嗯，想想“装饰”这个词的含义，你就相当接近了。

TypeScript 网站将其描述为:

> "装饰器是一种特殊的声明，可以附加到类声明、方法、访问器、属性或参数上."

我将把它描述为“在现有的类声明、方法、访问器、属性或参数上添加额外功能的特殊声明。”

# JavaScript 类

要使用装饰器，您需要熟悉 JavaScript 类。类知道了超级有用！

如果你不知道什么是 JavaScript 类以及它们是如何工作的，我强烈推荐你看一下来自编码培训的丹尼尔·希夫曼的视频。之后可以更好的关注这个关于装修工的帖子。

如果你正在使用 Angular，很可能你已经在使用像`@Component`、`@NgModule`、`@Input`、`@Output`或者`@Inject`这样的装饰器了。这将有助于更好地理解装饰者。

# 有哪些装修工？

为了描述 Decorators 在 TypeScript 中做什么，我们需要更深入地研究每种类型。有几种不同类型的装修工。

1.  班级装饰者(当前职位)。
2.  [物业装修](https://medium.com/better-programming/an-introduction-to-typescript-property-decorators-1c9db23b6ca1)(下一篇)。
3.  方法装饰器(后面会有)。
4.  Accessor @ Decorator(后面会有)。
5.  参数装饰器(后面会有)。

# 类装饰者

![](img/84261bdff013bfbec2cedb2e4e21c6c1.png)

让我们从班级装饰者开始。为了形象化，我们将构建一个(虚拟的)指环王游戏。

为了玩这个游戏，我们需要很多角色。有些可以是你玩的角色，有些是为了它周围的场景。

这是一个`Character`的类。它有一个`armour`、`height`、`width`、`weight`和`type`。该类型绑定到枚举`CharacterType`。

在 LOTR，你有各种各样的角色。我们将使用类装饰器来创建两种角色类型，霍比特人和精灵。

这两个类都扩展了我们在上面定义的`Character`类，因为精灵和霍比特人都有他们自己的长处、短处和能力。

## **霍比特人类**

## 精灵类

## **霍比特人装饰师**

在《霍比特人装饰者》中，当我们的玩家是霍比特人时，我们会覆盖一些特定的属性。小精灵也一样。

装饰器是一个函数，它返回一个扩展类的新构造函数。在这个构造函数中，我们向应用了装饰器的类添加或覆盖现有的属性/方法。

遗憾的是，我们无法从原始构造函数获得信息来使用这些信息。试了一天才知道！如果你知道另一种方法，请写在评论中，这样我就可以包括它。

当你想要添加装饰器到一个类中时，你定义它为`@hobbitDec`在你想要它被应用的类之上。

当您在浏览器中进行控制台记录时，您应该会看到与来自`PlayerCharacter`的信息相结合的信息。

## **精灵装饰师**

如果我们希望我们的玩家是一个精灵，我们做的和霍比特人一样。

我们在`PlayerCharacter`类上应用`@elfDec`来添加播放器类型信息。

在控制台中记录信息，您会看到应用了正确的信息。

现在我们知道了如何在 TypeScript 中使用类装饰器。如果你想检查类装饰器的全部代码，访问 [TypeScript playground](https://www.typescriptlang.org/play/index.html?experimentalDecorators=true&emitDecoratorMetadata=true#code/GYVwdgxgLglg9mABACzgIzTKARAphAHgBVFcAPKXMAEwGdEBvRMXAdwAoA6bgQwCcA5rQBciHmACeAbQC6ASlEMAvoiUA+dhAS0ofENDh9RROYwBQiS4i1gdKdJiiIAvMzaIAEg6zs5FqwH+ljZ2TKww1FDIADQouDACyFCxrPGJyWJ8ALZwIHyxUBIADrgqrqgYWEEBiNV8uFB5SBAANjy09OSUNPQhuvpQhuY1VuGRyC6IY1HVAchpSZPzCUmzowtOrqkrUGuW-Dl5kwe5fHuIhSWTl7jVSmb3ZqCQsAikLcB4hCRdVHSMbg43E4-CEonE0nkihU6k02n6BiMiBMwysfXewEmLFYiAAoh9fOdquiwhEorFlukUhtYic8gViqVJrgPkSAvVGnxmm0OqQKH9evC9IjUSNphMtmTkOdKYtyhtztt0pMlasRvtsqdjpq8ucbtdGXcHmYzFQQFlEABhZD8HjQXB8IiM0WIAByAHlXbjJgByMAIXA+6LVDzugBCYYAkiRXD6Ko4g9VcQAZABivpZwETj1a7Xo1tt9r4LqKIDQLRgEEyhyRYHNaAdkwAjAAGFsAbmqpfLlbiO1EdayDeLrhbXbLFar4oH9cbo-HPanGxnQ7niDHAW7k4ujNEBb4dsojudrn3h4dTpKnA9XuNZlzvK8lScvx6VptB6LJYnvdlUBXw7Ngu27Tsws4jogABMwG9qq-5gauEEACwblYW69jce4fuex5XKe2FFpeuCcKGEbRiaAR9MKgx8L4LoBLQIAlLRfgBPcOY8vQ+KYq+-xnl+DAwVWdK1uBzZth2Ql9ukAFrq2qGWOhU5SrJEGQQpiBKVMy4IYBrgAJwaVpmHvoWR5EZM-HmYynApqmFFokKAyGHRgnqogjHMYSbF3gA9L5iAAAptBIjYQARR5mAAAvGWBfPenFBSFDpWY2bloT+VZgDwWS4KIOh8DAYACL6wU8KFxbZblPqdjm8KaclEHYkl5UpRFDq+J297aHALTES0cACOwPpFI1ohBg1rV8HIQA) ，在那里你也可以看到编译后的 JavaScript 是什么样子。

# 谢谢！

![](img/2bd571bb1ce9f466ae279fa98111b086.png)

读完这个故事后，我希望你学到了一些新的东西，或者受到启发去创造一些新的东西！🤗

如果我给你留下了问题或一些要说的话作为回应，向下滚动并给我键入一条消息。如果你想保密，请在 Twitter @DevByRayRay 上给我发一条 [DM。我的 DM 永远是开放的😁](https://twitter.com/@devbyrayray)

[**通过电子邮件获取我的文章点击这里**](https://byrayray.medium.com/subscribe) **|** [**购买 5 美元中等会员**](https://byrayray.medium.com/membership)

# 阅读更多

![RayRay](img/992af170033696163d6cc0269218aedd.png)

[雷雷](https://byrayray.medium.com/?source=post_page-----afb996af0763--------------------------------)

## 荒诞的故事

[View list](https://byrayray.medium.com/list/angular-stories-24674407532a?source=post_page-----afb996af0763--------------------------------)6 stories![](img/b94f2b7d2929c90566cd2dd6f657a751.png)![](img/02b73423a62d73b113af9fdf9629c79f.png)![Stacked books](img/b02a2f57c0093e04ab1d11d3a55f35ea.png)![RayRay](img/992af170033696163d6cc0269218aedd.png)

[雷雷](https://byrayray.medium.com/?source=post_page-----afb996af0763--------------------------------)

## 最新的 JavaScript 和 TypeScript 故事

[View list](https://byrayray.medium.com/list/latest-javascript-typescript-stories-0358ad941491?source=post_page-----afb996af0763--------------------------------)14 stories![](img/c93ca03b33796c40dcc47873de2697c2.png)![](img/86f37efa11855f6f0f0f62984c37f696.png)![](img/ddbaa6d0bea676316247e82043d60b63.png)