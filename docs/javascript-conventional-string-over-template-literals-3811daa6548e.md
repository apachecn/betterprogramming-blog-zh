# JavaScript 中模板文字上的传统字符串

> 原文：<https://betterprogramming.pub/javascript-conventional-string-over-template-literals-3811daa6548e>

## 何时使用每一种，为什么

![](img/dd13c286047a30e5458ffcf30e7337b4.png)

照片由[马修·费雷罗](https://unsplash.com/@matheusferrero)在 [Unsplash](https://unsplash.com/photos/RxpqZBBevKg) 拍摄

按照惯例，用 JavaScript 写一个字符串，我们可以使用`singleQuote`或者`doubleQuote`。例如:

```
const singleQuoteString = 'This is a single quote string';
const doubleQuoteString = "This is a double quote string";
```

随着 [ES6](http://es6-features.org/) 中[模板文字](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals)的引入，字符串文字得到了巨大的改进，比如多行支持、在字符串中更好地嵌入变量或表达式、不再需要在字符串中使用反斜杠引号或双引号等。

它基本上只是使用反勾号，而不是引号或双引号。

```
const templatedString = `This is a double quote string`;
```

# 模板文字的首选项

通过观察模板文字(反勾号)相对于传统字符串的优势，我怀疑是否还需要传统字符串。

我在下面找到了这篇文章，它强烈主张使用反勾号作为替换传统字符串的标准，因为它有好处，也是为了一致性。

[](https://ponyfoo.com/articles/template-literals-strictly-better-strings) [## 模板文字严格来说是更好的字符串

### 假设有一个 ES6 代码库，有几种方法可以在字符串中进行插值。你可以使用字符串连接…

ponyfoo.com](https://ponyfoo.com/articles/template-literals-strictly-better-strings) 

我质疑用模板文字替换传统字符串会影响性能，并发现一篇文章显示它们的性能差异可以忽略不计。

[](https://medium.com/javascript-in-plain-english/are-backticks-slower-than-other-strings-in-javascript-ce4abf9b9fa) [## 反斜线(``)比 JavaScript 中的其他字符串慢吗？

### 我使用 jsperf 运行了一些测试，以找出哪个字符串字符运行得最快，以及是否比空的…

medium.com](https://medium.com/javascript-in-plain-english/are-backticks-slower-than-other-strings-in-javascript-ce4abf9b9fa) 

所以，切换到模板字面量似乎是合理的。

我不会列出模板文字的好处，因为它们已经在许多文章中提供了。

# 对传统字符串的偏好

然而，由于 JavaScript 已经存在了很长时间，我仍然看到许多代码使用传统的单引号和双引号来表示字符串，所以人们仍然坚持使用它们肯定是有原因的。

就连 Airbnb 的 [JavaScript 代码指南也不提倡。因此，我进行了更多的搜索并理解了原因。以下是原因，从最明显的到一些对我来说不可思议的…](https://github.com/airbnb/javascript#strings)

## 并非所有反勾号字符串对于 JavaScript 都是合法的

令我惊讶的是，如果我们只是盲目地将所有的单引号和双引号替换成反勾号，我们就会出错。下面是三个场景。

1.  使用`use strict`。

如果我们用``use strict`;`代替`'use strict';`，就不再起作用了。最可怕的是，没有任何预兆。

2.从模块导入。

如果我们使用`import React from `react`;`而不是`import React from 'react';`，将导致如下错误。

```
Parsing error: Unexpected token
```

3.JSON 风格的对象声明。

我们不能像下面这样声明一个对象。

```
const headers = {
    `Accept`: `application/json`,
    `Content-Type`: `application/json`
};
```

它将错误排除:`Uncaught SyntaxError: Unexpected template string`。

以下是合法的。

```
const headers = {
    [`Accept`]: `application/json`,
    [`Content-Type`]: `application/json`
};
```

并且:

```
const headers = {
    'Accept': `application/json`,
    'Content-Type': `application/json`
};
```

## 普通字符串和需要模板文字的字符串的区别

就像`const`和`let`一样，有些人主张我们应该只在需要的时候使用模板文字字符串。即不要对所有事情都使用`let`。

例如，在下面的代码中，`name`只是一个普通的字符串，而`sentence`因为需要嵌入一个变量，所以使用了模板文字。

```
let name = 'Alex';
let sentence = `My name is ${name}`;
```

## 首选显式转义(反斜杠)

使用模板化的文字，我们可以很容易地得到 multiline。但是如果在这条线后面有很多空格，人们可能不容易看到它。如下图，第一行`space`字后有空格，但不容易看到。

```
let spaces = `there are unseen space     
But you can’t see it`
console.log(spaces.replace(‘\n’,’ ‘))// Output "there are unseen space     But you can’t see it"
```

但是有了下面，下一行在哪里就很清楚了，而且`\n`说的很清楚。

```
spaces = “there cannot have unseen space\n\
You can see all”
console.log(spaces.replace('\n',' '))// Output "there cannot have unseen space You can see all"
```

因此，对于一些开发人员来说，他们喜欢使用传统字符串的更加冗长和明确的方法。

注意，对于第二个例子，如果在`space\n\`后面有空格，它将错误地指出`Uncaught SyntaxError: Invalid or unexpected token`，这不容易识别。

## 键盘问题

我不会想到这个问题，因为我是一个纯粹的美国 ASCII 键盘用户。法语、德语、葡萄牙语、巴西语和西班牙语键盘的用户抱怨称，反勾号被用作他们特殊字母的其他用途。

例如，对于法语和西班牙语键盘，例如键入```然后`a`，产生一个`à`。其他角色也一样`àèìòù`。

如果有其他原因，欢迎回复本博客分享。

# TL；DR；

我的观点是，模板文字肯定比传统的字符串有优势，使代码在各方面更具可读性。因此，人们应该明确地使用它们。要么使用模板文字替换所有常规字符串，要么仅在需要组首选项时使用它们。

JavaScript 是一种非常灵活的语言。做同样的事情有很多方法。有许多建议的最佳实践。选择一个最适合你的，并让团队持续使用它。

感谢阅读。

~Elye~