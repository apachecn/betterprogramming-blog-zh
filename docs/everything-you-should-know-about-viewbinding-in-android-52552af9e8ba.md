# 关于 Android 中的 ViewBinding 你应该知道的一切

> 原文：<https://betterprogramming.pub/everything-you-should-know-about-viewbinding-in-android-52552af9e8ba>

## 没有 findViewById，是时候升级了

![](img/977d056f4e19c45e1cb9aff0da687e8c.png)

Benjamin Lizardo 在 [Unsplash](https://unsplash.com/s/photos/maths?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

# 介绍

在现有环境中，每当我们创建一个新的屏幕时，我们都必须使用`findViewById()`手动将布局中的视图与 Java/Kotlin 文件链接起来。这很耗时，容易出错，并且意味着我们使用了更多的样板代码。

如果我们更深入地研究这个过程，我们可以找到一个可以自动化的模式。例如，我们所做的是在 Java/Kotlin 文件中声明一个视图，并用来自膨胀布局的适当视图`id`初始化该视图(将布局中带有`id`的每个视图引用到 Java/Kotlin 文件)。

Android 团队的`ViewBinding` 背后的想法是为每个布局创建一个绑定类，保存布局中视图的引用。这减少了组件(activity 和 fragment)中的样板代码，我们可以通过为该类创建一个实例来访问视图，您将很快了解到这一点。

*视图绑定取代了* `*findViewById*` *。*

# 综合

***注:*** *视图绑定在* [*Android Studio 3.6 金丝雀 11+*](https://developer.android.com/studio/preview) *中可用。*

可以在模块级别启用视图绑定。因此，您的项目中有多个模块，您必须为每个模块启用它。要在模块中启用视图绑定，您需要在模块级`build.gradle`文件中的`android`标签下添加以下几行:

```
viewBinding {
    enabled = true
}
```

# 使用

启用视图绑定后，每当我们创建新布局时，都会生成一个引用布局中视图的绑定文件。布局的根在绑定文件中用名为`root` 的视图表示。我们可以使用`setContentView` 中的`root`来膨胀 UI 组件中的布局。

让我们创建一个布局文件，并用视图绑定来扩展它。看一下布局文件:

现在让我们创建一个布局的绑定实例:

```
private lateinit var activityMainBinding: **ActivityMainBinding**
```

# 查看活动中的绑定

要在活动中使用绑定类的实例，请在活动的`[onCreate()](https://developer.android.com/reference/kotlin/android/app/Activity#oncreate)`方法中执行以下步骤:

1.  调用生成的绑定类中的静态`inflate()`方法。这创建了一个我们可以使用的绑定类的实例。
2.  如上所述，通过调用`getRoot()`函数或者使用 [Kotlin 属性语法](https://kotlinlang.org/docs/reference/properties.html#declaring-properties)来获得对根视图的引用。
3.  将根视图传递给`[setContentView()](https://developer.android.com/reference/kotlin/android/app/Activity#setcontentview_1)`方法，以在屏幕上呈现布局。

看一看:

# 在片段中查看绑定

要在片段中使用绑定类的实例，请在片段的`onCreateView()`方法中执行以下步骤:

1.  调用生成的绑定类中的静态`inflate()`方法。这将创建一个在片段中使用的绑定类的实例。
2.  通过调用`getRoot()`方法或者使用 [Kotlin 属性语法](https://kotlinlang.org/docs/reference/properties.html#declaring-properties)获得对根视图的引用。
3.  从`onCreateView()`方法返回根视图，在屏幕上呈现布局。

现在，您可以使用此实例来引用布局中的视图并执行操作:

```
binding.greetings.text = viewModel.msg
```

# 优势

## 零安全

有了视图绑定，在对视图执行操作时，不再需要对视图进行空检查。作为直接从布局文件生成的绑定类，它只包含布局内的视图。

## 类型安全

类型安全是视图绑定中最重要的优势之一。如果您尝试将文本观察器分配给按钮，它不会接受，因为绑定类包含具有适当视图类型的引用。

## 易用性

查看装订的唯一目的是替代使用`findviewbyId`。与数据绑定不同，它是轻量级的，所以编译不会花费太多时间。同时，它不支持像数据绑定那样的双向绑定。使用视图绑定，不需要像在数据绑定中那样在每个布局文件中提到 layout-tag。

# 更新

阅读下面的文章，了解更多关于查看投标

[](https://medium.com/better-programming/android-view-binding-components-dialogs-and-adapters-7628f6ce6ff7) [## Android 视图绑定组件:对话框和适配器

### 不再有 findViewById。是时候升级了

medium.com](https://medium.com/better-programming/android-view-binding-components-dialogs-and-adapters-7628f6ce6ff7) 

感谢阅读！