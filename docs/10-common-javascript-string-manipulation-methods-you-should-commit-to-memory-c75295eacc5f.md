# 你应该记住的 10 种常见的 JavaScript 字符串操作方法

> 原文：<https://betterprogramming.pub/10-common-javascript-string-manipulation-methods-you-should-commit-to-memory-c75295eacc5f>

## 使用这个清单来避免一直搜索

![](img/f0132e82a804ebf2daef24cda1ff39e0.png)

[贾斯汀·林](https://unsplash.com/@justinlim?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

通常需要对字符串进行操作来显示期望的结果。是前端必备技能。验证、向最终用户显示正确的信息等等都需要字符串方法。这里有一些你应该记住的字符串。这样，你将不再需要一直谷歌他们。我还将包括简单明了的例子，说明何时使用它们。

## 首先，介绍一下 JavaScript 字符串的背景

[JavaScript 字符串](https://www.w3schools.com/jsref/jsref_obj_string.asp)可以用单引号或双引号定义，如下所示:

```
const singleQuoteString = 'Hello';
const doubleQuoteString = "Hello";
```

字符串是一系列字符。将字符串视为字符数组会很有帮助。第一个字符是索引 0，然后是 1、2、3 等等。您经常需要使用这些索引和命令来分解、检索或更改字符串的某些部分。我还将在链接中为每种方法提供文档。

## 字符串长度

[这个简单的命令](https://www.w3schools.com/jsref/jsref_length_string.asp)返回字符串的长度。这在前端对文本字段输入进行数据验证、限制某些数据对象的长度等等时很常见。

```
let str = "matt";
console.log(str.length); // this outputs 4
```

## 查拉尔(指数)

[该函数](https://www.w3schools.com/jsref/jsref_charat.asp)将字符串视为字符数组。它检索您提供的索引处的字符。这里有一个例子:

```
let str = 'The cool programmer';
console.log(str.charAt(0)); // this outputs T
console.log(str.charAt(4)); // this outputs c
```

如果您需要检查字符串的一致性，这很有帮助。字符串中的最后一个索引是`string.length — 1`。就像数组一样，永远记住字符串中的第一个索引是 0，而不是 1。这让我不知所措，尽管我不愿意承认。

## 串联(字符串)

[这个方法](https://www.w3schools.com/jsref/jsref_concat_string.asp)将两个字符串连接成一个。如果你需要在一个字符串中添加一些东西或者组合它们，这个方法会派上用场。下面是它的用法:

```
const string1 = 'clean ';
const string2 = 'code'
console.log(string1.concat(string2)); // This returns 'clean code'
```

说实话，我不怎么用 concat 方法。我觉得 JavaScript [模板字符串](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals)干净多了；他们用更少的代码完成同样的事情。您还可以轻松地将字符串变量插入到另一个字符串中，而不必担心串联或索引。为此，您可以使用```符号:

```
const string1 = 'clean';
const string2 = 'code'
const templateString = `${clean} ${code}`;
console.log(templateString); // this prints out clean code
```

这就是干净的代码！

## 包括(字符串)

[这个函数](https://www.w3schools.com/jsref/jsref_includes.asp)检查一个字符串是否包含子串。这里有一个例子:

```
const str = 'hello world';
console.log(str.includes('hello')); // true
console.log(str.includes('word')); // false
```

就像许多这些方法一样，这有助于验证字符串是否包含用于验证目的的字符串中预期的单词或子字符串。

## 匹配(正则表达式字符串)

[“匹配”功能](https://www.w3schools.com/jsref/jsref_match.asp)检查字符串是否匹配正则表达式。[正则表达式](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Regular_Expressions)是一种字符模式，可用于验证字符串是否为某种格式。这通常可用于前端的字段验证。假设您需要验证一个字符串只包含字母。这里有一个例子:

```
const firstName = "Matt";
const badFirstName = "Matthew4";
const nameRegex = /^[a-zA-Z]+$/console.log(firstName.match(nameRegex)); // True
console.log(badFirstName.match(nameRegex)); // False
```

名字变量是有效的，因为它只包含字母。错误的名字变量失败，因为它有一个数字。

## replace(stringToBeReplaced，stringToAdd)

[这个方法](https://www.w3schools.com/jsref/jsref_replace.asp)获取字符串中出现的一个字符，并用另一个字符替换它。也许用户在文本字段中输入了电话号码，必须添加破折号而不是空格。你可以这样做:

```
const userInput = '414 555 5656';
console.log(userInput.replace(' ', '-')); // 414-555-5656
```

字符串中的空格用破折号替换。这有助于将字符串转换为您需要的数据格式。

## 拆分(字符串)

有时候，你可能需要[根据某个字符或者其他字符串拆分一个字符串](https://www.w3schools.com/jsref/jsref_split.asp)。该函数返回子字符串数组。以下是按空格拆分字符串的示例:

```
const hello = "Hello World";const helloWorldSplit = hello.split(' ');
console.log(helloWorldSplit); // ["Hello", "World"];
console.log(helloWorldSplit[0]); // "Hello"
```

拆分后，您可以像访问数组一样访问新字符串。

## 子串(索引，索引)

有时，你必须[在某个索引或索引范围](https://www.w3schools.com/jsref/jsref_substring.asp)拆分一个字符串。对于这个函数，传入希望开始的元素的索引，以及希望子字符串结束的字符串的索引。这里有一个例子:

```
const hello = 'Hello World';
console.log(hello.substring(1, 4)); // ell
```

警告:注意它不打印子串 4 处的字符。当 4 作为第二个参数提供时，它只打印索引 1-3 的字符。end 参数是停止点，不包含在子字符串中。

## toLowercase()/toUppercase()

这些方法获取一个字符串，并将其转换为全部大写的[或全部小写的](https://www.w3schools.com/jsref/jsref_touppercase.asp)。如果您想确保所查看的字符串不区分大小写，这可能会很有帮助。

```
const firstName = "Matt";
console.log(firstName.toUpperCase()); // MATT
```

在进行验证检查时，将所有字符串转换为大写或小写并不是一个坏主意，这样可以确保当数据不区分大小写时，应用程序的行为符合预期。

## 修剪()

[该方法](https://www.w3schools.com/jsref/jsref_trim_string.asp)是文本字段输入的另一种常用方法。它删除任何字符串中的空白。如果用户输入以空格结尾的内容，这通常是一个有用的函数。在字符串末尾留一个空格会影响字段验证和其他功能。

```
const stringWithSpace = 'John ';
console.log(stringWithSpace.trim()); // this will be 'John'
```

这就结束了我要学习的字符串方法列表。我希望你能在你的项目中使用其中的一些。

感谢阅读！