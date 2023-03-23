# 字符串模板文字:JavaScript ES6 特性系列(第 5 部分)

> 原文：<https://betterprogramming.pub/string-template-literals-javascript-es6-feature-series-pt-5-a40e55a5485b>

## 为什么开发人员需要知道反勾号键在哪里

![](img/751c3f4f312dca34085157f5fab2caea.png)

照片由[亚伦·伯顿](https://unsplash.com/@aaronburden?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/search/photos/writing?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

# 介绍

这些作品背后的灵感很简单:对于很多开发人员来说，JavaScript 有点神秘。我同意，有很多东西需要理解——这是一门复杂的语言，一旦你了解了它的皮毛。

然而，根据维基百科，如今 95%最受欢迎的在线网页都是由 JavaScript 驱动的，除此之外，ECMAScript 委员会每年都会发布更新。需要跟上的东西太多了。

由于这些变化来得如此之快，我想提供一些我经常使用的 ES6+特性的文章和例子，供其他开发人员参考。

目的是使这些文章简短。我将对该语言的各种改进提供深入的解释，我希望这会启发您使用 JavaScript 编写一些真正酷的东西。谁知道呢，在这个过程中你可能会学到一些新的东西！

在我的第五篇文章中，我将讨论模板文字:在 JavaScript 中使用字符串的最新、最简单的方法。

# 模板文字

字符串是主要的 JavaScript 对象类型之一。它们都是用单引号`‘’`或双引号`“”`括起来的字符序列。

## 传统弦乐剖析

```
const string1 = 'this is a string in single quotes';

const string2 = "this is a string in double quotes";
```

上面的字符串类型值是不可变的(不可更改)，但是使用`String()`构造函数创建的字符串对象是可变的，因为它们是对象，你可以给它们添加新的属性。

但这是 JavaScript 101，也是本文的重点。我兴奋地谈论的是[模板文字](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals)(在 ES2015 之前被称为“模板字符串”)。

为了让我们开始，下面是模板文字的官方 MDN 定义:

> 模板文字是允许嵌入表达式的字符串文字。您可以使用多行字符串和字符串插值功能。”— [MDN 网络文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals)

我说的“嵌入式表达式”或“字符串插值特性”是什么意思？很高兴你问了，让我给你看看！

## 模板文字语法

```
// just a normal string in one line - not much different from a traditional string
const simpleString = `a template literal is surrounded by backticks`;

// a string spread across multiple lines
const multiLineString = `it can be spread across
multiple lines with just 
one set of enclosing backticks`;

const name = "Paige";

// an expression placeholder allowing a variable to be injected into the string
const greetByName = `Hello, ${name}`;
console.log(greetByName); // 'Hello, Paige'
```

这三个例子展示了如何使用模板文字。

第一个是`simpleString`，是一个单行字符串，很像普通的 JavaScript 字符串，用传统的引号括起来。

第二个，`multiLineString`，是一个多行字符串(包括返回字符，尽管您看不到它们的`\r`字符)，它将完全按照上面写的方式打印出来。

第三个是表达式占位符的例子，其中变量`name`被插入到字符串`greetByName`中。

既然我已经向您展示了使用模板字面量的快速方法，那么让我们深入了解您需要了解的关于它们的细节。

## 定义模板文字的特征

模板文字有许多特征，这些特征使它们不同于过去的 JavaScript 字符串。在我看来，这些细微的差别也使它们更加有用。

这就是模板文字的独特之处。

## **反勾号**

识别模板文字最简单的方法是通过字符周围的反斜杠`(``)`(也称为重音符)而不是单引号或双引号。

对于 Mac 用户，反勾号键位于键盘的左上角。这与您在命令行终端中使用的波浪号`~`键相同。

*反斜杠字符串示例:*

```
/* your basic JavaScript strings, but with back-ticks. 
notice the lack of escape characters needed for things 
like single quotes, double quotes, and apostrophes
when back-ticks are employed */

const fooString = `A string called 'fooString'`;

const barString = `Another string named "barString"`;

const bazString = `Without fooString and barString, you can't have bazString, right?`;
```

*注意:*如果需要在模板字符串内部写反斜杠，必须用反斜杠进行转义:``\```和`"`”`一样。

一些 JavaScript 开发人员甚至建议使用更漂亮的和其他自动格式化程序来改变他们所有的字符串，使其始终使用反勾号。我自己默认使用单引号，除非我需要模板文字的力量，但这只是我自己！

## **表达式占位符**

模板文字可以包含占位符，占位符在字符串中由美元符号和花括号(`${variableExpression}`)表示。占位符中的表达式和反勾号(` `)之间的文本被传递给一个函数。

*表达占位符示例:*

```
function authorize(user, action) {
  if (!user.hasPermission(action)) {
    throw new Error(
      `User ${user.name} is not allowed to ${action}.`
    );
  }
}

const person = {
  name: "Sean"
};
const activity = "bake";

console.log(authorize(person, activity)); // "User Sean is not allowed to bake."
```

## **多行字符串**

在源代码中插入的任何换行符都是模板文本的一部分。不必使用`\n`和`+`符号将两个字符串连接在一起得到一个多行字符串，整个字符串可以作为一个字符串来完成。

*创建多行字符串的原始方式示例:*

```
console.log("To make a multi-line string this way\n" + 
"I have to concatenate two separate strings with a + sign");

/* prints: To make a multi-line string this way
I have to concatenate two separate strings with a + sign */
```

*模板文字方式创建多行字符串示例:*

```
console.log(`With template literal back-ticks, I can spread strings across
as many lines as I want
with
no
problem.`);

/* prints: With template literal back-ticks, I can spread strings across
as many lines as I want
with
no
problem. */
```

## **表达式插值**

过去，将表达式(如简单的数学计算)嵌入到字符串中很难用代码写出(和解释)。

*字符串串联以将表达式值插入其中:*

```
const c = 10;
const d = 5;
console.log('It used to be harder to calculate that ' + (c + d) 
+ '\n is not the same as ' + (2 * c) + ' in a string.');

/* prints: It used to be harder to calculate that 15
is not the same as 20 in a string. */
```

模板文字利用了语法上的优势，使得这样的替换更具可读性。

*字符串模板文字内部的表达式插值:*

```
const c = 10;
const d = 5;
console.log(`With the syntactic sugar of ES6 template literals, 
doing math in strings like this: ${c + d} 
and that: ${2 * c} is a cinch.`);

/* prints: With the syntactic sugar of ES6 template literals, 
doing math in strings like this: 15
and that: 20 is a cinch. */
```

## 嵌套模板

有时候嵌套一个模板是获得可配置字符串的最简单和最易读的方法。通过在模板内的占位符`${ }`中使用反勾号，允许在反勾号模板内使用内反勾号是很简单的。在使用三元组来确定返回哪个模板文本的情况下，这可能很有用。

*在嵌套模板之前配置字符串:*

```
var classes = 'header';

function isDesktopSize(){
  window.innerWidth > 1400 ? true : false;
};

var navbar = {
  isOpen: false
};

classes += (isDesktopSize() ?
   '' : navbar.isOpen ?
     ' collapse-navbar' : ' expand-navbar');

console.log(classes);

// prints: header expand-navbar
```

*创建已配置字符串的嵌套模板:*

```
function isDesktopSize(){
  window.innerWidth > 1400 ? true : false;
}

var navbar = {
  isOpen: true
};

classes = `header ${ isDesktopSize() ? '' :
 `icon-${navbar.isOpen ? 'collapse' : 'expand'}` }`;

 console.log(classes);

 // prints: header icon-collapse
```

嵌套 ternaries(在两个可配置的 string 示例中都使用了)通常是不被认可的编码实践，但是我还是在示例中使用了它们来说明如何更干净地配置`classes`变量。

在实际编码中，如果你发现自己在处理嵌套 ternaries，建议使用`if / else / if`语句。

## 标记模板

模板文字的更高级形式是*标记的*模板。标签允许你用一个函数来解析模板文本。标记函数中的第一个参数包含一个字符串值数组。其余的参数与表达式相关。最后，该函数可以返回一个被操纵的字符串(或者它可以返回完全不同的内容)。用于标记的函数的名称可以是您想要的任何名称。

*标记模板示例:*

```
var guy = 'Paul';
var age = 96;

function myTag(strings, personExp, ageExp) {
  var str0 = strings[0]; // "That "
  var str1 = strings[1]; // " is a "

  var ageStr;
  if (ageExp > 99){
    ageStr = 'centenarian';
  } else {
    ageStr = 'youngster';
  }

  // You can even return a string built using a template literal - just to be fancy
  return `${str0}${personExp}${str1}${ageStr}`;
}

var output = myTag`That ${ guy } is a ${ age }`;

console.log(output);
// prints: That Paul is a youngster

var lady = 'Elizabeth';
var age = 107;

var output = myTag`That ${ lady } is a ${ age }`;

console.log(output);
// prints: That Elizabeth is a centenarian
```

相当整洁！

## 原始字符串

下面的`tag`函数的第一个参数提供了特殊的`raw`属性，允许您在输入原始字符串时访问它们，而不处理转义序列(`\t`、`\v`、`\n`、`\r`等)。).

