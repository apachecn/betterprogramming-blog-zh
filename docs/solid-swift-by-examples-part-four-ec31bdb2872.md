# 界面分离原理

> 原文：<https://betterprogramming.pub/solid-swift-by-examples-part-four-ec31bdb2872>

## Swift 实例中的坚实原则

## 坚固的雨燕

![](img/f0a8449e2e8fea07a8c33bc54b6cf0b4.png)

约翰·巴克利在 Unsplash 上的照片

这是坚实的原则系列的第四部分。在之前的文章中，我们讨论了什么是可靠原理，它们试图解决什么，以及什么是 SRP(I)、OCP(II)和 LSP(III)。

你可以在这里看到以前的原则:

[](https://medium.com/@piero9212/solid-swift-by-examples-part-one-35018d53d3e6) [## 坚固的雨燕

### 完整指南第一部分

medium.com](https://medium.com/@piero9212/solid-swift-by-examples-part-one-35018d53d3e6) [](https://medium.com/@piero9212/solid-swift-by-examples-part-two-82ac3c457e4e) [## 开闭原理

### 在第一部分，我们描述了什么是坚实的原则和他们试图解决什么，解释了第一…

medium.com](https://medium.com/@piero9212/solid-swift-by-examples-part-two-82ac3c457e4e) [](https://medium.com/@piero9212/solid-swift-by-examples-part-three-675672c1ec20) [## 利斯科夫替代原理

### 这是关于坚实原理的出版物的第三部分。在以前的文章中，我们讨论了什么是固体…

medium.com](https://medium.com/@piero9212/solid-swift-by-examples-part-three-675672c1ec20) 

在这一部分，我们将探讨以下原则:

Robert C. Martin 在为施乐公司提供咨询，帮助他们为新的打印机系统构建软件时，定义了接口分离原则。

他将其定义为:

> *“不应该强迫客户端依赖他们不使用的接口。”*

听起来很明显，不是吗？

嗯，违反这个原则很容易。与[单一责任原则](https://medium.com/better-programming/solid-swift-by-examples-part-one-35018d53d3e6)相似，接口分离原则的目标是通过将软件分成多个独立的部分来减少副作用和所需变更的频率。

ISP 帮助我们避免:

*   胖接口。
*   强制类实现它们不应该实现的方法。
*   用很多方法污染协议。

让我们开始编写示例代码:

假设我们必须制作一个餐馆列表，每个单元格都应该能够显示菜单并进行预订:

餐馆建筑

餐馆电池

因此，一旦用户从`RestaurantCell`点击*显示菜单*按钮，应用程序将显示一个新的`View`菜单，以及同样的保留、`markFavorite`和`getDirections IBAction`。

但是，当我们添加一个名为`History`的新视图，显示用户过去的预订，而不是预订，需求定义我们必须显示菜单和`getDirections`作为`RestaurantCell`，但是我们必须添加一个*显示*和*编辑*到预订动作时，会发生什么呢？

因此，我们创建了一个新的细胞类别:

保留结构

预订单元

我们正在处理预订过程，但是实现`ReservationCellDelegate`的类会发生什么呢？

预订视图控制器

使用这种方法，从`ReservationCellDelegate`实现的类被强制实现所有的函数，打破了接口分离原则。

当客户依赖于它不使用的方法时，我们就违反了一些坚实的原则。

让我们使用接口隔离原理来解决这个问题。在这种情况下，可以通过组合并遵循 SRP 来完成:

坚固到可以营救

现在，我们有小协议而不是大协议。这使我们的`ViewController`班更好了:

我们全新的预约 VC

恭喜你，你已经分离了接口，所以现在我们有了彼此独立的特性。

还有其他情况来涵盖和使用接口分离原则。这是经过一些修改后的餐厅结构:

餐厅结构

在我们之前的例子中，当用户点击`showMenu`时，我们共享了这个餐馆结构，但是你注意到了吗，我们可能通过这个动作调用向其他屏幕共享了额外的未使用的信息？

正如你所看到的，我们在像`getDirections`这样的方法中分享了太多的信息，因为它只需要`longitud`、`latitud`和`title`。这是假设请求只是显示一个简单的弹出菜单，其中包含餐馆名称和选择地图服务应用程序的列表。

让我们用我们所学的来解决它:

我们新的餐厅结构

利用 typealias 并更新我们的`RestaurantDirectionsDelegate`和`ReservationViewController.`

这样，我们就不会向`Directions` `View`暴露额外的数据(比如餐馆 ID 和图片)。此外，它对于单元测试非常有用，让我们创建一个模拟类，实现`RestaurantLocation`协议。

# 结论

记得让你的抽象变得可重用、更小和可组合。正如您从 SRP 中学到的，您应该避免大的类和接口。

我希望你喜欢这篇文章，并发现它是有帮助的。感谢阅读。

# 下一章

[](https://medium.com/@piero9212/solid-swift-by-examples-part-five-a860b86b85f5) [## 从属倒置原则

### 这是关于坚实原理的出版物的最后一部分。在以前的文章中，我们讨论了什么是固体…

medium.com](https://medium.com/@piero9212/solid-swift-by-examples-part-five-a860b86b85f5) 

# 参考

[](https://stackify.com/interface-segregation-principle/) [## 坚实的设计原则解释:界面分离与代码示例

### 与单一责任原则相似，界面分离原则的目标是减少副作用

stackify.com](https://stackify.com/interface-segregation-principle/) [](https://medium.com/@piyush.dez/solid-principles-in-swift-e9cc84ff5aa2) [## Swift 坚实的设计原则

### SOLID 代表了面向对象编程的 5 个原则:单一责任、开放/封闭、Liskov 替换…

medium.com](https://medium.com/@piyush.dez/solid-principles-in-swift-e9cc84ff5aa2) [](https://medium.com/the-aesthetic-programmer/ios-solid-principles-pt-4-interface-segregation-principle-a40d534b8d7f) [## iOS —固体原理第 4 部分—界面分离原理

### 界面分离原理认为:

medium.com](https://medium.com/the-aesthetic-programmer/ios-solid-principles-pt-4-interface-segregation-principle-a40d534b8d7f) [](https://marcosantadev.com/solid-principles-applied-swift/) [## 适用于 Swift - Marco Santa Dev 的坚实原则

### 可维护的组件。可重复使用。只是一个梦？也许不是。坚实的原则，也许是出路。固体是一个缩写…

marcosantadev.com](https://marcosantadev.com/solid-principles-applied-swift/) [](https://codeburst.io/solid-design-principle-using-swift-fa67443672b8) [## 使用 Swift 的可靠设计原则

### 坚实的原则是实现/创建有效的、可维护的、可扩展的和松散的…

codeburst.io](https://codeburst.io/solid-design-principle-using-swift-fa67443672b8) 

*   [https://web . archive . org/web/20150905081110/http://www . object mentor . com/resources/articles/ISP . pdf](https://web.archive.org/web/20150905081110/http://www.objectmentor.com/resources/articles/isp.pdf)