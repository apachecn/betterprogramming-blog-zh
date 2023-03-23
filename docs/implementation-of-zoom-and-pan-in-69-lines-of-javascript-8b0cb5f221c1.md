# 仅用 69 行 Javascript 代码实现缩放和平移

> 原文：<https://betterprogramming.pub/implementation-of-zoom-and-pan-in-69-lines-of-javascript-8b0cb5f221c1>

## 轻量级，可扩展，易于使用

![](img/e6a8d785076db16b2ec3929409eecb9c.png)

由 [twinsfisch](https://unsplash.com/@twinsfisch) 在 [Unsplash](https://unsplash.com) 上拍摄的照片

在最近的一个工作项目中，我必须添加缩放和平移包含多个不同元素的工作空间的功能。我决定分享我的实现，因为它是轻量级的，可扩展的，简单易用的，并且只需要普通的 JavaScript。

我本可以在许多已经编写好的库中进行选择，但是所有的库都有比我真正需要的更多的特性和代码。除此之外，自己写东西总是一个很好的挑战和乐趣(一旦你找到了一路上遇到的所有问题的解决方案)。

在这篇文章中，我将提出我最终的解决方案。不要再说了，让我们言归正传。

# 图书馆的特点

## 简单易用

我选择的一个假设是我的实现必须简单易用。我创建了一个演示程序，其中包含一个带有容器区域的 HTML 文件。我们将在其中操作一些样本元素。容器是一个有一个孩子的根，一个工作区。根放在所有页面上，因为将添加事件(以允许操作工作区)，这些事件必须在站点的任何地方工作。

在样式表文件(`index.css` ) 中，我通过将主体的`overflow`设置为`hidden`来关闭滚动，使用`area`类来设置标记操作区域的边框:

`circle`、`rectangle`和`text-area`是仅为演示目的而定义的职业，所以请不要关注它们(纳瓦霍白色没有帮助)。

同样出于演示目的，我创建了一个带有鼠标事件监听器的`index.js`文件，它将与包含所有库实现的`renderer`模块通信。

我想实现库的易用性，所以我创建了一个`renderer`的实例，并通过了基本配置，如最小比例、最大比例、将被操作的元素和可选的比例敏感度。

## 轻量级且可扩展

该库还必须是轻量级的。最终，它只是 69 行普通的 JavaScript 代码。它可能会更少，但我不想失去可读性。此外，你可以使用我的 [GitHub 库](https://github.com/kwdowik/zoom-pan)上的缩小版。

在模块外部导出的主函数是`renderer`，它有一个状态，描述转换并通过对象组合用可用的特性来修饰它。前缀为`can`的函数能够接受参数状态，根据特性改变被操作的元素。所有的元素转换都由`state.transformation`对象描述。我选择了组合而不是继承，以使新特性的扩展变得非常容易。组合保证了从`renderer`对象中分离特性(每个方法负责每个特性),使得测试易于编写。您可以通过从`Object.assign(…)`中删除一个负责功能的方法(`canZoom(),` `canPan()`)来测试解耦特性。如果你想学习更多关于写作和 JavaScript 的知识，我强烈推荐 MPJ 和他的频道。

## 平移功能

在这两种情况下，库都使用 CSS `matrix`函数来更改`style.transform` 并操作元素。`Matrix`以下列方式组合所有 2D 变换方法`matrix(scaleX()`、`skewY()`、`skewX()`、`scaleY()`、`translateX()`和`translateY())`。

我实现了两个函数，`panBy()`和`panTo()`。两者都只是使用了`pan()`函数，但是`panTo()`也设置了一个新的`scale`并传递了`originX`和`originY`，减去了当前的`transformation.translateX`和`transformation.translateY`值。这是因为元素需要绝对值移动。`pan()`功能的实现非常简单。通过`originX`和`originY`增加属性`transformation.translateX`和`transformation.translateY`；从`getMatrix()`功能生成的新`matrix`值被分配给`style.transform`。

创建`matrix`的函数只返回一个模板字符串，其中包含一个带有传递参数的矩阵。

## 缩放

除了`style.transform` 之外，缩放还使用`style.transformOrigin`来校正放大和缩小期间的元素位置。注释掉第 14 行，看看会发生什么:

除此之外，`zoom()`方法需要一个当前的和新的`scale`，并通过`getScale()`方法获得它们。这是基于当前的`scale`、`deltaScale`和`scaleSensitivity`。保持范围(第 3 行)将返回当前和新计算的`scale`值作为一个元组。

这将根据传递的坐标(`x`、`y`)和元素位置`newOriginX`和`newOriginY`，通过将`originX`和`originY`除以`scale`来计算`originX`和`originY`。接下来，它通过调用高阶函数`getTranslate()`计算`translateX`和`translateY`，该函数返回用`minScale`、`maxScale`和`scale`参数化的函数，并将它们分配给`translate`。

然后调用位置为`originX/originY`的`translate`和之前的位置`transformation.originX/transformation.originY`，并平移`transformation.translateX/transformation.translateY`。基于这些值，它检查`scale`是否在最小值和最大值之间，以及位置是否已经改变。如果有，它通过将最后一个`translate`和其先前和当前位置之差乘以`scale`因子的结果相加来计算新的`translate`值；否则，它只返回最后一个`translate`。

最后，`zoom()`函数将计算值赋给`style.transformOrigin`和`style.transform`，并将一个新对象赋给`state.transformation`。

# 工作示例

要更改工作区(虚线边框)的缩放，您必须使用鼠标滚轮或触摸板并按下“Ctrl”键。您可以通过将`scaleSensivity`属性传递给`renderer`实例来更改缩放敏感度。要更改平移区域，您必须在按下“Shift”键的情况下移动鼠标/触摸板。双击将恢复默认视图。

# **测试**

我为实现的特性创建了一些样本测试。在每次测试之前，我用默认值创建了一个`_element`对象。下面你可以看到对`panBy()`函数的测试。出于测试目的，我喜欢用[摩卡](https://www.npmjs.com/package/mocha)。

潘比试验

基本上，我在`it(...)`块中编写测试逻辑，并将所有测试参数扩展到一个单独的文件中:

我的队友向我展示了这些参数化的测试，我非常喜欢它们。它们对我的情况特别有帮助，我的测试只是在它们的值上有所不同。在我看来，这种方法使测试更具可读性，更易于维护。

# 结束语

我希望这个实现易于理解和使用。欢迎任何想法和评论。

推特: [*k_wdowik*](https://twitter.com/k_wdowik)