*模板文字字符串的原始属性示例:*

```
function tag(strings) {
  console.log(strings.raw[0]);
}

tag`I'd like this line 1 \n to ignore the newline and be in line with 1 
\n even though I should be on line 3 at this point`;

/* prints: I'd like this line 1 \n to ignore the newline and be in line with 1
\n even though I should be on line 3 at this point */

// including the two characters '\' and 'n' multiple times
```

此外，`[String.raw()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/raw)`方法用于创建原始字符串，就像默认的模板函数和字符串连接一样。

*带有表达式插值和字符串方法的原始属性示例:*

```
var str = String.raw`What's up \n${4+7}?`;
console.log(str);
// prints: What's up \n11?

console.log(str.length);
// prints: 15

console.log(str.split('').join(','));
// prints: W,h,a,t,',s, ,u,p, ,\,n,1,1,?
```

我不确定在现实世界中你会经常需要它，但是知道它就在那里真好！

# 结论

ECMAScript 每年都会发布 JavaScript 语言的新更新，旨在让开发人员的生活更加轻松。乍一看，这种语法似乎很陌生——即使对于经验丰富的 JavaScript 开发人员来说也是如此。然而，花时间学习新技巧是非常值得的——它们会给你的发展带来巨大的动力。

我这个博客系列的目的是解释现在到处都在使用的 JavaScript 和 ES6 语法中我最喜欢的部分，并向您展示如何最大限度地使用最新的部分。

字符串是 JavaScript 语言最基本的部分之一，但当 ES2015 出现时，它引入了模板文字，这些曾经不起眼的对象得到了很多升级。注入变量，执行表达式，甚至创建多行字符串——由于模板文字，曾经复杂的代码变得轻而易举。

过几周再来看看，我会写更多关于 JavaScript 和 ES6 或其他与 web 开发相关的东西。

感谢您的阅读，我希望您能尝试一下各种形式的模板文字——它们使得 JavaScript 中的字符串更容易使用。

**参考资料和更多资源:**

*   String，MDN Docs:[https://developer . Mozilla . org/en-US/Docs/Web/JavaScript/Reference/Global _ Objects/String](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)
*   模板文字，MDN 文档:[https://developer . Mozilla . org/en-US/Docs/Web/JavaScript/Reference/Template _ Literals](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals)
*   我在 Github 上的所有 gist:[https://gist.github.com/paigen11](https://gist.github.com/paigen11)