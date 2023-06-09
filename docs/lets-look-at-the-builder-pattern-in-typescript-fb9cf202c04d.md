# 让我们看看 TypeScript 中的构建器模式

> 原文：<https://betterprogramming.pub/lets-look-at-the-builder-pattern-in-typescript-fb9cf202c04d>

## 提高 TypeScript 接口的可读性

![](img/8aea981cbd06a4a5e6d89936a5e7d076.png)

Alexander Schimmeck 在 [Unsplash](https://unsplash.com/s/photos/builder?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

# 构建器模式简介

TypeScript 现在被用在很多流行的前端框架中(比如 Angular，Vue，React)。在工业环境中，以测试驱动的方式编写干净的代码是非常有益的。

如果你不同意，这篇博文不适合你。对于我和我以前的大多数同事来说，构建器模式是一个必须了解的模式。至少如果你想作为一个程序员被认真对待的话。

读完这篇简短的博文后，你会明白设计模式“构建器”的用途。这是你在未来的面试中可能会遇到的问题之一，所以让我们马上建立(双关语)你的知识。

# 什么是构建器模式，什么是危险信号？

构建器模式是创造性的(例如，构建器、工厂、单例)。这意味着该模式用于创建对象，而不必直接实例化对象。

想象你曾经遇到过以下情况:

*   我正在编写代码，并决定在整个应用程序中使用的接口上添加一个缺失的属性。因为我的接口的所有属性都是强制的，所以我*必须*在它被实例化的任何地方给它们赋值。但是我只需要应用程序中一个特定部分的值。我开始到处都有越来越复杂的对象，如果默认值发生变化，这些对象也可能变得非常脆弱。
*   我向一个接口添加一个值，因为一个用户请求更改。我在测试中创建了许多复杂的对象，它们都需要实现这个值。我有很多失败的测试，我的团队领导不理解增加一个额外的属性会花费开发团队这么多时间。团队期望只需要一个测试就能完成所要求的变更。

这只是你可能遇到的两种情况。但是信号非常清楚。代码变得更难维护，开发成本增加，并且为客户获取价值需要越来越多的时间。

防止这种情况。我不在乎你是前端开发人员、后端开发人员，还是喜欢在业余时间阅读关于构建者的书籍。

是时候结束这一切，应用一个模式了。我保证，这会让你和你同事的生活更轻松。

# 通过实例学习:用户构建器

以下定义准确地告诉您生成器模式如何解决创建复杂对象的问题:

> "构建器模式本身用于将复杂对象的构建与其表示分离开来."— [维基百科](https://en.wikipedia.org/wiki/Builder_pattern)

我将向您展示如何对一个简单的对象这样做。构建器模式为更复杂的对象提供了最大的优势。但是如果我们保持例子的简单性，就更容易解释了。

假设你有一个`User` ，带有一个`name`和一个`age`属性。这个对象很容易实例化，对吗？

user.ts

举个例子，让我们这样做。我们马上创建接口`User`的一个实例:

```
const user: User = {
  name: "John",
  age: 16
};
```

很简单，对吧。“你想说什么，”我听得出你在想什么。让我们假设您想要编写一个代码测试来显示这个用户。

例如，您希望编写一个测试来查看名称的第一个字母是否以大写形式显示。您必须创建一个具有随机年龄值的对象(因为它是强制的)。这还不算太糟，是吗…

但是想象一下我们这里有一个非常简单的物体。假设这个用户有五个其他属性。您必须在您编写的每个测试中为每个属性提供值*。随着时间的推移，情况会变得更糟。*

每个测试都有一个复杂的对象，如下所示，有很多属性:

我们不希望我们的测试变得如此脆弱。因此，我提出一个可能的解决方案。

这个`UserBuilder`类将会创建一个默认的`User`，使用你的应用程序中使用的默认属性。您只需指定一次默认值。如果他们改变了，你马上改变他们。很公平，最小的努力。

如果你有一个额外的属性，你添加一个东西到生成器。`UserBuilder`非常容易维护。

user.builder.ts

# 用 Builder 怎么样？

真正的力量来自于`UserBuilder`的使用。我们现在可以实例化对象并省略掉我们不关心的每个属性。如果我们想为这个名字写一个测试，我们可以写如下:

```
const userWithName: User = new UserBuilder()
  .name("John")
  .build();
```

没有指定年龄，也没有指定其他属性。我们只指定我们需要什么，其他的都不太重要。

现在，我们在一个测试中会写些什么呢？比如，根据用户的`gender`，我们会为特定用户显示不同的广告。

```
const userWithGender: User = new UserBuilder()
  .gender("Female")
  .build();
```

就是这么简单优雅。名字、年龄和其他所有属性都无关紧要(除非你想让它们无关紧要)。

# 结论

通过使用构建器，对象的表示变得非常简单，而对象本身可以根据您的需要变得非常复杂。

在最先进的软件中，构建模式无处不在。我们没有充分利用它。我希望没有人会认为:“嗯，这是一个无用的模式！”

如果你确实这样认为，请展开讨论。我很乐意改变你的想法。