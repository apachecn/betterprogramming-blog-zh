# 如何从一个文件中导出多个 Dart 文件

> 原文：<https://betterprogramming.pub/how-to-export-multiple-dart-files-from-just-one-b2f2964029e>

## 借助一些水果！

![](img/0efd463c44f22c7b84845899940e85d1.png)

[菠萝供应公司](https://unsplash.com/@pineapple?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/tech-fruit?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

我最近正在为我们的 Flutter SDK 构建各种类，这时我意识到我正在创建多少个`.dart`文件。其中大多数是单独的班级。有些包含多个类，但大多数情况下每个类都属于自己的文件。

当我开始在小部件中使用这些类时，我意识到我正在分别导入多个`.dart`文件。一个小部件几乎使用了十个类！那就是*十*单独进口。我想找到一种更简洁的方法，从一个文件中一次性导入所有这些类。

我首先想到的是用一个文件包含我所有的类。从技术上来说这是可行的，但是这样做通常是不好的，因为以后很难找到你的课程。这对源代码控制也有好处，因为如果你改变了一个文件，你会知道你已经改变了属于那个文件的类。

那现在怎么办？

嗯，我做了一些调查，在[堆栈溢出](https://stackoverflow.com/questions/55835593/how-to-import-all-dart-files-from-a-directory)上找到了一个答案，这正是我所需要的。

实际上，您创建了另一个`.dart`文件，它获取并导出您包含在其中的任何文件。假设您在一个`fruit`目录中有以下文件。

```
fruit
|__ apple.dart
|__ banana.dart
|__ grape.dart
|__ orange.dart
|__ pear.dart
|__ pineapple.dart
|__ rambutan.dart
|__ raspberry.dart
|__ strawberry.dart
```

> 通过创建一个[中型合作伙伴计划账户](https://matt-croak.medium.com/membership)和[订阅我的电子邮件](https://matt-croak.medium.com/subscribe)，获取我所有的最新内容。:)

你有一个叫做`FruitSalad`的小部件，它在某种程度上使用了`fruit`目录中的所有类。如果您单独导入每个文件，您将进行 9 次单独的导入。

```
import './fruit/apple.dart';
import './fruit/banana.dart';
import './fruit/grape.dart';
...class FruitSalad extends StatelessWidget {
  ...
}
```

相反，您可以做的是在您的`fruit`目录中创建一个`index.dart`文件。在这个文件中，您可以导出所有其他文件！见下面我们的`index.dart`文件。

```
export './fruit/apple.dart';
export './fruit/banana.dart';
export './fruit/grape.dart';
...
```

一旦你有了你需要的所有类，我们就可以将`fruits/index.dart`导入到我们的`FruitSalad`小部件中。

```
import './fruit/index.dart';class FruitSalad extends StatelessWidget {
  ...
}
```

就是这样！通过从`./fruits/index.dart`导出所有其他文件，我们现在可以访问我们的`fruits`目录中的每个类，而不必导入每个单独的类。这很好，因为如果我们创建另一个小部件，姑且称之为`FruitBasket`，我们不需要从`FruitSalad`复制/粘贴 9 个导入。相反，我们可以复制粘贴从`./fruits/index.dart`导入的那个，然后访问我们小小的心灵想要的所有水果(类)!

[***升级您的免费 Medium 会员资格***](https://matt-croak.medium.com/membership) *并接收各种出版物上数千名作家的无限量、无广告的故事。这是一个附属链接，你的会员资格的一部分帮助我为我创造的内容获得奖励。*

*您也可以通过电子邮件* [***订阅***](https://matt-croak.medium.com/subscribe) *，每当我发布新内容时，您都会收到通知！*

谢谢你！

# 参考

[](https://stackoverflow.com/questions/360643/is-it-a-bad-practice-to-have-multiple-classes-in-the-same-file) [## 在同一个文件中有多个类是一种不好的做法吗？

### 我曾经一个文件一个类。例如，car.cs 拥有 car 类。但是当我编写更多的类时，我会…

stackoverflow.com](https://stackoverflow.com/questions/360643/is-it-a-bad-practice-to-have-multiple-classes-in-the-same-file) [](https://stackoverflow.com/questions/55835593/how-to-import-all-dart-files-from-a-directory) [## 如何从一个目录中导入所有的 Dart 文件？

### 我是 dart 新手，所以如果这个问题很糟糕，请耐心等待。我正在使用 flutter 开发一个应用程序，我…

stackoverflow.com](https://stackoverflow.com/questions/55835593/how-to-import-all-dart-files-from-a-directory)