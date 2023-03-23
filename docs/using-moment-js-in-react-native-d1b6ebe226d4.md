# 在 React Native 中使用力矩 JS

> 原文：<https://betterprogramming.pub/using-moment-js-in-react-native-d1b6ebe226d4>

## Moment.js 是计算日期和时间的更好方法

![](img/77d3cc5b843f2b28ce35738227950e72.png)

如果您正在 React Native 或任何其他 JavaScript 框架中工作，那么您一定曾经遇到过按照需求以格式化的方式使用时间或日期。通常，在 React Native 的情况下，我们使用 JSON 或 JavaScript Date 函数获取这些数据。但是在使用它时，我们得到了一个奇怪的字符串，它是日期和时间以及时区的组合，格式奇怪。这里有一个专门为此建造的图书馆。

# 介绍

该库旨在帮助开发人员学会更好地与日期和时间问题域进行交互。它解决了我们在这里最常见的日期和时间请求，所以它是一个很好的地方来检查任何问题的解决方案。

## 安装:如何安装 Moment.js

只需在您的终端中运行以下命令，就大功告成了；不需要运行任何其他链接或命令来将其链接到您的项目。本教程针对 React Native，因此您可以按如下方式安装 npm 命令:

```
npm install moment --save 
```

## JavaScript 日期

如果对日期使用 JavaScript 函数，Moment.js 会为原生 JavaScript 日期对象提供一个包装器。通过这样做，Moment.js 扩展了功能，也解决了对象中的几个缺陷。

对于原生日期，解析显然是不可预测的。例如，假设我在美国使用一台计算机，但是我有一个日/月/年格式的日期。

```
var a = new Date('01/12/2016'); //December 1 2016 in DD/MM/YYYY format
//"Tue Jan 12 2016 00:00:00 GMT-0600 (Central Standard Time)"
```

对于本机日期对象的这种行为，没有好的解决方法。然而，Moment 的解析器处理得很好:

```
moment('01/12/2016', 'DD/MM/YYYY', true).format()
"2016-12-01T00:00:00-06:00"
```

如果您不理解这些格式，可以查看下面的代码片段以获得更好的想法。您还可以进入浏览器控制台，检查所有这些命令的正确输入和格式。您可以使用的一些流行格式如下。

## 日期

```
moment().format('MMMM Do YYYY, h:mm:ss a'); // December 13th 2018, 5:25:14 pm
moment().format('dddd');                    // Thursday
moment().format("MMM Do YY");               // Dec 13th 18
moment().format('YYYY [escaped] YYYY');     // 2018 escaped 2018
```

## 时间

```
moment("20111031", "YYYYMMDD").fromNow(); // 7 years ago
moment("20120620", "YYYYMMDD").fromNow(); // 6 years ago
moment().startOf('day').fromNow();        // 17 hours ago
moment().endOf('day').fromNow();          // in 7 hours
moment().startOf('hour').fromNow();       // 26 minutes ago
```

## 日历

```
moment().subtract(10, 'days').calendar(); // 12/03/2018
moment().subtract(6, 'days').calendar();  // Last Friday at 5:25 PM
moment().subtract(3, 'days').calendar();  // Last Monday at 5:25 PM
moment().subtract(1, 'days').calendar();  // Yesterday at 5:25 PM
moment().calendar();                      // Today at 5:25 PM
moment().add(1, 'days').calendar();       // Tomorrow at 5:25 PM
moment().add(3, 'days').calendar();       // Sunday at 5:25 PM
moment().add(10, 'days').calendar();      // 12/23/2018
```

# 将 Moment.js 与 React Native 集成

在 React Native 中导入矩库非常容易。只需将库导入到需要格式化日期或时间的文件中。

```
import moment from "moment";
```

现在，您的标题必须看起来像这样:

在 Header 部分调用了库之后，就该格式化日期并将其集成到代码中了。在文件的构造函数方法中创建一个状态，并以您需要的方式在代码中呈现它。

以下是我的整个代码片段，你可以看看我们如何在我们的代码中使用它。

![](img/a3469e225103dc306506b4f9323cc95c.png)

这就是现在屏幕应该的样子。我只在屏幕上使用了当天和日期。下面是代码:

```
const today = this.state.currentDate;
const day = moment(today).format(“dddd”);
const date = moment(today).format(“MMMM D, YYYY”);
```

请尝试其他格式，继续学习！你可以在这个 GitHub 库的[了解更多关于 Moment 的信息。](https://github.com/moment/momentjs.com)

# 两个日期之间的差异

通常，我们会遇到倒计时或剩余天数的需求，尤其是当我们构建基于考试、旅行计划、日历、预订等的实时应用程序时。我们也可以很容易地用力矩来表示。

要获得两个日期之间的差异，可以使用下面的代码。默认情况下，或者从应用程序屏幕中的日期选择器初始化任何特定日期的状态。