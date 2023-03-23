# 如何在 SwiftUI 中将列表滚动到底部

> 原文：<https://betterprogramming.pub/swiftui-scrolling-a-list-to-the-bottom-68337e4d8db0>

## 跳到列表中的不同点

![](img/2f2e25ca72025963f0670e6733ba8ca7.png)

Glenn Carstens-Peters 在 [Unsplash](https://unsplash.com/s/photos/list?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片。

使用 SwiftUI 列表是使用所有自动机制来插入、删除或移动行的一种便捷方式。

但是，如果您添加了一个新条目，则没有正式的方法可以滚动到添加了新条目的列表底部。所以，我试图找到一个解决办法。您会发现一些关于堆栈溢出的问题，但没有真正好的解决方案(我不想用包装的 UITableView 替换列表)。

一个偶然的机会，我发现了一个名为 [Introspect](https://github.com/timbersoftware/SwiftUI-Introspect.git) 的库，它提供了对 SwiftUI 元素背后的大部分 UIKit 内容的访问。

我现在的想法是访问列表后面的 UITableView，然后只使用一个名为`scrollToBottom`的自定义函数。

# 通往幸福之路

*   将自省添加到项目的包列表中。
*   将自省导入到您的自定义内容视图中。
*   将此添加到您的`Add`按钮的动作中:

```
self.tableView?.scrollToBottom(animated: true, yOffset: self.$yOffset)
```

*   为了让编译器接受这一点，我们需要一个状态变量:

```
@State var yOffset: CGFloat = 0
```

*   还有一个:

```
@State var tableView: UITableView?
```

*   将此修饰符添加到列表中:

```
.introspectTableView(customize: { tableView in
if self.tableView == nil { self.tableView = tableView }
else { self.tableView?.setContentOffset(CGPoint(x: 0, y: self.yOffset + 60), animated: true) } })
```

*   添加此扩展名:

```
extension UITableView {
func scrollToBottom(animated: Bool, yOffset: Binding<CGFloat>) {
let y = contentSize.height — frame.size.height
if y < 0 { return }
yOffset.wrappedValue = y
}}
```

## 一些笔记

*   第三个项目符号中的添加触发滚动到当前的`yOffset`。
*   第五个项目符号中的变量需要使`tableView`在整个`CustomView`中可用，因为它似乎在其生命周期中保持不变。
*   第六个项目符号中的修饰语是魔法酱。通过自省，我们可以访问列表背后的 ui table 视图。我们使用`yOffset`来调整内容偏移。无论出于什么原因，都有必要在偏移量(这里是 60)上增加一些偏移量，因为——至少在我的环境中——最后一行是不可见或完全可见的。
*   在第七个项目符号中，设置了新的`yOffset`。因为有了`@State`属性包装器，视图会根据新的偏移量进行更新。

现在，一切都非常顺利(不完美，但以一种可接受的方式)。当然，并不是一切:如果您删除一行，定位就会不稳定(例如，到`yOffset`中的最后一个保存位置)。

我们现在该怎么办？

*   让我们将`yOffset`声明改为`CGFLoat?`(可选)。
*   在列表的`.onDelete(perform:)`事件中，移除操作后增加一个`self.yOffset = nil`。
*   在`.introspectTableView`视图修改器中，在`setContentOffset`的调用前添加一个`guard let yOffset = self.yOffset else { return }`。

现在，事情进展顺利。

# 结论

我在 GitHub 上添加了一个[要点，你可以查看视图的完整代码。它引用了一些没有提供的模型相关的协议。您可以用您的代码替换与模型相关的调用。](https://gist.github.com/innoreq/ad20d8d2a65865ffbf3d0ade89ceda5a)

感谢阅读！