# JavaScript 中的模板文字

> 原文：<https://betterprogramming.pub/replace-string-concatenation-using-template-literals-in-javascript-fbdfd5e83bbe>

## 了解如何使用模板文本替换字符串串联

![](img/f5e225fad7f8d847709e47e3027f79d2.png)

[明锐丹](https://unsplash.com/@octadan?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片。

模板文字允许您在字符串中嵌入 JavaScript 表达式。

要创建模板文字，您应该使用反斜杠(````):

```
`I am a template stirng`
```

模板文字有三个特征:

*   多行字符串
*   插值(在字符串中插入表达式)
*   标记的模板文字(调用函数)

# 多行字符串

在模板文字之前，如果我们想创建一个多行字符串，我们应该写:

```
console.log('Line 1 \n' + 'Line 2');
```

但这可以简化为:

本质上，模板文字使得多行字符串更加简单。

# 插入文字

这将允许您在模板文本中插入有效的 JavaScript 表达式。在计算字符串之前，所有的表达式都被执行。

您可以包含一个类似于``${expression}``的表达式:

```
let a = 10;
let b = 20;console.log(`Result = **${a + b}**`); // **Result = 30**
```

使用条件表达式:

```
var a = 10;var b = 20;console.log(` **${a > b ? a : b}** is greater`); // 20 is greater
```

在模板文本中使用函数调用:

```
function getName() {
   return "JavaScript Jeep";
}console.log(`Name => ${getName()}`); // Name => JavaScript Jeep
```

使用模板文字打印```:

```
console.log(`\``); // **`**
```

使用模板文字打印`${}`:

# 标记模板

标记模板允许你调用一个函数。

> "标签允许你用一个函数解析模板文字."— [MDN 网络文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals)

在上面的函数中，所有的字符串都被传递给`string`参数，而`a`被传递给`param1`。

这是另一个例子:

您可以使用一个`rest`参数，而不是使用多个参数:

下面是另一个使用析构变量的例子:

[JavaScript Joel](https://twitter.com/joelnet/status/1142217207042019330) 提供了一个使用上述所有内容的真实示例:

> 参观 JavascriptJeep.com

[](https://www.buymeacoffee.com/Jagathish) [## Jagathish Saravanan

### 你好👋。我是 Jagathish。爱写关于 JavaScript 的文章。你的支持就像夏天吃冰淇淋一样。我…

www.buymeacoffee.com](https://www.buymeacoffee.com/Jagathish)