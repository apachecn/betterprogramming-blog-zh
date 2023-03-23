# 构建一个没有政治的棋盘游戏(第 4 部分)

> 原文：<https://betterprogramming.pub/build-a-board-game-without-the-politics-part-4-92ea2ddd78b>

## 通过拖放使用 SwiftUI

![](img/200ddec9653e6326d1f9db4539a0c2ac.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上由[马腾·范登赫维尔](https://unsplash.com/@mvdheuvel?utm_source=medium&utm_medium=referral)拍摄的照片。

本文基于 iOS 11.4 和 2020 年 3 月下旬发布的 Swift 5 的一个版本。警告:如果你还没有更新你的 iDevice 和/或 Xcode，它显然不会工作。

这个简短的系列文章通过几个例子介绍了新的拖放协议的构建。最终目标是创建一个迷你数独板，然后您可以填充它。我们的目标只是创建一个你可以拖动棋子的板子。当然，它可以是你想尝试和构建的任何棋盘游戏的任何棋盘。

在这一部分中，我希望展示我们如何通过使用 struct 而不是 raw 类型来改进我们的代码。

现在，有两件事一直困扰着我。第一个是当你玩游戏时，你经常发现自己在最后一轮被束缚住了。你早几步就做出了错误的决定，这已经让你自食其果了。

第二，当我实现了对缺失腿筋问题的解决方案时，我开始意识到我忽略 MVC 规则有点太多了，现在它也赶上了我。

让我们实现一个步进器功能，让你逆转你所做的决定。如果你愿意的话，一个撤销(长期记忆)功能。然后，让我们后退一步，重新编写应用程序，以改进我们应该已经到位的模型，看看我们还可以改进多少代码。

对于步进器，首先将以下代码添加到主界面:

我们引入了两个新的变量:

*   一个`sliderDB`数组，包含你移动时的数据。
*   一个跟踪他们的`sliderValue`。

我还编写了一个快速助手函数来帮助管理这些值:

显然，您需要将变量添加到主循环中，并在正确的位置调用对新结构的引用。

```
@State private var sliderDB = [(Int?,String?,Color?)](repeating: (nil,nil,nil), count: 100)
@State private var sliderValue: Int = 0
```

更新`DropDelegate`链接，我们将在其中引用这个方法和我们的怪物矩阵。这是代码。将此块添加到测试下的矩阵中，看看`self.poke !` = ":

而这挡到了`DropDelegate`法。不要忘记在方法的开头绑定两个新变量:

```
self.sliderDB[Int(self.sliderValue)] =  (self.textID,text,backgrounds[(Int(text)! - 1)])
self.sliderValue = self.sliderValue + 1
```

好了，你应该准备好了。编译并运行。现在你可以添加方块到你的游戏中，玩到一半时，使用步行者返回和前进的路径。

抱歉，我差点忘了:你应该在用户关闭磁贴时删除历史记录。这个代码也属于怪物矩阵。我包括了其他陈述，以便您可以确保在正确的位置得到它:

```
self.sliderDB[Int(self.sliderValue)] = (nil,nil,nil)
self.textText[fCalc(c: column, r: row, x: self.textValue.count)] = ""
self.textColors[fCalc(c: column, r: row, x: self.textValue.count)] = Color.clear
```

回顾我的代码，我认为公平地说，我对 MVC 的解释是创建一个“大规模视图控制器”——而不是一个模型、视图和控制器，就像它本来应该的那样。

当我试图将一些方法分解成更简单的方法时，这变得更加明显。让我们回顾一下刚刚输入的代码。我们怎样才能做得更好？我首先把我添加到矩阵中的新代码剪下来，放入它自己的函数中:

对函数的调用如下所示。没那么简单吧。

```
cellHistory(column: column, row: row, tVcount: self.textValue.count, poke: self.poke, sliderValue: &self.sliderValue, sliderDB: &self.sliderDB, textText: &self.textText, textColors: &self.textColors)
```

这里的一个问题是，虽然我的历史记录的数据结构(一个元组)比我用于瓷砖上的文本和颜色(它们是分开的)的简单的双数组更有效，但它看起来和感觉起来仍然像一顿狗的晚餐。

让我们通过定义一个适当的结构(不是元组)并通过代码中的一行引用它来使它更清楚:

现在我们在`ContentView`中将它定义为`ObservedObject`:

```
@ObservedObject var stepperInst:StepperData
```

并重新定义它与`DropDelegate`、`cellHistory`和`cellsUsed`方法的关系:

`DropDelegate`的召唤:

```
let dropDelegate = TheDropDelegate(textID: $textID, textText: $textText, rect: $rect, textColors: $textColors, startStop: $startStop, stepperInst: stepperInst)
```

`DropDelegate`本身:

用`performDrop`中的这一小段代码:

```
let newRec = stepperSteps(stepIndex: self.textID, stepText: text, stepColor: backgrounds[Int(text)! - 1])
self.stepperInst.stepperDB[self.stepperInst.stepperValue] = newRec
self.stepperInst.stepperValue = self.stepperInst.stepperValue + 1
```

进行更改并进行测试。你发现窃听器了吗？如果您先放置一个图块，然后在上面放置另一个图块，它会记录这两个动作。我们需要解决这个问题。这里有一个新的`cellHistory`方法:

嗯，就是这样。我希望你同意这是一个更干净的解决方案。让我们以同样的方式为瓷砖实现一个。我们从`rect`数组开始。这是一个新的`InsideView`，它将数据归档到我们的`stepperInst`:

这里有一个新的`DropTarget`:

好的，您需要对代码进行修改，这些代码引用了具有相同名称的其他版本，但是新版本以“v”结尾。

然后你可以再次简化对`DropTarget`的调用:

```
let dropDelegate = TheDropDelegate(textID: $textID, textText: $textText, textColors: $textColors, startStop: $startStop, stepperInst: stepperInst)
```

我继续走这条路，重写/重新定义了大多数方法，以使用新发布的 struct。我不打算回顾我所做的所有修改，因为我不认为这会使阅读变得有趣。如果你准备好迎接挑战，我建议不要把显示的文字/颜色和选择的文字/颜色混淆。我的最终结构如下所示:

# 结论

我本可以做得不同，只保存一个文本/颜色和一个标志来决定它是否应该显示，但我没有。说到这里，我想是时候开始新的项目了。