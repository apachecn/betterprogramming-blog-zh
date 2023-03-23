# 如何在 Flutter 中实现自动完成特性

> 原文：<https://betterprogramming.pub/how-to-implement-an-autocomplete-feature-in-flutter-bea21d6b1847>

## 轻松集成 Datamuse API 和 Flutter

![](img/54ffd57c693222f3bfbae3e2c5cd266d.png)

由[罗曼五世](https://unsplash.com/@lebrvn?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

自动完成是一种方便的功能，可以预测用户希望在文本字段中输入的单词。他们只需输入前几个字符，程序就会提示可能的单词。

在本教程中，我将向您介绍 [Datamuse](https://www.datamuse.com/api/) API，一个易于使用的单词查找查询引擎。它最大的特点之一是自动完成。当搜索词的词汇量非常大时，您可以将其应用到您的网站或应用程序中。

在下一节中，我将向您展示如何将 Datamuse API 与 [Flutter](https://flutter.dev/) 集成，这是一个用于构建优秀跨平台应用程序的现代工具包。

让我们开始吧！

# Datamuse 概述

Datamuse 提供了一个简单的 API 来查找押韵单词、拼写相似的单词、描述性形容词等等。它的查询引擎由像 [CMU 发音词典](http://www.speech.cs.cmu.edu/cgi-bin/cmudict)、 [WordNet 3.0](http://wordnet.princeton.edu/) 和 [word2vec](https://code.google.com/archive/p/word2vec/) 这样的数据源提供支持。该 API 可以免费使用，但每天的请求数量限制在 100，000 个以内。有一个选择是获得更大的配额。

让我们来看几个例子。

## 找到与“吉他”押韵并且与音乐相关的单词

在你的浏览器中打开这个网址:[https://api.datamuse.com/words?**ml =音乐**rel _**rhy =吉他**max = 1000](https://api.datamuse.com/words?ml=music&rel_rhy=guitar&max=1000)。

结果:

```
[{"word":"bar","score":6002,"numSyllables":1},{"word":"repertoire","score":1531,"numSyllables":3},{"word":"sitar","score":408,"numSyllables":2}]
```

注意，`ml`代表*的意思就像* 和`rhy` 代表代表*押韵*。

## 在以字母“s”开头的句子中，找出经常跟在“write”后面的单词

打开这个网址:[https://api.datamuse.com/words?**LC = write**&**sp = s ***](https://api.datamuse.com/words?lc=write&sp=s*)。

结果:

```
[{"word":"something","score":5214},{"word":"some","score":4115},{"word":"such","score":4023},{"word":"so","score":3277},{"word":"stories","score":2260},{"word":"short","score":2100},{"word":"sentences","score":1996},{"word":"soon","score":1928}]
```

这里，`lc`表示*左上下文*，而`sp`代表*的拼写与*相似。也可以使用通配符，比如`*`符号。

你可以在 API 的[网站](https://www.datamuse.com/api/)上探索完整的例子集合。

在本文中，我们对基于用户输入返回单词建议的`/sug`端点感兴趣。[文档](https://pub.dev/documentation/datamuse/latest/datamuse/Datamuse-class.html)描述了幕后发生的事情:

> “当无法找到精确的前缀匹配时，这些建议会执行实时拼写纠正，并智能地退回到语音或语义相似的选项。”

让我们看看对`flo`有什么建议。

打开这个网址:[https://api.datamuse.com/**sug？**s = flo](https://api.datamuse.com/sug?s=flo)。

结果:

```
[{"word":"flower","score":3280},{"word":"flow","score":3075},{"word":"float","score":2159},{"word":"floor","score":1934},{"word":"flourish","score":1875},{"word":"flock","score":1687},{"word":"flood","score":1511},{"word":"flora","score":1505},{"word":"flop","score":1407},{"word":"floop","score":1322}]
```

对了，我脑子里有过*花* 这个词，而且好像得分最高。

# Datamuse API 与 Flutter 的集成

我们将使用外部 Flutter 库来简化与 Datamuse API 的集成过程。

TypeAhead 是一个很棒的用于 Flutter 的自动完成部件。当用户在文本字段中键入时，您可以使用它来显示建议。该包在其 GitHub [库](https://github.com/AbdulRahmanAlHamali/flutter_typeahead/tree/master/example)中有实现示例。为了本教程，我简化了其中一个例子。

## 准备项目

1.  在您最喜欢的 IDE 中创建一个新的 Flutter 项目。
2.  通过更新`pubspec.yaml`文件中的`dependencies`来安装必要的库:

```
http: ^0.13.3
flutter_typeahead: ^3.1.3
```

在您的终端中运行以下命令来触发更新:

```
flutter pub get
```

3.用我们的替换自动生成的`main.dart`文件的内容:

```
import 'package:flutter/material.dart';
import 'material_app.dart';

void main() => runApp(MyMaterialApp());
```

4.使用以下代码创建一个名为`material-app.dart`的新 Dart 文件:

我们已经导入了必要的库。注意，您会得到一个错误消息，说`data.dart`文件丢失了，但是不要担心。我们将很快创建它。

*   这里要注意的主要是`TypeAheadField`。您可以随意定制输入字段的样式。
*   `suggestionCallback`方法调用我们的 Datamuse API 后端。
*   `itemBuilder`方法在一个`ListTile`小部件中显示结果。
*   我将`onSuggestionSelected`方法留空。您可以添加当用户选择一个建议时应该发生什么的实现。

5.创建丢失的`data.dart`文件:

*   我们通过一个简单的 REST 调用来调用 Datamuse API 的`/sug`端点。
*   我们接收 JSON 数据并将其映射到一个`Suggestion`对象。

注意，使用[改进](https://pub.dev/packages/retrofit)库实现 REST 调用有一个更有利的方法。Hetashree Bharadwaj 写了一个关于如何使用这个库的综合教程[。主要的好处是它支持 JSON 数据的自动序列化。这样，我们不需要手动映射我们的类。](https://medium.com/mindful-engineering/retrofit-the-easiest-way-to-call-rest-apis-is-flutter-fe55d1e7c5c2)

## 项目演示

最后，让我们通过执行以下命令来运行项目:

```
flutter run -d chrome - web
```

![](img/0dd7e97a57f4391d0ab565f8185542be.png)

颤振自动完成演示

太好了！程序根据第一个字符给出建议。

# 结论

在本文中，您了解了 Datamuse API 的优点以及如何将其与 Flutter 集成。您还了解了如何使用 TypeAhead Flutter 库来轻松实现自动完成。

你可以在下面链接的我的 GitHub 资源库中找到这个项目的代码。

希望这篇教程对你有用。如果你喜欢颤振主题，你可能也会喜欢我的其他相关文章:

[](/create-a-simple-admin-ui-with-flutter-and-etcd-1d00c788014d) [## 用 Flutter 和 etcd 创建一个简单的管理用户界面

### 处理来自 etcd 后端的数据，并在 Flutter 中显示数据

better 编程. pub](/create-a-simple-admin-ui-with-flutter-and-etcd-1d00c788014d) [](/how-to-implement-a-navigation-drawer-in-flutter-8d97d3b599d4) [## 如何在 Flutter 中实现一个导航抽屉

### 在 Flutter 中创建一个简单的抽屉，并在菜单项之间导航

better 编程. pub](/how-to-implement-a-navigation-drawer-in-flutter-8d97d3b599d4) [](/how-to-create-flutter-charts-on-android-5a75b5fc337c) [## 如何在 Android 上创建颤振图表

### 在抖动图表中显示来自 Android 后端的数据

better 编程. pub](/how-to-create-flutter-charts-on-android-5a75b5fc337c) 

感谢您的阅读和快乐编码！

# 参考

*   [https://github . com/kirshiyin 89/flutter _ autocomplete/tree/master](https://github.com/kirshiyin89/flutter_autocomplete/tree/master)
*   [https://github.com/AbdulRahmanAlHamali/flutter_typeahead](https://github.com/AbdulRahmanAlHamali/flutter_typeahead)
*   [https://pub.dev/packages/flutter_typeahead](https://pub.dev/packages/flutter_typeahead)
*   [https://pub.dev/packages/http/install](https://pub.dev/packages/http/install)
*   【https://www.datamuse.com/api/ 号