# 让你的代码看起来和运行起来更好的 8 个 JavaScript 实践

> 原文：<https://betterprogramming.pub/8-javascript-practices-to-make-your-code-look-and-work-better-45bff492f86a>

## 你应该知道的简单提示和技巧

![](img/ea00682dc818bb2c5900c58618ba9aeb.png)

照片由[内森·达席尔瓦](https://unsplash.com/@silvawebdesigns?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

我总是说 JavaScript 充满了惊喜。它总能让我们惊讶。每天，我都会学到一些关于这种编程语言的新东西。一些非常小但能对你的代码产生巨大影响的东西。值得分享的东西。

在本文中，我将分享八个 JavaScript 技巧，你可以在日常工作中使用它们。所以让我们学点新东西吧！

# 1.仅运行一次事件侦听器

在您的 web 应用程序中使用事件侦听器是非常常见的，不仅仅是在普通的 JS 应用程序中，在 Angular 和 React 等现代 JS 框架和库中也是如此。

举个例子，假设我们想在点击按钮时做一些事情。我们可以在这里使用事件监听器。通常，我们希望在特定事件触发时采取行动。但是如果我们只想做一次呢？

别担心。我们实际上只需执行一次事件侦听器，只需执行以下操作:

```
const button = document.querySelector('button');button.addEventListener('click', () => {
    console.log('I will only run once');
},{ once: true })
```

# 2.使用“延期”属性

老实说，我们所有人都面临过这样的情况，我们觉得我们的 web 应用程序越来越慢，但我们无法找出原因。我们觉得我们做了所有应该做的事情，但是浏览器仍然需要时间来显示我们应用程序的第一个内容。

嗯，这可能是因为长脚本在 HTML 之前被执行。通常，我们在中添加我们的脚本，我们忘记了在中的脚本完成执行之前，下一个语句不会被执行。

我们可以简单地在脚本中使用`defer`属性，在 HTML 内容之后执行它们。

```
/* No matter where you place this script tag inside the HTML, this will only run when the HTML content is completely loaded. */<script src="main.js" defer></script>
```

# 3.快速条件检查

总是建议使用简写来使我们的代码看起来更整洁。因为没有像`if`语句这样的条件，任何应用程序都无法生存，所以我们也应该知道它的捷径。谢天谢地，JavaScript 提供了`&&`操作符。

我们可以通过使用`&&`操作符来缩短我们的条件检查。

# 4.给定范围内的随机数

我们只需一行代码就可以从给定的范围内生成一个随机数。这些技巧在实际应用中非常有用。

```
function randomNumber(num1, num2) {
    return Math.random() * (num2 - num1) + num1;
}console.log(randomNumber(50, 100)); // Expected output - random number between 50 and 100 (50 and 100 will be inclusive)
```

# 5.在一行中清空数组

这是我最喜欢的黑客之一。我们可以通过给数组的`length`属性赋值 0 来清空数组。

```
let sampleArray = ["John", "Bob", "Mark", "Alex"];
sampleArray.length = 0;console.log(sampleArray); // Output - [] (it's empty now)
```

# 6.检查对象是否为空

我们可以使用`Object.entries()`方法来检查一个对象是否为空。由于`Object.entries()`返回对象的可枚举属性的数组，因此如果该数组的长度为 0，则意味着该对象为空，因此该对象的属性为 0。

# 7.从数组中筛选重复值

从 ES6 开始，我们在 JavaScript 中有了一个新的概念，叫做`Set`。

> `“Set`对象是值的集合。您可以按插入顺序循环访问集合中的元素。`Set` **中的一个值只能出现一次**；它在`Set`系列中独一无二。”— [MDN 文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Set)

由于 JavaScript 中的`Set`是唯一的项目集合，即没有元素可以重复，因此我们可以用它来过滤数组中的重复值。

这个技巧同样非常简单但非常有用。首先，我们将把我们的`array`转换成`Set`，由于`Set`没有重复的值，我们新的`Set`将拥有来自`array`的唯一值。然后我们简单地将这个新的`Set`转换回一个`array`。

```
let namesArray = ["John", "Bob", "Mark", "Alex", "Mark", "Alain", "Bob"];console.log([...new Set(namesArray)]); // Output - ["John", "Bob", "Mark", "Alex", "Alain"]
```

# 8.使用一行克隆对象

由于 JavaScript 中的对象是引用类型，我们不能简单地使用`=`来克隆它们，所以使用下面的三种方法，你可以只用一行代码正确地克隆一个对象。

这就是这篇文章的内容。我希望这是有帮助的，你已经学到了一些新的东西。感谢阅读！