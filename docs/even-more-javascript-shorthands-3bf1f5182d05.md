# 甚至更多的 JavaScript 速记

> 原文：<https://betterprogramming.pub/even-more-javascript-shorthands-3bf1f5182d05>

## 句法糖，以改善你的流程

![](img/3b29cfe97f2da08719d3a4ebf614868c.png)

[丘特尔斯纳普](https://unsplash.com/@chuttersnap?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

在 JavaScript 的最新版本中，这种语言引入了更多的语法糖。在这篇文章中，我们将看看在新版本和旧版本的 JavaScript 中容易阅读的快捷方式。有了它们，可以节省我们的时间，让我们的代码更容易阅读。

在本文中，我们将研究对象属性赋值，寻找函数的索引，用对象替换 switch 语句，通过压缩`if`语句合并对象，等等。

# 对象属性分配

我们可以使用`Object.assign`方法将一个对象从一个新对象浅层复制到另一个对象。

例如，如果我们有以下对象…

```
const foo = {
  foo: 1
}
```

…我们可以如下克隆它:

```
const copied = Object.assign({}, foo);
```

克隆将复制其自身属性的结构，并将阻止对原始对象的修改。

这意味着修改`copied`不会修改`foo`。

这是一个浅层克隆，所以嵌套对象不会被克隆。

我们可以将两个对象合并成一个并用`Object.assign`返回。例如，如果我们有以下两个对象…

```
const foo = {
  foo: 1
}const bar = {
  bar: 1
}
```

…然后我们可以将它们合并成一个，并通过运行以下命令返回一个具有合并结构的新对象:

```
const merged = Object.assign(foo, bar);
```

然后我们得到…

```
{foo: 1, bar: 1}
```

…它不引用原始对象。

# IndexOf 和 findIndex

我们可以使用`indexOf`方法找到数组中原始元素的位置。如果没有找到，则返回`-1`，如果存在，则返回找到该值的第一个索引。

例如，如果我们有以下数组…

```
const arr = [1, 2, 3, 4, 1];
```

…那么`arr.indexOf(1)`会让我们得到 0。

对于对象数组，我们可以使用`findIndex`方法来做同样的事情。例如，给定以下数组…

```
const people = [{
    name: 'Joe',
    age: 10
  },
  {
    name: 'Joe',
    age: 11
  },
  {
    name: 'Mary',
    age: 13
  },
]
```

…我们可以如下使用`findIndex`方法:

```
people.findIndex(p => p.name === 'Joe')
```

然后，我们得到 0，因为在`name`属性中具有`'Joe'`的第一个条目是第一个条目。

# Object.entries()

使用 ES2017，我们可以通过使用`Object.entries`方法获得对象的所有键值对。它得到自己的键值对，而不是它的原型。

例如，我们可以编写以下代码:

```
const obj = {
  a: 'foo',
  b: 'bar',
  c: 'baz'
};
const arr = Object.entries(obj);
```

然后，我们回来了:

```
["a", "foo"]
["b", "bar"]
["c", "baz"]
```

第一个条目是键，第二个是值。

# Object.values()

我们可以使用`Object.values`方法来获取对象的值。

例如，如果我们有…

```
const obj = {
  a: 'foo',
  b: 'bar',
  c: 'baz'
};
const arr = Object.values(obj);
```

…然后我们回来了:

```
["foo", "bar", "baz"]
```

这些是对象属性的值。

![](img/929bbd27692cc28c3ff4072b0dee7a1b.png)

西蒙·伯特在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 获取字符串文字的字符

我们可以使用`charAt`方法或者括号符号来实现。

例如，我们可以写:

```
'foo'.charAt(0);
```

或者:

```
'foo'[0];
```

两个都让我们`'f'`，这是`'foo'`的第一个角色。

# 开关的紧凑型替代产品

我们可以使用对象作为`switch`语句的紧凑替代。

例如，如果我们有…

```
const apple = () => console.log('apple');
const orange = () => console.log('orange');
const grape = () => console.log('grape');
const fruit = 'apple';switch (fruit) {
  case 'apple':
    apple();
    break;
  case 'orange':
    orange();
    break;
  case 'grape':
    grape();
    break;
  default:
    return;
}
```

…我们可以用一个简单的物体来代替`switch`陈述:

```
const fruit = 'apple';const dict = {
  apple() {
    console.log('apple');
  },
  orange() {
    console.log('orange');
  },
  grape() {
    console.log('grape');
  },
}dict[fruit]();
```

# 用 indexOf 或 includes 替换 ifs

如果我们有类似…

```
const foo = 1;
if (foo == 1 || foo == 5 || foo == 7 || foo == 12) {
  console.log(foo);
}
```

…我们可以用`indexOf`方法替换`if`，如下所示:

```
const foo = 1;
if ([1, 5, 7, 12].indexOf(foo) !== -1) {
  console.log(foo);
}
```

`-1`表示数组中找不到该值。任何其他值表示该值所在的索引。

或者我们可以使用`includes`方法:

```
const foo = 1;
if ([1, 5, 7, 12].includes(foo)) {
  console.log(foo);
}
```

`includes`返回一个布尔值。如果在数组中找到该值，则为`true`，否则为`false`。

我们可以用`charAt`方法或括号符号来获取字符串的字符。

`switch`语句可以用充当字典的对象代替。

使用较新版本的 JavaScript，我们可以分别用`Object.values`方法和`Object.entries`方法获得对象的值和键值对。

为了获得数组元素第一次出现的索引，我们可以对原语使用`indexOf`方法，对对象使用`findIndex`。

要复制和合并对象，我们可以使用`Object.assign`方法。