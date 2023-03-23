# 用 Flutter Web 构建单页面应用程序

> 原文：<https://betterprogramming.pub/build-a-single-page-application-with-flutter-web-312c03d0e488>

## 使用 Flutter 建立一个带有狗图像的 SPA，并使用 Firebase 主机为生产做好准备

![](img/8ae6c9d2ec1bb7551bdd5b4681d57e96.png)

单页应用程序是一个网站，所有的内容都显示在一个单独的(长的)可滚动页面上。页面可以是静态的，但也可以在您滚动时从服务器动态更新。它已经成为一种非常流行的设计模式。例子包括网飞和特雷罗。

SPA 是展示产品数量有限的小型企业的理想选择。

你可以阅读大量的文章来了解更多关于 spa 的知识，例如， [*单页申请:它是什么，它实际上是如何工作的？*](https://www.monocubed.com/blog/what-is-single-page-application/)[*2022 年构建单页面 app 的利弊*](https://gearheart.io/articles/pros-and-cons-building-single-page-applications-2019/) *。*

在本教程中，我们将构建一个非常简单的 SPA，按品种显示狗的图像。以上是截图示例。使用以下 URL 运行 SPA:`[https://dog-spa.web.app](https://dog-spa.web.app)`。它具有以下特点:

1.  内容是延迟加载的，即启动时只加载前几个品种的图像，其余的在向下滚动时加载。这使得第一个品种出现的时间更短。
2.  每个品种的网址会随着你的滚动而更新，所以如果你对拉布拉多最感兴趣，你可以在观看该品种时将网址加入书签，以后你(和你邮寄网址的朋友)可以通过这个网址直接进入本部分:`[https://dog-spa.web.app/?breed=labrador](https://dog-spa.web.app/?breed=labrador)`
3.  如果你点击菜单按钮，会显示一个菜单，很容易导航到不同的品种。

你将学习如何建立这个温泉。教程假设你有关于构建一个 Flutter 应用的基础知识。

# 步骤 1:使用 ListView.builder

使用 Android Studio 或`flutter create`命令创建一个新的 flutter 项目。用以下代码替换`main.dart`:

```
import 'package:flutter/material.dart';
import 'dart:ui';
import 'constants.dart';
import 'dogwidget.dart';

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  MyApp({Key? key}) : super(key: key);
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: MyHomePage(),
    );
  }
}

class MyHomePage extends StatefulWidget {
  MyHomePage({Key? key}) : super(key: key);
  @override
  State<MyHomePage> createState() => _MyHomePageState();
}

class _MyHomePageState extends State<MyHomePage> {
  @override
  Widget build(BuildContext context) {
    print('********** Main build');
    return Scaffold(
      appBar: AppBar(
          title: Center(
              child: Text('Dog Breeds Single Page Application',
                  style: TextStyle(color: Colors.white, fontSize: 40)))),
      body: ScrollConfiguration(
        behavior: ScrollConfiguration.of(context).copyWith(dragDevices: {
          PointerDeviceKind.touch,
          PointerDeviceKind.mouse,
        }),
        child: ListView.builder(
            cacheExtent: 0,
            itemCount: dogBreeds.length,
            itemBuilder: (BuildContext context, int index) {
              return Container(
                  color: Colors.primaries[index.remainder(Colors.primaries.length)],
                  child: DogWidget(dogBreeds[index]));
            }),
      ),
    );
  }
}
```

`ListView.builder`加载一个小部件列表。在我们的例子中，它是同一个小部件`DogWidget`，由一个参数控制。但是您也可以在`constants.dart`中有一个小部件名称列表，并为每个索引返回一个不同的小部件。

如果没有`ScrollConfiguration`小部件，您可以使用鼠标上的滚轮和滚动条来滚动，但不能通过按下鼠标左键来拖动。更多信息[点击此处](https://docs.flutter.dev/release/breaking-changes/default-scroll-behavior-drag#copy-and-modify-existing-scrollbehavior)。

在品种列表中添加`lib/constants.dart`:

```
const List<String> dogBreeds = [
  'airedale',
  'beagle',
  'dalmatian',
  'cockapoo',
  'dachshund',
  'havanese',
  'germanshepherd',
  'labradoodle',
  'labrador',
  'pekinese',
];
```

添加`lib/dogwidget.dart`:

```
import 'package:flutter/cupertino.dart';
import 'package:flutter/material.dart';
import 'package:http/http.dart' as http;
import 'dart:convert' as convert;
import 'dart:math';
import 'dart:ui';

class DogWidget extends StatelessWidget {
  final String? breed;
  const DogWidget(this.breed, {Key? key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    print('DogWidget build for breed $breed');
    return Center(
      child: FutureBuilder(
          future: http.get(Uri.https('dog.ceo', '/api/breed/$breed/images')),
          builder: (BuildContext context, AsyncSnapshot<http.Response> snapshot) {
            if (snapshot.connectionState == ConnectionState.waiting) {
              return Container(
                  height: 500,
                  child: const CupertinoActivityIndicator(
                    radius: 100,
                    color: Colors.white,
                  ));
            } else if (snapshot.connectionState == ConnectionState.done) {
              if (snapshot.hasError) {
                return const Text('Error');
              } else if (snapshot.hasData) {
                var jsonResponse = convert.jsonDecode(snapshot.data!.body);
                List imgList = jsonResponse["message"];
                print('$breed number of images ${imgList.length}');
                return Column(children: [
                  Text('Breed: $breed', style: const TextStyle(color: Colors.white, fontSize: 25)),
                  if (imgList.length > 0) dogRow(imgList, context, first: 0),
                  if (imgList.length > 4) dogRow(imgList, context, first: 4),
                  if (imgList.length > 8) dogRow(imgList, context, first: 8),
                ]);
              } else {
                return const Text('Empty data');
              }
            } else {
              return Text('State: ${snapshot.connectionState}');
            }
          }),
    );
  }

  Widget dogRow(List imgList, BuildContext context, {int first = 0}) {
    return ScrollConfiguration(
      behavior: ScrollConfiguration.of(context).copyWith(dragDevices: {
        PointerDeviceKind.touch,
        PointerDeviceKind.mouse,
      }),
      child: SingleChildScrollView(
        scrollDirection:Axis.horizontal,
        physics: BouncingScrollPhysics(),
        child: Row(
        mainAxisAlignment: MainAxisAlignment.center,
        children: [
          for (int i = first; i < min(first + 4, imgList.length); i++)
            Padding(
              padding: const EdgeInsets.all(10),
              child: ClipRRect(
                borderRadius: BorderRadius.circular(15.0),
                child: Image.network(
                  imgList[i].toString(),
                  height: 200,
                ),
              ),
            )
        ],
        ),
      ),
    );
  }
}
```

这个小部件使用 [Dog CEO Dog API](https://dog.ceo/dog-api/) 为一个品种检索多达 12 张图片。它以一到三行的形式返回它们。例如:

![](img/85882dc7b7919d0b5055edfac659dd0e.png)

如果视口不够宽，无法显示一行中的所有狗，我们希望这一行可以水平滚动(通过拖动)，因此有了`SingleChildScrollView`和`ScrollConfiguration`小部件。

`dogWidget`使用 pub.dev 上的 [HTTP](https://pub.dev/packages/http) 包，用这个更新`pubspec.yaml`并执行`Pub get`。然后打开命令行窗口，转到项目目录，输入这个命令:`flutter run -d chrome`。(或者，如果你更喜欢微软 Edge 而不是谷歌 Chrome，`flutter run -d edge`。)在命令行窗口中，您将看到以下内容:

![](img/44eadb0c9da910028ee06cb6b8c37a48.png)

只装两个第一品种，带`ListView cacheExtent: 0`。使用默认缓存，将预装四或五个品种。

另请注意，在`DogWidget`中，库比蒂诺活动指示器周围的`Container height:500`。如果没有它，小部件的初始大小将会很小——当图像被加载后，它将增长到最大大小。并且当`ListView`确定有多少窗口小部件适合视口时；初始大小很重要。

在 Chrome 窗口中向下滚动，观察命令行窗口中出现的其他 Dogwidget 构建行。

## 测试 SPA

人们可以写一篇关于这个主题的长篇教程。在这里，我将给出一些提示和建议:

1.  大多数测试将通过`flutter run -d chrome`完成。您可以添加打印语句来显示您的位置和变量值。可以在命令行窗口和 Flutter 消息中看到输出。
2.  [Flutter DevTools](https://docs.flutter.dev/development/tools/devtools/overview) 是一套用于 Dart 和 Flutter 的性能和调试工具。可以通过命令行窗口中显示的 URL 访问 Flutter 检查器和调试器(参见上面的示例)。使用调试器，您可以设置断点、检查变量等等。检查器非常适合调试 GUI。
3.  Chrome DevTools 是一套直接内置在谷歌 Chrome 浏览器中的网络开发工具。你可以通过在视窗中输入`Ctrl-Shift-I`来找到它。控制台视图将显示打印输出和抖动消息。网络视图将显示图像加载的时间。点击节流并选择快速 3G 测试延迟加载。
4.  有两个屏幕非常有用。然后，您可以在一个屏幕上显示 SPA，在另一个屏幕上显示命令行窗口、Flutter DevTools 和 Chrome DevTools。在 Chrome DevTools 中，单击菜单图标(三个垂直点)，然后移至单独的窗口。
5.  有几种方式来运行 SPA:

*   `flutter run -d chrome`
*   `flutter run -d web-server --debug`
*   `flutter run -d web-server --release`
*   `flutter build web`，然后在(项目目录)/build/web 中提供代码
*   `flutter run`，在连接的(或仿真的)手机上作为 Android 应用程序运行。使用`if (kIsWeb)`来避免 SPA 中 Android 不支持的代码(参见最后的`main.dart`中的两个例子)。
*   还有一个名为`--web-renderer`的选项，您可以选择`auto`、`html`或`canvaskit`。这里，我们使用默认值，即`auto`。你可以在这里阅读。

# 第二步:使用 **scrollable_positioned_list 并添加一个菜单**

`ListView.builder`有一个限制:使用它的滚动控制器，你可以跳到一个偏移量，但是不能跳到一个特定小部件的索引。这在菜单中是需要的。

如果`ListView`中的所有部件都具有相同的高度，这不是问题。你可以很容易地计算出偏移量。或者可以用`PageView.builder`代替`ListView.builder`。

但是如果高度不可预测地变化(就像我们的情况)，你能做什么？以下是一些想法:

1.  给`ListView`中的每个小部件分配一个全局键。然后用`scrollController.position.ensureVisible` ( `globalKeys[index].currentContext.findRenderObject()`，..)进行滚动，直到具有该索引的小部件在视口中可见。这在`ListView,`中运行良好，但是在`ListView.builder`中，它并不总是运行良好，因为对于不在视口或缓存中的窗口小部件，键是空的。然后，您可以在一个循环中执行`scrollcontroller.jumpTo`一小段距离，直到键为非空，然后执行`ensureVisible`。这是可行的，但不是最好的解决方案。
2.  使用 pub.dev 上的`[indexed_list_view](https://pub.dev/packages/indexed_list_view)` 包，它有一些限制。
3.  使用 pub.dev 上的`[scrollable_positioned_list](https://pub.dev/packages/scrollable_positioned_list)` 包。这是我们将要使用的。用这个更新`pubspec.yaml`，做一个扑扑的酒馆 get。

我们现在可以创建一个菜单来滚动到某个品种。

添加`lib/navigationmenu.dart`:

```
import 'package:flutter/material.dart';
import 'package:scrollable_positioned_list/scrollable_positioned_list.dart';
import 'constants.dart';

class NavigationMenu extends StatelessWidget {
  final ItemScrollController itemScrollController;
  int? focusedDogIndex;
  NavigationMenu(this.itemScrollController, {this.focusedDogIndex});

  @override
  Widget build(BuildContext context) {
    return Container(
      color: Colors.white,
      padding: const EdgeInsets.all(3.0),
      child: Column(
        children: [
          for (int i = 0; i < dogBreeds.length; i++) menuRow(i, focusedDogIndex),
        ],
      ),
    );
  }

  Widget menuRow(int index, int? focusedDogIndex) {
    return GestureDetector(
      onTap: () {
        itemScrollController.scrollTo(
            index: index, duration: Duration(seconds: 1), curve: Curves.easeInOut);
      },
      child: Container(
        width: 150,
        height: 50,
        color: Colors.primaries[index.remainder(Colors.primaries.length)],
        child: Center(
            child: Text(
              dogBreeds[index],
              style: TextStyle(color: index == focusedDogIndex ? Colors.white : Colors.black, fontSize: 15),
            )),
      ),
    );
  }
}
```

这个小部件将显示一个如下所示的菜单:

![](img/76a5a09519cf60ee644d0ce524440de8.png)

当点击一行时，滚动控制器的`scrollTo`方法被调用，相应的索引作为参数。(可选参数`focusedDogIndex`在步骤 3 中解释。)

将`main.dart`替换为:

```
import 'package:flutter/material.dart';
import 'package:scrollable_positioned_list/scrollable_positioned_list.dart';
import 'constants.dart';
import 'dogwidget.dart';
import 'navigationmenu.dart';

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  MyApp({Key? key}) : super(key: key);
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: MyHomePage(),
    );
  }
}

class MyHomePage extends StatefulWidget {
  MyHomePage({Key? key}) : super(key: key);
  @override
  State<MyHomePage> createState() => _MyHomePageState();
}

class _MyHomePageState extends State<MyHomePage> {
  final ItemScrollController itemScrollController = ItemScrollController();

  @override
  Widget build(BuildContext context) {
    print('********** Main build');
    return Scaffold(
      appBar: AppBar(
          title: Center(
              child: Text('Dog Breeds Single Page Application',
                  style: TextStyle(color: Colors.white, fontSize: 40)))),
      body: Stack(children: [
        ScrollablePositionedList.builder(
          // cacheExtent: 0,
            itemCount: dogBreeds.length,
            itemScrollController: itemScrollController,
            itemBuilder: (BuildContext context, int index) {
              return Container(
                  color: Colors.primaries[index.remainder(Colors.primaries.length)],
                  child: DogWidget(dogBreeds[index]));
            }),
        NavigationMenu(itemScrollController)
      ]),
    );
  }
}
```

滚动控制器被声明，然后在生成器和菜单中使用。小部件列表被一个`Stack`小部件包裹，菜单被添加。

![](img/6440cf33cd75cf4372e4bd62cd71fec1.png)

命令窗口现在显示如下:

![](img/8253a3734b00c76fed26442e2c9d3c8d.png)

几个品种现在开始预装，而不是两个。这是因为`scrollable_positioned_list`不支持`cacheExtent`参数。

为了使网站更加用户友好，我在`appBar`中添加了一个菜单按钮来关闭和打开菜单。并用一个`Draggable` 小部件包装了菜单。`main.dart`现在看起来是这样的:

```
import 'package:flutter/material.dart';
import 'package:scrollable_positioned_list/scrollable_positioned_list.dart';
import 'constants.dart';
import 'dogwidget.dart';
import 'navigationmenu.dart';

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  MyApp({Key? key}) : super(key: key);
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      debugShowCheckedModeBanner: false,
      home: MyHomePage(),
    );
  }
}

class MyHomePage extends StatefulWidget {
  MyHomePage({Key? key}) : super(key: key);
  @override
  State<MyHomePage> createState() => _MyHomePageState();
}

class _MyHomePageState extends State<MyHomePage> {
  final ItemScrollController itemScrollController = ItemScrollController();
  bool menuVisible = false;
  Offset menuPosition = Offset(20, 20);

  @override
  Widget build(BuildContext context) {
    print('********** Main build');
    return Scaffold(
      appBar: AppBar(
        title: Center(
            child: Text('Dog Breeds Single Page Application',
                style: TextStyle(color: Colors.white, fontSize: 40))),
        actions: [
          IconButton(
            icon: Icon(Icons.menu),
            onPressed: () {
              menuVisible = !menuVisible;
              setState(() {});
            },
          )
        ],
      ),
      body: Stack(children: [
        ScrollablePositionedList.builder(
          // cacheExtent: 0,
            itemCount: dogBreeds.length,
            itemScrollController: itemScrollController,
            itemBuilder: (BuildContext context, int index) {
              return Container(
                  color: Colors.primaries[index.remainder(Colors.primaries.length)],
                  child: DogWidget(dogBreeds[index]));
            }),
        menuVisible
            ? Positioned(
          left: menuPosition.dx,
          top: menuPosition.dy,
          child: Draggable(
            feedback: NavigationMenu(itemScrollController),
            childWhenDragging: Opacity(opacity: .3, child: NavigationMenu(itemScrollController)),
            onDragEnd: (details) {
              menuPosition = details.offset;
              setState(() {});
            },
            child: NavigationMenu(
              itemScrollController,
            ),
          ),
        )
            : Container()
      ]),
    );
  }
}
```

现在，如果你愿意，菜单可以被关闭和打开，并被拖动到一个新的位置。`debugShowCheckedModeBanner: false`行删除了调试横幅，否则它会隐藏菜单按钮。

进行热重启并测试新版本。

# 步骤 3:处理 URL

如果 SPA 有很多小部件，用户可能希望能够标记一个直接指向所需小部件的 URL，而不必滚动到它。当然，这对于一个菜单来说可能不是很重要，但是拥有它很好，也很容易修复。

我们将支持类似`hostname/?breed=labrador`的 URL。更新 URL 只需一行代码:

```
html.window.history.pushState(null, '', '?breed=${dogBreeds[focusedDogIndex]}');
```

问题是要知道用户在 SPA 中向下滚动了多远。`scrollable_positioned_list`中的`itemPositionsListener`特性会处理好这一点。`ScrollablePositionedList.builder`向任何想听的人发送一串位置。在滚动的过程中，听众会每隔一段时间得到一组`ItemPosition`。在视窗中全部或部分可见的每个小部件都有一个`ItemPosition`，它包含以下内容:

*   小部件的索引(例如，`2`代表斑点狗)
*   `itemLeadingEdge` =从视窗前缘到小工具前缘的距离，与视窗主轴长度成比例。如果项目是部分可见的，它可能是负的。例如，`0.5`表示小部件的顶部在视口的中间位置。
*   `itemTrailingEdge` =与视窗主轴长度成比例的距离，从微件的前沿到后沿。如果项目是部分可见的，则它可能大于 1。

要接收这个流，用一个`ValueListenableBuilder`小部件包装菜单:

```
final ItemScrollController itemScrollController = ItemScrollController();
final ItemPositionsListener itemPositionsListener = ItemPositionsListener.create();
  :
body: Stack(children: [
        ScrollablePositionedList.builder(
          // cacheExtent: 0,
            itemCount: dogBreeds.length,
            itemScrollController: itemScrollController,
            itemPositionsListener: itemPositionsListener,
            itemBuilder: (BuildContext context, int index) {
              return Container(
                  color: Colors.primaries[index.remainder(Colors.primaries.length)],
                  child: DogWidget(dogBreeds[index]));
            }),
        ValueListenableBuilder<Iterable<ItemPosition>>(
            valueListenable: itemPositionsListener.itemPositions,
            builder: (context, positions, child) {
              if (positions.isEmpty) {
                return Container();
              }
              int focusedDogIndex = findFocusedDog(positions);
              html.window.history.pushState(null, '', '?breed=${dogBreeds[focusedDogIndex]}');
              return menuVisible
                  ? Positioned(
                left: menuPosition.dx,
                top: menuPosition.dy,
                child: Draggable(
                  feedback: NavigationMenu(itemScrollController),
                  childWhenDragging: Opacity(opacity: .3, child: NavigationMenu(itemScrollController)),
                  onDragEnd: (details) {
                    menuPosition = details.offset;
                    setState(() {});
                  },
                  child: NavigationMenu(itemScrollController, focusedDogIndex: focusedDogIndex),
                ),
              )
                  : Container();
            })
      ]),
```

`findFocusedDog`功能旨在当视窗中有多个品种时，找出用户最可能关注的品种。例如，在这些情况下，

![](img/494d0ec68015e669f726e3061de3faa2.png)

该函数应该为左侧图像返回`7 = labradoodle`，为右侧图像返回`8 = labrador`。

返回值用于执行以下操作:

*   更新网址— `html.window.history.pushState(null, ‘’, ‘?breed=${dogBreeds[focusedDogIndex]}`
*   在`NavigationMenu`调用中，这将使菜单中相应的行高亮显示。

用以下代码添加`lib/findfocuseddog.dart`:

```
import 'package:scrollable_positioned_list/scrollable_positioned_list.dart';

int findFocusedDog(Iterable<ItemPosition> positions) {
  ItemPosition topPos = positions.where((ItemPosition position) => position.itemTrailingEdge > 0).reduce(
          (ItemPosition min, ItemPosition position) =>
      position.itemTrailingEdge < min.itemTrailingEdge ? position : min);
  ItemPosition botPos = positions.where((ItemPosition position) => position.itemLeadingEdge < 1).reduce(
          (ItemPosition max, ItemPosition position) =>
      position.itemLeadingEdge > max.itemLeadingEdge ? position : max);
  if (topPos.itemLeadingEdge == 0) {
    // If a widget is aligned with the top of the viewport, use it
    return topPos.index;
  } else if (positions.length == 1) {
    // If a widget covers the whole viewport, use it
    return topPos.index;
  } else if (positions.length == 2) {
    // If there are two widgets in the viewport,
    // select the one which uses most of the viewport
    if (topPos.itemTrailingEdge < 1 - botPos.itemLeadingEdge)
      return topPos.index + 1;
    else
      return topPos.index;
  } else if (positions.length == 3) {
    // If there are three widgets in the viewport, select the one in the middle
    Iterable otherPositions = positions.where((ItemPosition position) => position != topPos && position != botPos);
    return otherPositions.first.index;
  } else
    return topPos.index;
}
```

iterable 中的`ItemPositions`可以是任意顺序的——位置`positions.first`不一定代表最顶端的小部件。这使得代码有点复杂。(我怀疑可能有更简单的解决方案。有想法就回应。)

现在，URL 会随着您的滚动而动态更新。如果你去一个网址。品种=xyz，您希望 SPA 直接跳转到查询品种。为此，将品种作为参数添加到`MyHomePage`:

```
class MyApp extends StatelessWidget {
  MyApp({Key? key}) : super(key: key);
  @override
  Widget build(BuildContext context) {
    String queryBreed;
    String query = Uri.base.query;
    if (query.length > 6) {
      queryBreed = query.substring(6);
      if (dogBreeds.indexOf(queryBreed) == -1) queryBreed = dogBreeds[0];
    } else {
      queryBreed = dogBreeds[0];
    }
    print('building for breed $queryBreed');
    return MaterialApp(
      debugShowCheckedModeBanner: false,
      home: MyHomePage(queryBreed: queryBreed),
    );
  }
}

class MyHomePage extends StatefulWidget {
  String queryBreed;
  MyHomePage({Key? key, required this.queryBreed}) : super(key: key);
  @override
  State<MyHomePage> createState() => _MyHomePageState();
}
```

然后添加以下`initState`覆盖:

```
@override
  void initState() {
    SchedulerBinding.instance.addPostFrameCallback((_) {
      itemScrollController.jumpTo(index: dogBreeds.indexOf(widget.queryBreed));
    });
    super.initState();
  }
```

`addPostFrameCallback`确保跳转被推迟到第一次构建完成之后。

完整的最终`main.dart`看起来是这样的:

```
import 'package:flutter/foundation.dart';
import 'package:flutter/material.dart';
import 'package:flutter/scheduler.dart';
import 'dart:html' as html if (kIsWeb) "";
import 'dart:ui';
import 'package:scrollable_positioned_list/scrollable_positioned_list.dart';
import 'constants.dart';
import 'dogwidget.dart';
import 'navigationmenu.dart';
import 'findfocuseddog.dart';

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  MyApp({Key? key}) : super(key: key);
  @override
  Widget build(BuildContext context) {
    String queryBreed;
    String query = Uri.base.query;
    if (query.length > 6) {
      queryBreed = query.substring(6);
      if (dogBreeds.indexOf(queryBreed) == -1) queryBreed = dogBreeds[0];
    } else {
      queryBreed = dogBreeds[0];
    }
    print('building for breed $queryBreed');
    return MaterialApp(
      debugShowCheckedModeBanner: false,
      home: MyHomePage(queryBreed: queryBreed),
    );
  }
}

class MyHomePage extends StatefulWidget {
  String queryBreed;
  MyHomePage({Key? key, required this.queryBreed}) : super(key: key);
  @override
  State<MyHomePage> createState() => _MyHomePageState();
}

class _MyHomePageState extends State<MyHomePage> {
  final ItemScrollController itemScrollController = ItemScrollController();
  final ItemPositionsListener itemPositionsListener = ItemPositionsListener.create();
  bool menuVisible = false;
  Offset menuPosition = Offset(20, 20);

  @override
  void initState() {
    SchedulerBinding.instance.addPostFrameCallback((_) {
      itemScrollController.jumpTo(index: dogBreeds.indexOf(widget.queryBreed));
    });
    super.initState();
  }

  @override
  Widget build(BuildContext context) {
    print('********** Main build');
    return Scaffold(
      appBar: AppBar(
        title: Center(
            child: Text('Dog Breeds Single Page Application',
                style: TextStyle(color: Colors.white, fontSize: 40))),
        actions: [
          IconButton(
            icon: Icon(Icons.menu),
            onPressed: () {
              menuVisible = !menuVisible;
              setState(() {});
            },
          )
        ],
      ),
      body: Stack(children: [
        ScrollConfiguration(
          behavior: ScrollConfiguration.of(context).copyWith(dragDevices: {
            PointerDeviceKind.touch,
            PointerDeviceKind.mouse,
          }),
          child: ScrollablePositionedList.builder(
            // cacheExtent: 0,
              itemCount: dogBreeds.length,
              itemScrollController: itemScrollController,
              itemPositionsListener: itemPositionsListener,
              itemBuilder: (BuildContext context, int index) {
                return Container(
                    color: Colors.primaries[index.remainder(Colors.primaries.length)],
                    child: DogWidget(dogBreeds[index]));
              }),
        ),
        ValueListenableBuilder<Iterable<ItemPosition>>(
            valueListenable: itemPositionsListener.itemPositions,
            builder: (context, positions, child) {
              if (positions.isEmpty) {
                return Container();
              }
              int focusedDogIndex = findFocusedDog(positions);
              if (kIsWeb) html.window.history.pushState(null, '', '?breed=${dogBreeds[focusedDogIndex]}');
              return menuVisible
                  ? Positioned(
                left: menuPosition.dx,
                top: menuPosition.dy,
                child: Draggable(
                  feedback: NavigationMenu(itemScrollController),
                  childWhenDragging: Opacity(opacity: .3, child: NavigationMenu(itemScrollController)),
                  onDragEnd: (details) {
                    menuPosition = details.offset;
                    setState(() {});
                  },
                  child: NavigationMenu(itemScrollController, focusedDogIndex: focusedDogIndex),
                ),
              )
                  : Container();
            })
      ]),
    );
  }
}
```

# 第五步:为生产做准备

法图标。favicon 是一个 16x16 像素的小图标，显示在 Chrome 的标签和书签中。我用颜料做了这个:

![](img/7c5b2217c8bf553a47410476f0749087.png)

用你自己的图标替换`web\favicon.png`。

**清单和 Chrome 图标**。更新文件`web\manifest.json`。更改字段`name`、`short_name`和`description`。另外，用你自己的图标替换`web\icons\icon-192.png`。我使用了与 splash 图像相同的图像，如下所示，并将其大小调整为 192x192。如果您选择通过单击 Chrome 地址栏中三个图标中最左边的一个来将 SPA 安装为 Chrome 应用程序，将会使用该名称和图标:

![](img/4d512c34e4e186843db9e387a674f090.png)

**闪屏**。这是在启动 SPA、等待 Flutter 加载以及显示第一个应用程序屏幕时显示的。它看起来是这样的(但背景是蓝色的):

![](img/f9a1930a8b96bc3d5e4de91f60278ca7.png)![](img/65dbc3d13f0f303088aab805a67f003e.png)

将图像和 gif 保存在`\web\icons`中。将以下两行添加到`web\index.html`，就在主体之后:

```
<img src="icons/splash.png" class="center" />
<img src="icons/wait1.gif" width="100" height="100"  class="below" />
```

然后添加以下文件作为`web\styles.css`:

```
html,
body {
    background-color: #368ed6
}
.center {
    margin: 0;
    position: absolute;
    top: 30%;
    left: 50%;
    margin-right: -50%;
    transform: translate(-50%, -50%)
}
.below {
    margin: 0;
    position: absolute;
    top: 70%;
    left: 50%;
    margin-right: -50%;
    transform: translate(-50%, -50%)
}
```

最后，将这一行添加到`web\index.html`，就在清单链接的后面:

```
 <link rel="stylesheet" type="text/css" href="styles.css">
```

建造并主持水疗中心。您现在已经准备好进行最后的构建了。输入这个命令:`flutter build web`。将创建一个`build`目录，其中包含部署到服务器所需的所有文件。`web`目录中的图标和其他文件将被复制到`build\web`。

对于主机，我推荐 Firebase 主机。它很容易使用。遵循这里的说明。在创建 Firebase 项目时，确保将默认的项目 id 更改为更加用户友好的名称，因为它将是 SPA 的 URL 的一部分:`(project_id).web.app`。在`firebase init hosting`期间，

*   输入 build/web 作为您的公共目录
*   回答是配置为单页 app？
*   对文件 build/web/index.html 的回答是否定的，因为它已经存在。覆盖？

您现在可以将您的 SPA 部署到服务器:firebase deploy。

**添加 Firebase Analytics** 。这是可选的，但如果您想了解 SPA 的使用方式和位置，建议您这样做。而且实现起来也不难。按照这里的指示[。](https://firebase.google.com/docs/analytics/get-started?platform=web)

要找到要添加到`index.html`的代码，请转到 Firebase 控制台，选择您的项目，单击项目概述设置齿轮，向下滚动到您的应用程序，选择 CDN 配置并复制代码:

```
<script type="module">
  // Import the functions you need from the SDKs you need
  import { initializeApp } from "https://www.gstatic.com/firebasejs/9.14.0/firebase-app.js";
  import { getAnalytics } from "https://www.gstatic.com/firebasejs/9.14.0/firebase-analytics.js";
  // TODO: Add SDKs for Firebase products that you want to use
  // https://firebase.google.com/docs/web/setup#available-libraries

  // Your web app's Firebase configuration
  // For Firebase JS SDK v7.20.0 and later, measurementId is optional
  const firebaseConfig = {
    apiKey: "AIzaSyBfFajK2I2UR2xtXQXBqPj31lLLmfsIWEY",
    authDomain: "dog-spa.firebaseapp.com",
    projectId: "dog-spa",
    storageBucket: "dog-spa.appspot.com",
    messagingSenderId: "913865773590",
    appId: "1:913865773590:web:6b89da844ba439aa409b37",
    measurementId: "G-76XBK247DD"
  };

  // Initialize Firebase
  const app = initializeApp(firebaseConfig);
  const analytics = getAnalytics(app);
</script>
```

将这段代码添加到 web/index.html 中，就在闪屏的 img 语句之后。然后进行新的 firebase 构建和 firebase 部署。

(由于我们使用 Firebase 托管，我们可以通过从[保留的 URL](https://firebase.google.com/docs/hosting/reserved-urls)加载 Firebase SDKs 来稍微提高性能。我没试过。)

要测试分析是否有效，请在几台设备上运行 SPA。然后转到 Firebase 控制台，选择分析—实时。在“过去 30 分钟内的用户”中，您应该看到用于测试的设备数量。

为了防止滥用 apiKey，任何人都可以使用 Chrome Inspector 显示 API key，您应该限制它在您的 SPA 中使用。这是在[这里](https://console.cloud.google.com/apis/credentials)完成的。

# 摘要

感谢您阅读本教程。如果您已经学习了它，那么您已经了解了如何使用 Flutter Web 构建一个具有延迟加载、URL 更新和菜单的单页面应用程序网站，以及如何使用 Firebase 托管和分析来部署它。如果你觉得它有用，请为它鼓掌。

对于一个用这个代码制作的网站的实例，去我的主页[https://ndssoft.se/](https://ndssoft.se/)。