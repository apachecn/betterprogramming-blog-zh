# 我从 React 到 React Native 的旅程

> 原文：<https://betterprogramming.pub/my-journey-from-react-to-react-native-e876c31a46a4>

## 当我从 web 应用程序切换到本地应用程序时，发生了哪些变化

![](img/88349f86ec971d0f031fde7b533295f0.png)

[Mukuko 工作室](https://unsplash.com/@mukukostudio?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

我最近开始开发一个 Android 应用程序，作为一名 React 开发人员，我很容易选择使用和测试 React Native，因为它帮助我呆在自己的舒适区，也让我有机会在某一天探索 iOS。

即使是同一个框架，在本地应用中使用 React 与在 web 上使用 React 还是有一点不同。

我写这篇文章是为了分享我发现的这两个平台之间的主要差异，以及我必须找出的一些技巧，以获得期望的最终行为。

# 视图或文本—没有 div

在开发 web 应用程序时，我们通常会将`div`和`span`标签用于多种用途。既然我们不在网上，这就不再可能了。

相反，内容是由`View`和`Text`组成的，我们可以将它们与上面的两个标签相关联，但是它们有一些额外的约束。

## 视图元素

使用`View`元素，除了组件，你不能在里面添加任何东西。这意味着它不能包含文本，而这正是`Text`组件的用途。不幸的是，它在你的应用程序中有一个更大的树，但是它有助于将布局和文本分开。

React Native 中的 Hello world 组件

基于前一点，您可以很容易地发现您不能将文本相关的样式应用到一个`View`组件。像`color`或`fontSize`这样的文本样式需要应用到`Text`组件。

视图上的布局样式，文本上的文本样式

`View`也是一个 *flexbox* 容器，只能支持两个显示值:`none`或`flex`。如果您对模型没有信心，它可以改变许多事情，但是它比 DOM 上默认使用的经典块模型更强大。

你可以在 [CSS-Tricks](https://css-tricks.com/snippets/css/a-guide-to-flexbox/) 上了解更多关于这个布局系统的信息。React Native 支持所有 flex 属性，从`align-items`到`flex-grow`。

然而，网页版和本地版之间有一个主要的区别:默认值`flex-direction`。如果我们在 web 上有`row`，它在 React Native 中被设置为`column`。基本上，这意味着默认情况下元素是从上到下放置的，而不是从左到右。

React Native 上的 Flexbox 用法

最后，`View`不可点击。如果你需要一个点击行为，你必须把它包装成一个`Touchable*`组件:

*   `TouchableHighlight`点击添加背景色。
*   `TouchableOpacity`减少点击时的不透明度。
*   `TouchableWithoutFeedback`对 click 没有反馈，出于用户体验的原因我不推荐。
*   `TouchableNativeFeedback`(仅在 Android 上)在按钮上产生涟漪效应。

TouchableHighlight 用法示例

## 文本元素

如果我们可以很容易地将`Text`元素与 web 上的`span`标签进行比较，这种差异就像视图一样明显。

`Text`元素——顾名思义——的存在只是为了呈现文本内容。我们不能将它用于任何我们可能需要的与布局相关的东西。因此，`display: "flex"`将没有任何作用。`position`也不会。

然而，`Text`像在 web 上一样从父`Text`组件继承样式。

文本组件样式继承

像`View`一样，`Text`组件是不可点击的。如果这是您需要的行为，您将不得不包装成一个`Touchable*`组件。

最后，`Text`仅仅意味着包含文本和其他`Text`组件。您不应该包含布局相关的组件，如`View`、`ScrollView`或`FlatList`。

# 用 TextInput 替换输入

由于原生 API 不是 DOM，我们也没有`input`元素，但是 React 为我们需要表单的时候提供了一个组件。

组件`InputField`的工作方式与`input`相同，但是它也有一个`onChangeText`属性，该属性接受一个以值作为参数的回调。不再需要`event.target.value`！

TextInput 和 onChangeText 回调

# CSS 的用法

如果我在开发 web 应用程序时使用了 [CSS 模块](https://github.com/css-modules/css-modules)，那么在 native 上就有点不同了，在 native 上 CSS 的使用更像是 CSS-in-JS 的方式。样式表是用 React Native 提供的`StyleSheet.create`方法创建的，它是组件的类/样式的键/值对象。

使用 StyleSheet.create()设置样式

如果 CSS 中有单位，那么 React Native 中没有——或者更准确地说，单位总是在`dp`中设置，因此即使手机没有相同的像素比率，渲染也是正确的。这使得 CSS 的使用有点不同，但如果你想让事情更简单，就把它们看作像素。

如果我们以前在 CSS 中有快捷方式，那么在 React Native 中就不一样:`padding`必须是一个数字，而不是字符串中的值列表，`backgroundColor`用于颜色，等等。

为了说明这个规则，假设 CSS `padding: "8 16"`无效，那么`background: "#333333"`也无效。

即使这些输入起来有点长，我发现它比我们习惯的快捷方式更明确。另外，对于初学者来说，理解它们总是很复杂。

几个小时后，我已经完全采用了这种编写 CSS 的新方法。

dp 单位和快捷方式

# 可缩放矢量图形

如果 SVG 在 web 上被大量使用，那么它在 React Native 中是不受支持的。我们需要用一个外挂包:`react-native-svg`。

然而，这个包的使用方式和网页上的完全一样，只是有一点不同:第一个大写字符。

React Native 中的简单 SVG

# 泛滥

如果你想要一个可滚动的`View`，你需要切换到`ScrollView`组件。它的行为是一样的，但有一个内置的滚动条。

由于组件默认有一个垂直滚动，您可以使用`horizontal`属性使其在 *x* 轴上滚动。

出于性能原因，您也可以使用`FlatList`组件，它使用起来有点复杂，但是它会让您的长列表快速滚动。如果是你需要的东西，我鼓励你去[看看官方文档](https://reactnative.dev/docs/flatlist)。

# 提示和技巧

## 可触摸组件应用于单个元素

如果您得到错误`Error: React.Children.only expected to receive a single React element child`，那么您只需要将您的元素包装在一个新的`View`组件中。

做什么似乎很明显，但是当来自 web 时可能会有点麻烦:当使用`Touchable*`组件时，您需要有一个布局项目。

## `Text`的断线

在 web 上，新行是用`<br />`做的，但是由于 native 不是 DOM，你可以简单地在你的`Text`组件中使用`{"\n"}`或者直接在一个字符串中使用`{"\n"}`(例如`<Text>{"Hello\nworld!"}</Text>`)。

## 文本中的视图

在`Text`元素中不能有`View`元素。这将抛出以下错误:`Cannot add a child that doesn't have a YogaNode to a parent without a measure function!`。

由于一些代码重复，这可能会使您的树变得更加复杂，但是您应该总是找到避免这种消息的方法。

# 结论

尽管 React Native 是基于 React 的，但还是有很多不同之处。一方面，我们在 web 上使用 React 并使用 DOM API。另一方面，我们使用 Android、iOS 和其他系统的原生布局。但是还是很容易进入的。不要犹豫，试试吧！