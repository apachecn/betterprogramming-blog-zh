# 你知道吗，在 JavaScript 中几乎所有的东西都是对象。

> 原文：<https://betterprogramming.pub/did-you-know-that-almost-everything-is-an-object-in-javascript-f06c3f69faf1>

## 你可能听说过这个，但是让我们看看是否真的是这样

![](img/be0453de24a88f553253448b0466b851.png)

Boris Stefanik 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

首先，我们应该理解在这种语言中一个对象实际上是什么。

# 目标

对象是属性的动态集合，其原型具有隐藏属性。

这个定义对你来说可能看起来不同，因为在其他语言中对象是一个类的实例。在 JavaScript 中，它只是键值对的集合。

一旦创建了一个对象，我们就可以添加、编辑或删除它的属性。考虑以下空对象:

```
const game = {};
game.name = 'Candy Crush'; //prop added
game.name = 'Candy Crush Saga'; //prop edited
delete game.name //prop deleted
```

对象确实有一个对原型对象的隐藏属性，叫做`__proto__`。所有对象都继承内置`Object.proptotype`对象的属性。下面是我们如何检查这一点:

```
game.__proto__ === Object.prototype;
//trueObject.getPrototypeOf(game) === Object.prototype;
//true
```

`Object.getPrototypeOf()`是获取对象原型的更好选择。我使用`__proto__`属性只是为了显示一个隐藏的属性实际上存在于新创建的对象上。

# 数组

数组是对象吗？

记住对象是什么:道具的动态集合。让我们创建一个空数组，并向其中添加新属性:

```
const flightArr = [];flightArr.no = 815; //prop added
flightArr.from = 'Sydney'; //prop added
delete flightArr.no //prop delete
```

现在让我们检查数组是否有其原型的隐藏道具，`Array.prototype`:

```
flightArr.__proto__ === Array.prototype;
//trueObject.getPrototypeOf(flightArr) === Array.prototype;
//true
```

数组确实是对象，但是它们不应该像前面的例子那样使用。下面，您可以查看我们如何创建一个简单的数组:

您应该知道的是，数组是使用对象模拟的。使用类似于下面的对象模拟以前的阵列:

```
{
 '0': 'Jack',
 '1': 'Locke',
 '2': 'Kate'
}
```

因为它是一个对象，我们可以使用字符串键访问它上面的元素。数组索引被转换为字符串。这就是为什么索引`1`和键串`'1'`返回相同的元素:

```
arr['1'];
//'Locke'arr[1] === arr['1'];
//true
```

连 JavaScript 都搞混了，说数组是对象。只是尝试一下`typeof`操作符。为了确定一个值实际上是否是一个数组，我们需要使用`Array.isArray`:

```
const arr = [];typeof arr
//'object'Array.isArray(arr);
//true
```

# 功能

函数也是对象吗？

让我们试着创建一个函数，并开始向它添加属性:

```
const movie = function(){};
movie.title = 'Toc Toc'; //prop added
movie.year = 2017; //prop added
delete movie.year; //prop deleted
```

考虑下面的`Integer`函数，它将其他值转换成整数:

```
const Integer = function(value){ 
  return parseInt(value);
};Integer('123');
//123
```

同时，我们可以向它添加两个属性——一个保存`MAX_VALUE`数值，另一个存储函数。我们能够在`Integer`函数上调用`isInteger`方法:

你觉得熟悉吗？

所有的内置函数(`Number`、`String`、`Boolean`、`Object`)都是这样的。

`Number`函数将字符串转换成数字，但是`Number`也是一个对象，有类似`isInteger`、`isNaN`等方法。它保存像`Number.EPSILON`或`Number.MAX_VALUE`这样的数据对象。它还有一个`Number.prototype`属性，保存用于所有数字的原型。

此时，我们可以说函数是一个可调用的对象。

```
const x = function(){ return 'Hi' };
x.type = 'Greeting';console.log(x());
console.log(x.type);
```

# 基元

以下内置类型是原语:`boolean`、`number`、`bigint`、`string`、`symbol`、`null`和`undefined`。

原语是对象吗？我们能给它们添加属性吗？

考虑下一个例子:

```
const text = "Hi";
text.type = 'greeting';console.log(text.type);
//undefined
```

我们不能添加、编辑或删除字符串等原语的属性，但是我们可以访问从`String.prototype`继承的所有方法，就像它们是对象一样。

每当我们试图使用访问操作符访问一个属性时，JavaScript 会临时将字符串值转换成一个`String`对象，并访问新创建的对象的属性:

```
'in bruges'.toUpperCase(); 
// "IN BRUGES"
```

在幕后，会发生这样的事情:

```
new String('in bruges').toUpperCase();
```

当试图访问字符串上的`toUpperCase`属性时，JavaScript 会围绕它构建一个临时包装器对象，并访问`toUpperCase`属性。访问该属性后，包装对象可以被销毁。

除了`null`和`undefined`之外的其他原语都有这些将原语转换为包装对象的内置函数:`Boolean`、`Number`、`BigInt`、`Symbol`。

# 空且未定义

[null 和 undefined](https://medium.com/programming-essentials/essential-things-on-undefined-and-null-178e2baa21b8) 仍然是原语，但不作为对象处理。

`null`和`undefined`不会被转换成对象包装器。试图访问这些原语的属性将会引发错误:

```
null.msg; 
// Uncaught TypeError: Cannot read property 'msg' of nullundefined.length; 
// Uncaught TypeError: Cannot read property 'msg' of undefined
```

当然，我们不能给这两个值添加属性。

```
const x = null;x.msg = 'Hi';
//Uncaught TypeError: Cannot set property 'msg' of null
```

尽管如此，JavaScript 是误导性的，它说`null`是一个对象:

```
typeof(null);
//'object'
```

# 最后的想法

函数和数组都是对象，但并不是 JavaScript 中的所有东西都是对象。

原语不是对象，但总的来说 JavaScript 试图让它们看起来像对象，并保持一切都是对象的假象。这种方法有两个例外:`null`和`undefined`。这两个值不被视为对象。基元值到包装器对象的转换发生在幕后，可能使基元看起来像对象，但是属性是在包装器对象上访问的。

总之，除了原语之外的所有值都是对象，甚至原语也试图看起来像 JavaScript 中的对象。

感谢阅读。