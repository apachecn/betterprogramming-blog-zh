# 看一看颤动的冷冻包装

> 原文：<https://betterprogramming.pub/a-look-at-the-flutter-freezed-package-5ea5473c6abc>

## 颤动冻结的基调

![](img/db212c9c24af854a90e6ff567abcb142.png)

克里斯托弗·高尔在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在这篇文章中(或者我应该说我的关键笔记)，我将展示如何使用我最喜欢的一个 Flutter 包: [Freezed](https://github.com/rrousselGit/freezed) 。

Freezed 是一个健壮的、可伸缩的数据类代码生成器。它很可靠，但也有一个缺点:对于初学者来说，很难找到合适的混合和必要的功能来用于他们的应用程序逻辑。它的[文档](https://github.com/rrousselGit/freezed)非常精彩，详尽地解释了所有的特性，所以可以把这篇文章看作是偶尔参考的一些速记笔记。

因此，我将强调冻结优惠的关键必要功能，并解释您为什么需要它们。

在继续之前，请查看关于如何安装和使用软件包的[冻结文档](https://github.com/rrousselGit/freezed#install)。

# 禁用 invalid_annotation_target 警告和生成文件中的警告。

对于那些结合使用`json_serializable`和`freezed`的人来说，他们的共存有一个已知的问题，dart 分析器在一个无效的注释目标上抛出警告。对于那些认为警告是错误的人来说，下面是解决这个问题的方法。

请查看本[期](https://github.com/rrousselGit/freezed/issues/488)以了解关于该问题的更多见解和更新。

# 默认值

当变量在类形成期间没有被传递时，它允许有一个占位符。当缺少预期值时，将此值视为后备值。

```
@Default(“John Doe“) String name
```

# JsonKey

这在从后端反序列化 JSON 数据时很方便。它用于引用保存变量数据的键。仅当变量名不同于密钥时才需要，如下所示。

```
@JsonKey(name: “first_name”) String firstName
```

# 托吉森

**冻结后的**会自动要求`[**json_serializable**](https://pub.dev/packages/json_serializable)` 生成[文档](https://github.com/rrousselGit/freezed#fromjsontojson)中所述的所有必要的`fromJson` / `toJson`。

但是，为了序列化冻结对象的嵌套列表，您应该:

在类定义前指定一个`@JsonSerializable(explicitToJson: true)`。

或者在你的`build.yaml`文件中修改`explicit_to_json`(在项目的根文件夹中创建)。

# 自定义 JSON 转换器

不要像我第一天那样害怕这个。你是否厌倦了要求你的后端工程师在进行 API 调用时改变他们返回的变量类型？除非你是后端工程师(这会让你付出代价)。

于是就有了`JsonConverters`的概念。在类序列化/反序列化之前，它们将 JSON 值转换成所需的变量类型。

> NB:在 Dart int 和 int？，弦与弦？etc 是不同的变量类型。

下面的例子将一个字符串分别转换为`int`和`int?`。

之后，在您的类中使用自定义转换器，如下所示。

```
@IntConverter() int id,@NullableIntConverter() int? age,
```

# 我们类中的方法和 Getters

每当你定义一个方法或 getter 时，我们都需要添加一个空的私有构造函数来让它工作。

否则，您可能会遇到这样的错误:非抽象类`_$_Person`缺少这些成员的实现:

# 可变类

到目前为止，您应该知道 Flutter 主要围绕两个概念:

*   不可变:不能改变，只能替换。
*   可变的:可以改变

Freezed 通过使用`@freezed`注释支持不变性，通过使用`@unfreezed`注释支持可变性。

[这里的](https://github.com/rrousselGit/freezed#defining-a-mutable-class-instead-of-an-immutable-one)是对[概念](https://github.com/rrousselGit/freezed#defining-a-mutable-class-instead-of-an-immutable-one)的深入解释。

那是一个包裹。

我会不断更新这篇文章，介绍 Freezed 提供的很酷的特性。尽管如此，请查看[文档](https://github.com/rrousselGit/freezed)，该文档全面涵盖了封装的所有方面。

感谢阅读。