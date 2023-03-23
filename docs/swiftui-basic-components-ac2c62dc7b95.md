# SwiftUI —基本组件

> 原文：<https://betterprogramming.pub/swiftui-basic-components-ac2c62dc7b95>

## SwiftUI 中 UI 组件的简单介绍

![](img/2b2b0c744bd622abfc30025c697377d1.png)

我将使用 SwiftUI 呈现一系列 UI 组件。每个组件将用几行简单的文字来描述。你可能会发现这篇文章作为指南很有用。

# 内容

*   用户输入:切换、滑块、文本字段、选择器、日期选择器和分段控件
*   容器:滚动视图、表格和表单
*   演示者:警报、行动表和弹出窗口
*   SwiftUI 示例项目

# 触发器

Toggle 的目的很简单:它用于绑定一个属性。在某些情况下，我们可以用它来改变屏幕，显示或隐藏其他视图。

SwiftUI —切换

![](img/d2007a9fa11e9d10fac5210be8287dfe.png)

iPhone 截图

# 文本字段

要使用文本字段，我们只需要指定一个占位符(字符串)和一个绑定。

SwiftUI —文本字段

![](img/88e67e184819fd0ba2c66c7db279c4ec.png)

# 采摘者

Picker 在 SwiftUI 中有一个加号。在表单中使用它，应用程序将导航到一个详细的选项列表视图。

如果我们想用经典的内联样式显示 picker，我们只需添加行`.pickerStyle(.wheel)`。

SwiftUI —选取器

![](img/ba1fe5eb6f97da8765c425f600997cb7.png)

# 分段控制

分段控件是在同一个屏幕上显示不同视图的好工具。

SwiftUI —分段控制

![](img/754da1fc2af5fd5ad2ccab1699f7dd83.png)

# 容器

## 滚动视图

通过`ScrollView`视图实现:

SwiftUI —滚动视图

![](img/e32e2c6de5bbff240c278f01cef698d6.png)

## 项目清单

要在 SwiftUI 中显示表格，我们应该使用列表视图。该列表可以根据您的需要显示静态或动态内容。然而，它使用起来很简单，因为我们不需要写很多代码来注册单元格或告诉它有多少行等等。

使用对象数组(可识别协议)和内容块中的子视图:

SwiftUI —列表

![](img/b366477bbd5f4b8457d6d5ba2ccf15a2.png)

## 形式

SwiftUI 的表单像容器一样工作，所以我们可以在其中添加其他视图，它们会自动调整它们的行为和样式。

我们可以用截面视图分离输入组件:

SwiftUI —表单

![](img/047b2b9ed560fca4ac4b149fc987f5b3.png)

# 介绍会；展示会

这一部分展示了我们展示信息的一些方法。

## 警报

要创建一个简单的提醒，我们必须定义一个标题和消息，并添加一个解除按钮。

为了显示警报，我们添加了一个可绑定的条件来决定警报是否可见。

例如，下面的代码使用了一个`showingAlert`(布尔值)来跟踪是否应该显示警告:

SwiftUI —警报

![](img/50f054bbd211074b385c87fcac389fe8.png)

## 行动表

ActionSheet 的实现与 Alert 相同。

SwiftUI —行动表

![](img/776552c7668ee398b6e75afe2f40dd8a.png)

## 波普沃

按照 Alert 使用的相同逻辑，如果我们想要显示弹出窗口，我们必须传递一个布尔绑定来控制弹出窗口的可见性并指定内容:

SwiftUI — Popover

![](img/83ddd8c4add85d2c5ff15be76ee308df.png)

# SwiftUI 应用程序

如果您想查看之前的示例和 SwiftUI 的其他组件，我已经创建了一个示例项目，从中我们可以看到如何使用 SwiftUI 创建一个应用程序。这是资源库链接:[https://github . com/lucasPelizza/SimpleSwiftUIExample/tree/master](https://github.com/lucasPelizza/SimpleSwiftUIExample/tree/master)

![](img/ba0e0e91b49b0c92f2f07c0a11af1960.png)![](img/1bd332c7c355c0ce9e93e9122b2fd20d.png)![](img/eeae4c5d272c8a2d116e4bdd5b4cdbd4.png)

SwiftUI 示例

# 最后的话

我希望这篇文章对你有帮助！