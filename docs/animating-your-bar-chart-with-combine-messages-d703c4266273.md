# 用组合消息制作条形图动画

> 原文：<https://betterprogramming.pub/animating-your-bar-chart-with-combine-messages-d703c4266273>

## “在 SwiftUI 中将动画链接在一起以构建条形图”的第 2 部分

![](img/593b173794c2abf7fb3aaf62a5e71b14.png)

原生 Swift UI 动画条形图

前几天我写了一篇文章，其中我谈到了构建基本的条形图:“[在 SwiftUI](https://medium.com/better-programming/linking-animations-together-to-build-bar-charts-in-swiftui-6a51e3c9bb94) 中将动画链接在一起以构建条形图。”我不会再发布最终代码，但我会展示我们成功构建的内容。

简而言之，这是一个动画，自动着色，数据刷新的条形图。我保持了甜蜜和简短，但不禁觉得我想做得更多。

让我们根据条形图主题做一个新的简报。这里有一个愿望清单:

*   目前，值是标签。我想添加第二个文本标签。
*   目前，我没有任何轴上的图例。我们试着加一个。
*   我希望能够动态删除酒吧留下其他地方。
*   我希望能够动态插入酒吧离开其他地方。
*   我希望能够呈现排序的酒吧，最大的第一或最后(视觉上，理想)。
*   我希望能够改变用于酒吧的颜色(即，覆盖自动着色。)

这些事情有些很容易，有些不那么容易——至少有一件是几乎不可能的——所以让我们开始吧。

我将从文本标签开始，因为仅显示值的条形图并不那么有用。我也想添加一个轴标签。我们在找这个。

![](img/4280a97a2baf213b78af93d812210a8d.png)

带轴和标签的动画条形图

好吧，它并不完美…轴心真的不应该被动画化成那样，但这是一个进步。我是怎么做到的？显然，它基于上一篇文章中的代码，只是我交换了轴，添加了一个文本字段作为第二个发布对象，并对布局代码做了一些小的修改。

Bon，这已经是愿望清单的一半了——告诉你有些愿望并不难。后半段呢？为此，我将使用 SwiftUI 1.0 中可用的 Combine 框架，并用它创建一个动作集合。

```
var hideColumn = PassthroughSubject<String,Never>()
var showColumn = PassthroughSubject<String,Never>()
var changeColumn = PassthroughSubject<(String,String),Never>()
var changeFigure = PassthroughSubject<(String,Int),Never>()
```

实际上，添加代码来匹配这些操作，并更改我们的条形图。现在我使用 switch 语句来测试一切(仅限 SwiftUI 2.0)。

代码将告诉条形图隐藏两列，将一列带回来，更改几个列名，然后更改葡萄、菠萝和桃子类别的一些值。让我们来看看它的作用。

![](img/a2fc463fb33693789fb44a3cf989021f.png)

带有名称更改、值更改、列更改的动画条形图

仍有工作要做，但我们正在取得进展。几个悬而未决的问题:

*   当我们隐藏一列时，除非我们删除它，否则它会继续占用空间。
*   显然，如果我们隐藏并删除了一个列，那么不幸的是，还没有办法将它重新插入。
*   当我们改变棒线的值时，指数的移动不仅非常明显，而且非常烦人。

我们继续吧。继续沿着这条路走下去，我可以使用 Combine 框架向我们的代码中再添加三个动作。

```
enum sortDirection {
  case accending, decending
}var changeColour = PassthroughSubject<(String, Color),Never>()
var insertColumn = PassthroughSubject<(String,Bool),Never>()
var sortColumns = PassthroughSubject<sortDirection,Never>()
```

尽管等等——这显示了我们设计中的一个缺陷。您看，尽管您可以轻松地插入列和/或对它们进行排序，但当前的数据模型将这两项视为单独的元素，即字符串和整数。旧代码如下所示:

```
final class Figures: ObservableObject {
  @Published var text = ["Apples","Apricot","Bananas","Blueberry","Figs","Papaya","Pears","Pineapples","Peaches","Plums"]
  @Published var numbers = [90,80,70,60,50,40,30,20,10]
  static var shared = Figures()
}
```

这是我们需要重构的代码；显然，这也对我们代码的其余部分有一点影响。

鉴于数字现在包含在描述旁边的结构中，我们可以很容易地对它们进行排序。让我们使用 case 语句进行更多的测试。

```
case 0:
  insertColumn.send(("Cherries",45))
case 1:
  sortColumns.send(.accending)
case 2:
  changeColour.send(("Cherries",.yellow))
```

第一次点击将告诉我们的应用程序在图表中添加一个樱桃条。第二次点击将告诉它对条形图进行数字排序，第三次点击将手动改变新樱桃条的颜色。

![](img/90e2cc2d6587ae46e454476241df8e41.png)

显示添加的列、排序的列、更改的列颜色的动画条形图

和以前一样，它并不完美。理想情况下，我们希望对屏幕上的条形进行排序，并且我们仍然让该轴在屏幕上移动。尽管如此，我担心我又没有时间了。我们确实设法初步实现了愿望清单上的所有六个项目。相当不错。

这里是该项目的完整代码，供您浏览和使用。我可能还会考虑做更多的图表，不同类型的图表。跟着我，保持联系。

作业:排序是纯数字的，但是你可以创建一个字母排序。排序只是对升序值进行排序，因此您可以添加一个降序选项。轴上的标签需要固定，这样它们就不会乱动了。屏幕排序也许是可能的，尽管我还不确定如何实现——也许你能找到一个解决方案。颜色与值相关联，最好将它们与文本标签相关联。

保持冷静，继续编码。