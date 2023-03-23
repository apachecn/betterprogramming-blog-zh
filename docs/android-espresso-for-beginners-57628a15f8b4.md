# 适合初学者的安卓浓缩咖啡

> 原文：<https://betterprogramming.pub/android-espresso-for-beginners-57628a15f8b4>

## 用 Espresso 编写可靠的 Android UI 测试

![](img/5f48aebfbd91be87af71a4e5d7179c2b.png)

[自由库存](https://unsplash.com/@freestocks?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 这篇文章的要点

在本文中，您将学习如何使用 [Espresso](https://developer.android.com/training/testing/espresso) 来基于`id`、`text`等属性在布局中查找视图。然后，您将学习如何检查视图行为(如可见性)和执行动作(如`click`)。本文还包括如何在`RecyclerView`上执行测试。

# 介绍

无论您在什么平台上工作，随着产品的发展，测试都会变得复杂。出于这个原因，最好使用像 Espresso 这样的测试框架来使这个过程可靠、简单和有效。Espresso 是 Android 团队的一个测试框架，旨在让开发人员在实现测试用例时变得轻松。它作为单元测试的抽象层，使得编写可靠的 Android UI 测试变得容易。不要再耽搁了，让我们开始吧。

当我们创建一个 Android 项目时，在 Java 目录下，您会发现三个子目录，其中包含应用程序包名称。

*   第一个目录是编写应用程序源代码。
*   第二个目录是编写 Android 测试用例，就像我们在本文中用 Espresso 做的那样。
*   第三个目录是单元测试用例。

# 综合

如今，当你在 [Android Studio](https://developer.android.com/studio) 中创建新项目时，默认集成了 Espresso。如果您在现有项目中删除了这些库，或者如果您在 dependency 标签下没有看到 Espresso 实现库，请添加以下几行。

浓缩咖啡集成

# 成分

浓缩咖啡基本上有三种成分:

*   `viewMatcher` s:允许您通过`id`、`text`等各种参数在当前视图层次中查找视图
*   `viewAction` s: 允许您对`click`等视图执行操作
*   允许你断言一个视图的状态

看看这些组件的一般用法。

浓缩咖啡使用演示

## **onView**

使用这个函数，我们可以在视图层次结构中缩小到所需的视图。这可以通过使用`onview`中的以下函数来完成:

*   `withId(` —通过此功能，我们可以使用视图的 id 缩小视图搜索范围。
*   `withText()` —在这里我们可以传递所需的文本，以缩小基于文本的视图范围。

## **检查**

使用`matches` 和`doesNotExist`来检查所需视图的行为，如可见性、焦点等。

## **执行**

通过`perform`，我们可以对视图执行操作。以下是我们可以使用 Espresso 执行的一些操作:

*   `click()` —点击`onView`中传递的视图
*   `typeText()` —通过该函数，我们可以传递要在视图中输入的字符串。这对于`EditText`来说很方便。
*   `replaceText()` —用传递的字符串替换当前文本
*   `closeSoftKeyboard()` —关闭键盘

# 使用

假设我们有一个带有活动的应用程序，它的布局包含一个 id 为`tv_hello`的视图。我们的目标是发现 id 为`tv_hello`的视图是否显示在屏幕上。看一看:

首先，它将开始搜索 id 为`tv_hello`的视图，一旦使用`check`函数找到视图，它将通过`isDisplayed()`验证可见性。

我们还可以使用多个约束来缩小视图搜索范围，如下所示:

在这个测试中，当搜索一个视图时，它将考虑两个约束:`withId` (具有特定的 id)和`withText`(具有视图中的特定文本)。

## 行动

现在是时候对缩小的视图执行操作了。要执行动作，我们必须调用`onView`上的执行功能来执行所需的动作，如下所示:

我们还可以将多个动作作为参数传递给`perform`函数，如下所示:

在这个测试中，首先在视图上输入“Hello ”,然后执行`click`动作。只有当两个动作都正确执行时，测试才会成功。

## 列表

我们已经完成了标准布局的基本测试。如果我们有复杂的视图，比如列表，该怎么办？为了在列表上编写测试用例，我们有另一个函数叫做`onData`，类似于`onView`。

假设我们有一个字符串为`ArrayList`的列表，我们需要找到文本为`Kotlin`的条目，并对该特定条目执行`click`功能。看一看:

在实时场景中，事情不会像这样简单。假设我们有一个多次点击的`listview-item`，我们需要在内容为`Kotlin`的项目中测试 id 为`edit`的视图的`click`功能。看一看:

# 浓缩咖啡回看

`RecyclerView`对象的行为不同于`AdapterView`对象；我们不能用`onData()`来测试`RecyclerView`。

为了在`RecyclerView`和 Espresso 之间创建一个交互，我们需要添加`espresso-contrib`模块，它有`RecyclerViewActions`来执行像`click`、`scrollTo`这样的动作，以及更多有用的东西。

## 综合

为了集成`espresso-contrib`模块，在 app level `build.gradle`文件的依赖节点下添加下面一行。

## 再循环操作

*   `scrollTo`:将`RecyclerView`滚动到匹配的项目
*   `scrollToHolder`:用于将`RecyclerView`滚动到匹配的视图支架。
*   `scrollToPosition`:用于将`RecyclerView`滚动到上述位置，如果没有该位置，测试将失败。
*   `actionOnHolderItem`:该`RecyclerViewAction`对匹配的视图固定器执行`ViewAction`。
*   `actionOnItem`:该`RecyclerViewAction`对匹配的视图执行`ViewAction`。
*   `actionOnItemAtPosition`:该`RecyclerViewAction`在特定位置的视图上执行`ViewAction`。

## 使用

使用`RecyclerView`的第一步是使用`onView`函数和`RecyclerView`的`id`找到`RecyclerView`，如下图所示:

```
**onView**(ViewMatchers.**withId**(R.id.**recycler_view**))
```

让我们从一个简单的滚动测试开始。我们的目标是将`RecyclerView`滚动到特定位置。为此，我们在`recyclerviewAction`上使用`**scrollToPosition**` ，如下所示:

为了更深入地探索，让我们编写一个测试，我们的目标是用内容`Kotlin`来`click`这个项目。看一看:

这里`hasDescendant`功能是关键。它将检查列表中每一项的后代视图，如果匹配，它将执行`click`动作。

## 项目级视图-在 RecyclerView 中点击

不幸的是，没有现成的直接函数来测试`RecyclerView`项目的内部视图点击。为此，首先我们需要创建一个扩展`ViewAction`的自定义动作，如下所示:

现在使用上面的自定义，我们可以执行项目级视图点击，如下所示:

仅此而已。希望你学到了有用的东西。感谢阅读。