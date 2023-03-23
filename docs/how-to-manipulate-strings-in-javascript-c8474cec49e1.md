# 如何在 JavaScript 中操作字符串

> 原文：<https://betterprogramming.pub/how-to-manipulate-strings-in-javascript-c8474cec49e1>

## 组合和重复字符串，改变大小写，删除空白，等等

![](img/8d7ad300a033abaad2dd8d8f41e0c6d9.png)

约翰·霍特在 [Unsplash](https://unsplash.com/search/photos/strings?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

JavaScript 有内置的函数和构造来完成大多数字符串操作。不需要的可以通过几个简单的函数调用来完成。

# 组合字符串

## 串联

有两种方法可以做到。一种方法是使用`concat`函数。

例如:

```
const hello = "Hello ";
const bob = "Bob!";
const res = hello.concat(bob); // Hello Bob!
```

另一种方法是使用`+`操作符，就像这样:

```
const hello = "Hello ";
const bob = "Bob!";
const res = hello + bob; // Hello Bob!
```

## 模板字符串

ES6 或更高版本具有模板字符串，因此您可以在另一个字符串中包含字符串，如下所示:

```
const hello = "Hello ";
const bob = "Bob!";
const res = `${hello} ${bob}`; // Hello Bob!
```

## 数组.连接

如果你有一个字符串数组，你可以用`join`函数将条目用相同的分隔符组合起来，就像这样:

```
const arr = ['Hello', 'Bob'];
const res = arr.join(' '); // 'Hello Bob'
```

# 获取子串

## 字符串. substr

JavaScript 字符串有一个`substr`函数来获取字符串的子串。

它将起始索引作为第一个参数，将起始索引中的多个字符作为第二个参数，依此类推。

它可以这样使用:

```
const str = "Hello Bob";
const res = str.substr(1, 4); // ello
```

## 字符串.子字符串

JavaScript 字符串还有一个`substring`函数，它将起始和结束索引作为两个参数，并返回一个介于起始和结束索引之间的字符串。

包括开始索引，但不包括结束索引。

示例:

```
const str = "Hello Bob";
const res = str.substring(1, 3); // el
```

# 改变案例

## String.toLocaleLowerCase()

根据浏览器的区域设置，将字符串转换为小写。返回新字符串，而不是修改原始字符串。

例如:

```
const str = "Hello Bob!";
const res = str.toLocaleLowerCase(); // hello bob!
```

## String.toLocaleUpperCase()

根据浏览器的区域设置，将字符串转换为大写。返回新字符串，而不是修改原始字符串。

例如:

```
const str = "Hello Bob!";
const res = str.toLocaleUpperCase(); // 'HELLO BOB!'
```

## String.toLowerCase()

将字符串转换为小写。返回新字符串，而不是修改原始字符串。

例如:

```
const str = "Hello Bob!";
const res = str.toLocaleLowerCase(); // 'hello bob!'
```

## String.toUpperCase()

将字符串转换为大写。返回新字符串，而不是修改原始字符串。

例如:

```
const str = "Hello Bob!";
const res = str.toLocaleUpperCase(); // 'HELLO BOB!'
```

# 删除空白

## String.trim()

从字符串中删除开始和结束空格。

例如:

```
const str = "         Hello Bob!     ";
const res = str.trim(); // 'Hello Bob!'
```

## 删除字符串中的所有空格

没有函数可以删除字符串中的所有空格。为此，我们必须用空字符串替换字符串中的空格。

例如:

```
const str = "         Hello Bob!     ";
const res = str.replace(/ /g, ''); // 'HelloBob!'
```

# 重复字符串

JavaScript 中有几种重复字符串的方法。JavaScript 字符串有内置的`repeat()`函数。

你也可以使用一个循环来做同样的事情。

## 字符串重复函数

要使用`repeat`函数，需要将字符串重复的次数作为参数传递。它返回一个新的字符串。

例如:

```
const hello = "hello";
const hello5 = A.repeat(5);
console.log(hello5); // "hellohellohellohellohello"
```

## 使用循环

你可以使用`for`循环和`while`循环来重复连接字符串。

使用`for`循环，您可以:

```
const hello = "hello";
let hello5 = '';
for (let i = 1; i <= 5; i++){
  hello5 += hello;
}
console.log(hello5); // "hellohellohellohellohello"
```

使用`while`循环，您可以:

```
const hello = "hello";
let hello5 = '';
let i = 1;
while(i <= 5){
  hello5 += hello;
  i++
}
console.log(hello5); // "hellohellohellohellohello"
```

它们都涉及到最大值的增量索引。

# 搜索

有几种方法可以在 JavaScript 中搜索字符串。

琴弦具有`startsWith`、`endsWith`、`indexOf`、`lastIndexOf`、`charAt`、`search`、`includes`和`match`功能。

## 开始于

`startsWith`检查字符串是否以传入的子字符串开头。

例如:

```
const str = "Hello world.";
const hasHello = str.startsWith("Hello"); // trueconst str2 = "Hello world.";
const hasHello2 = str.startsWith("abc"); // false
```

## 末端

`endsWith`检查字符串是否以传入的子字符串结尾。

例如:

```
const str = "Hello world.";
const hasHello = str.endsWith("world."); // trueconst str2 = "Hello world.";
const hasHello2 = str.endsWith("abc"); // false
```

## 索引 Of

`indexOf`查找子字符串第一次出现的索引。如果未找到，则返回-1。

例如:

```
const str = "Hello Hello.";
const hasHello = str.indexOf("Hello"); // 0
const hasHello2 = str.indexOf("abc"); // -1
```

## lastIndexOf

`lastIndexOf`查找子字符串最后一次出现的索引。如果未找到，则返回-1。

例如:

```
const str = "Hello Hello.";
const hasHello = str.lastIndexOf("Hello"); // 6
const hasHello2 = str.lastIndexOf("abc"); // -1
```

## 查拉特

`charAt`返回位于字符串索引处的字符。

例如:

```
const str = "Hello";
const res = str.charAt(0); // 'H'
```

## 搜索

`search`获取传递给函数的子字符串的位置。如果在字符串中找不到子字符串，则返回-1。

示例:

```
const str = "Hello";
const res = str.search('H'); // 0
```

## 包含

`includes`检查传入的子串是否在字符串中。如果在字符串中，返回`true`，否则返回`false`。

```
const str = "Hello";
const hasH = str.includes('H'); // true
const hasW = str.includes('W'); // false
```

# 替换

字符串中包含的`replace()`函数对于用另一部分字符串替换另一部分字符串很有用。它返回一个新字符串，其中包含替换子字符串后的字符串。

示例:

```
const str = "Hello Bob";
const res = str.replace("Bob", "James"); // 'Hello James'
```

`replace()`也可以用来替换所有出现的子串。

例如:

```
const str = "There are 2 chickens in fridge. I will eat chickens today.";
const res = str.replace(/chickens/g, "ducks"); // "There are 2 chickens in fridge. I will eat chickens today."
```

如果第一个参数是全局搜索的正则表达式，它将替换子字符串的所有匹配项。

**现在就在**[**【http://jauyeung.net/subscribe/】**](http://jauyeung.net/subscribe/)**订阅我的邮件列表。在推特上关注我**[**https://twitter.com/AuMayeung**](https://twitter.com/AuMayeung)