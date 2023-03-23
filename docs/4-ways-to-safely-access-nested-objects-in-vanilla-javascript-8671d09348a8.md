# 在普通 Javascript 中安全访问嵌套对象的 4 种方法

> 原文：<https://betterprogramming.pub/4-ways-to-safely-access-nested-objects-in-vanilla-javascript-8671d09348a8>

## 如何访问嵌套对象并编写更安全的代码

![](img/e31163f752dec524e02bfa9b3478d057.png)

布莱克·康纳利在 [Unsplash](https://unsplash.com/search/photos/code?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

如果您正在使用 Javascript，您可能会遇到这样的情况:您必须访问一个深度嵌套的对象。如果一切顺利，您可以毫无问题地获得数据。太好了！

然而，在现实中，我们并不总是保证数据*存在，*却错误地假设它存在*。*我曾多次处于这种境地，我经常发现自己在想:“这个值怎么可能不被填充呢？”

幸运的是，我们需要防止这些讨厌的错误的只是对`undefined`值的额外检查。

# 错误的例子

对于那些以前没有见过这种情况的人来说，这里有一个例子可以让你熟悉。假设我有一个 JavaScript 对象，代表我感兴趣的音乐艺术家。

```
const macAyres = {
  tours: {
    nearMe: {
      sanFrancisco: {
        date: 'Sun Oct 27',
        location: 'The Regency Ballroom',
        cost: '30.00',
      },
    },
  }
}
```

如果我错误地认为这场音乐会在圣何塞举行，并希望检索(假想的)音乐会的地点，我可能会键入如下内容:

`const concertLocation = macAyres.tours.nearMe.sanJose.location;`

由于属性`sanJose`不存在，上面的表达式类似于`undefined.location`。当然，我们会遇到一个非常熟悉的错误；未捕获的类型错误:无法读取 undefined 的属性“type”。

# 1.用于检查空值/未定义值的三元运算符

处理这个问题最直接的方法是在试图访问其中的数据之前检查对象是否是`null/undefined`。

```
const concertCity = macAyres.tours.nearMe.sanJose
const concertLocation = concertCity ? concertCity.location : undefined;
```

这个选项可能是最容易实现和检查的。当对象嵌套不深时，这可能是一个很好的检查方法。

然而，当我们必须遍历更深的几层时，检查会变得非常复杂和重复。

```
const concertLocation = (macAyres.tours &&
macAyres.tours.nearMe &&
macAyres.tours.nearMe.sanJose) ? macAyres.tours.nearMe.sanJose.location : undefined;
```

如果您有一个深度嵌套的对象，最后两种方法提供了一种更优雅的方式。

# 2.奥利弗·斯蒂尔的嵌套对象访问模式

这种访问值的方法类似于上一点中描述的方法。

我们没有使用`&&`操作符来检查`null/undefined`值，而是使用了`||`操作符和一个空的对象文字。下面的例子将提供更多的清晰度。

```
const concertLocation = (macAyres.tours.nearMe.sanJose || {}).location;
```

由于`||`运算符中断返回遇到的真值，如果不是`null/undefined`，上面的表达式将返回`macAyres.tours.nearMe.sanJose`，否则返回`{}`。

在这种情况下，我们将从一个空对象访问`location`，而不是从`undefined`，所以我们将避免得到错误。

还要注意，当`||`操作符查找第一个遇到的**真值时，我们*不应该*写这样一个表达式:**

```
const concertLocation = ({} || macAyres.tours.nearMe.sanJose).location;
```

在这种情况下，我们将总是依靠`{}`，因为它被认为是
真值。如果你想了解更多关于逻辑操作符的知识，这篇文章提供了一个很好的纲要。

与上面描述的方法类似，如果我们需要在每一步都使用`||`操作符，那么即使是奥利弗·斯蒂尔的模式也会变得越来越难以理解。

幸运的是，下一个方法展示了如何利用强大的 JavaScript 函数来保持整洁。

# 3.数组缩减

这种方法要求你熟悉 JavaScript 的内置`reduce` 函数。如果你还不知道这个，这是一个很好的工具，你可以在 Mozilla 网站上了解更多。

简而言之，`reduce`对一个数组执行 reducer 函数并返回一个输出。你可以想象我们要处理的数组是一个包含了我们遍历对象的路径的数组。

我们将使用上面的示例数据结构，但是为了演示`reduce`函数的能力，我们将假设每一级数据都没有得到保证。

```
// Data Structure for recap
const macAyres = {
  tours: {
    nearMe: {
      sanFrancisco: {
        date: 'Sun Oct 27',
        location: 'The Regency Ballroom',
        cost: '30.00',
      },
    },
  }
}const paths = ['tours', 'nearMe', 'sanJose', 'location'];
const location = paths.reduce((object, path) => {
    return (object || {})[path]; // Oliver Steele's pattern}, macAyres)
```

请注意我们是如何整合之前所学的知识的！如果您不喜欢使用 Oliver Steele 模式，三元运算符检查也很好。

这种访问值的方式是我个人最喜欢的方式之一，因为它保持了高度的可读性和干爽性，即使对象嵌套很深。

# 4.带有 ES6 箭头功能的 Try/Catch 辅助功能

我正在做一些研究，无意中发现了这个[堆栈溢出帖子](https://stackoverflow.com/questions/14782232/how-to-avoid-cannot-read-property-of-undefined-errors/42349521)，我认为它真的很棒！

我以前没有使用过这种方法，但是这种简洁的方法可以作为一种实用的方法来实现，并且更加可重用。写完这一点，我怀疑我可能真的最喜欢这个了。

```
// Code Snippet taken from the post
function getSafe(fn, defaultVal) {
    try {
        return fn();
    } catch (e) {
        return defaultVal;
    }
}

// use it like this
getSafe(() => obj.a.lot.of.properties);

// or add an optional default value
getSafe(() => obj.a.lot.of.properties, 'nothing');
```

# 结论

这四种方法可以用来编写更安全的代码，并避免出现破坏代码的讨厌错误。

虽然有一些库可以帮助您克服这一点，但是如果您想要一种简单快捷的方法，您可能会考虑这些普通的 JavaScript 方法。

最后，我希望您能从这篇文章中获得一些有用的东西。这是我第一次写技术文章，我希望人们能从中发现价值。