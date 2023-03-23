# 如何在 JavaScript 中生成月、年或日的数组

> 原文：<https://betterprogramming.pub/how-to-generate-an-array-of-months-years-or-days-in-javascript-add4b8419ff9>

## 使用世界上最好的数据库

![](img/5858e527b76d4d578a8b66c30d9fc418.png)

由 [Chad Kirchoff](https://unsplash.com/@cakirchoff?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

做事有容易的方法，做事有难的方法。我是那种喜欢简单方法的人。通常，较少的代码对我来说意味着这是最简单的方法。

如果你是第一次接触 [moment.js，那么我推荐你复习一下](https://momentjs.com/docs)。此外，您需要将 moment.js 安装或导入到您的工作空间中。

我们将利用 moment.js 给我们的两个简单函数。那就是`.add()`和`.diff()`函数。

这里有一些关于如何使用它们的快速文档。

# 。添加()

```
moment().add(Number, String);
moment().add(Duration);
moment().add(Object);
```

该函数接受三种不同的参数格式。

## 数字和字符串

数字指定单位的数量，字符串表示单位的类型。

## 持续时间

`Duration`需要一个持续时间的对象。

## 目标

格式为{ 'unit': number }的对象。您可以在这里指定多个单位。

## 如何使用它们的示例:

```
moment().add(7, 'days’);
moment().add({ days:7, months:1 }); // with object literalConst duration = moment.duration({'days' : 1});
moment().add(duration);
```

# 。差异()

```
moment().diff(Moment|String|Number|Date|Array);
moment().diff(Moment|String|Number|Date|Array, String);
moment().diff(Moment|String|Number|Date|Array, String, Boolean);
```

该函数接受三个不同的参数，其中两个是可选的。

## 第一个参数

可以是下列之一:`Moment`、`String`、`Number`、`Date`、`Array`

## 第二个参数

一个字符串值，表示要进行比较的值。

## 第三个参数

指定是否允许将`floating-point`值作为返回值(否则将舍入到最接近的整数)。

## 如何使用它们的示例:

```
Const a = moment().add(2, ‘year’);
Const b = moment();
a.diff(b, 'years’); // returns 2Const a = moment();
Const b = moment().add(1, 'seconds');
a.diff(b) // -1000
b.diff(a) // 1000
```

# 让我们使用它们

## 生成年份数组:

```
const YEARS = () => {
 const years = []
 const dateStart = moment()
 const dateEnd = moment().add(10, 'y') while (dateEnd.diff(dateStart, 'years') >= 0) {
   years.push(dateStart.format('YYYY'))
   dateStart.add(1, 'year')
 }
 return years
}console.log(YEARS())
```

## 生成月份数组:

```
const MONTHS = () => {
 const months = []
 const dateStart = moment()
 const dateEnd = moment().add(12, ‘month') while (dateEnd.diff(dateStart, ‘months') >= 0) {
  months.push(dateStart.format(‘M'))
  dateStart.add(1, ‘month')
 }
 return months
}console.log(MONTHS())
```

## 生成日期数组:

```
const DAYS = () => {
 const days = []
 const dateStart = moment()
 const dateEnd = moment().add(30, ‘days') while (dateEnd.diff(dateStart, ‘days') >= 0) {
  days.push(dateStart.format(‘D'))
  dateStart.add(1, ‘days')
 }
 return days
}console.log(DAYS())
```

# 结论

我希望这些信息对您有所帮助。Moment.js 是一个非常强大的库，还有很多其他很酷的功能。

干杯！