# ES6 快速开发指南—第 2 部分

> 原文：<https://betterprogramming.pub/quick-es6-development-guide-part-2-c782740064fc>

## 粗箭头函数、析构、扩展操作符等等

![](img/5c66a5b1cb1b19a400cc74b1a4695940.png)

这篇文章是 ES6 系列文章的第 2 部分。如果您跳过了第 1 部分，您可以在[媒体](https://medium.com/better-programming/quick-es6-development-guide-9b9cd9894a7)上观看。虽然本文涉及的主题与第 1 部分无关，但建议先阅读第 1 部分。

在本帖中，我们将讨论更多真正有用的 ES6 特性。如果你掌握了这些，你就可以跳到 Angular 2+里，反应原生开发。开始吧！

# 粗箭头函数

如果你已经在使用 ES6 中的胖箭头功能，你就知道它们是多么的简单和神奇。首先，让我们看一个使用 ES5 的简单例子:

匿名函数:没有名字的函数。

例如:

```
setTimeout(function() {
    console.log('I am here after 3 light-years ;)');
}, 3000);
```

让我们给这个匿名函数起个名字:

```
function myFunction() {
    console.log('I am your function');
}
setTimeout(myFunction, 3000);
```

上面的函数有一个名字，`myFunction`。注意`setTimeout`函数的参数。我们将函数作为参数传递，因为 JavaScript 函数是一级函数。这意味着一个函数可以接受另一个函数作为它的参数。

ES6 引入了胖箭头函数，这是一种稍微短一点的编写匿名函数的方式。

> 箭头函数表达式的语法比[函数表达式](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/function)短，并且没有自己的 [this](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/this) 、 [arguments](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/arguments) 、 [super](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/super) 或 [new.target](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/new.target) 。这些函数表达式最适合于非方法函数，它们不能用作构造函数。— Mozilla 文档

由于箭头函数没有自己的`this`，输出与函数表达式不相似。示例:

```
var compareObj= {
  age: 26,
  ageArrow: () => console.log(this.age, this),
  ageExpression: function() {
    console.log(this.age, this);
  }
} >   compareObj.ageArrow();
 //   undefined
Window      

>  compareObj.ageExpression();
// 26 {age: 26, ageArrow: ƒ, ageExpression: ƒ}
```

上面的`setTimeout`匿名函数也可以使用粗箭头函数编写:

例子一:

```
setTimeout(() => {
     console.log("I am here after 3 light-years ;)");
}, 3000);
```

例子二:简单的`sum`函数遗留声明和粗箭头表示。

```
const sumFun = function(arg1, arg2, arg3) {
    return arg1 + arg2 + arg3;
};
```

粗箭头表示:

```
const sumFun = ( arg1, arg2, arg3 ) =>  arg1 + arg2 + arg3;
```

或者，对于函数中的多行语句，需要使用`return`关键字显式返回，如下例所示。如果是一行语句，它会自动返回(如上例所述)。

```
const sumFun = ( arg1, arg2, arg3 ) =>  {
    return arg1 + arg2 + arg3;
}
```

# 解构

析构是一种将数组和对象中的值直接提取到变量中的方法。让我们用一个例子来理解这一点:

```
const modusEmployee = {
    name: ‘Nikhil’,
    country: ‘India’,
    city: ‘New Delhi’,
    nativeLanguage: ‘Hindi’
};
```

在 ES5 中，我们可以这样提取对象值:

```
const empName = modusEmployee.name;
// Nikhil
const empCity = modusEmployee.city;
// New Delhi
```

在 ES6 中，它得到了改进:

```
const { name, city } = modusEmployee;
console.log(name);
	// Nikhil
console.log(city);
	// New Delhi
```

这里，我们使用别名作为属性名。

```
const { name: empName, city: empCity } = modusEmployee;
console.log(empName);
// Nikhil
console.log(empCity);
// New Delhi
```

析构可以以下列方式使用:

*   对象析构。
*   数组析构。
*   函数参数析构。

我们已经讨论了对象析构，让我们看看数组析构是如何工作的。

数组析构非常类似于对象析构。不同之处在于，它根据索引提取值。

```
const myCoins = [ 1,2,5,10 ];
const [ coin1, coin2 ] = myCoins;
console.log(coin1)
// 1
console.log(coin2);
// 2
```

## **奖励积分**

如果您只想要第三个值:

```
const [ , , coin3 ] = myCoins;
console.log(coin3);
// 5;
```

如果希望一个变量中包含所有值:

```
const [ ...all ] = myCoins;
console.log(all);
 // [1,2,5,10]
```

如果您想获得其余的值:

```
const myCoins = [ 1,2,5,10 ];
const [ coin1, ...otherCoins ] = myCoins;
console.log(coin1);
// 1
console.log(otherCoins);
// [ 2, 5, 10 ]
```

没有可用于析构的值时的处理条件:默认值。当要析构的属性未定义时，可以提供默认值:

```
const [ random = ‘not found’ ] = [ ‘Available’ ];
	console.log(random);
// Availableconst  [ random = ‘not found’ ] = [];
	console.log(random);
	// not found
```

# 面向对象:类

JavaScript 是一种基于原型的编程语言，在 ES5 中没有可用的关键字`class`。这并不意味着我们不能在 ES5 中使用面向对象编程来编码。

我们可以通过用原型方法创建一个对象来模仿 ES5 中的面向对象编程。也可以通过使用这些对象来实现继承。这就是我们如何用 ES5 创建一个`class`:

```
var Automobile = function (engineSound) {
    this.engineSound = engineSound;
} Automobile.prototype.engineSoundFun = function() {
    console.log(this.engineSound)
};var enfield = new Automobile(‘dhuk dhuk’);enfield.engineSoundFun();Output:
// dhuk dhuk
```

如果您熟悉 C++或 Java，您可能会想:“上面的例子中没有使用`class`关键字，那么面向对象在哪里？”

作为其他语言中经典的面向对象模式，ES6 中的语法变得更容易理解。ES6 引入了`class`关键字，使其更容易理解。

ES6 类支持基于原型的继承、超级调用、实例和静态方法以及构造函数。

让我们以 ES6 的方式来做上面的例子:

```
class Automobile {
    constructor(engineSound) {
        this.engineSound = engineSound;
    } engineSoundFun() {
        console.log(this.engineSound);
    }
}const enfield = new Automobile(‘Dhuk Dhuk’);

enfield.engineSoundFun()Output:
// Dhuk Dhuk
```

注意，在 ES6 中，代码更干净、更容易，也更熟悉。然而，原型模式仍然可以在 ES6 中使用。

# 模板文字

记住，使用`console.log`和变量串联调试代码:

```
var name = ' Welcome! ' + first + ' ' + last + '.';
console.log(‘Hi ‘ + name + ‘ your username is ‘ + username):
```

幸运的是，在 ES6 中，我们可以在反勾字符串中使用新的语法`${first}`来清理它:

```
const name = ` Welcome! ${first} ${last}.`;
console.log(`Hi ${name} your username is: ${username}`);
```

之前的另一个问题是使用*多行字符串*，使用的新行语句是用特殊字符打印的，如`\n`:

```
var message = ‘Individuals in semler project \n are not just developers or testers, they are \n good friends’;
```

现在，使用带反勾号的模板文字可以像您预期的那样工作:

```
const message = `Individuals in semler project are not just developers or testers, they are
good friends`;
```

# 传播算子

如果你知道 JavaScript 中的*深度复制*和*浅度复制*的概念(看一下下面这篇文章的附加部分)，你就会明白，有时候，分析对象复制和它们的使用之间发生了什么变得很困难。

代码说话，让我们用一个例子来理解这个场景(没有展开操作符):

```
const myCurrencyNotes = [1, 5, 10, 1000]; 
const piratedNotes = myCurrencyNotes;console.log(piratedNotes);Output: [1, 5, 10, 1000]> piratedNotes.push(50);console.log(piratedNotes);Output:
[1, 5, 10, 1000, 50]Now, when you log:> console.log(myCurrencyNotes);Output:
[1, 5, 10, 1000, 50]
```

我们知道在 JavaScript 中处理对象时(数组是对象的一种类型)，我们通过引用赋值，而不是通过值。

spread 运算符的用法也是如此:

```
const myCurrencyNotes = [1, 5, 10, 1000];
const piratedNotes = [ ...myCurrencyNotes ];console.log(piratedNotes);Output:
[1, 5, 10, 1000]> piratedNotes.push[50,100]> console.log(myCurrencyNotes);Output:
 [1, 5, 10, 1000]
```

请注意这里的区别，当我们推送`piratedNotes`数组中的元素时，原始数组`myCurrencyNotes`不受影响。

传播的另一个用例是:

```
var newCurrencyNotes = [ 50,100 ];
var myAllCurrencyNotes = [ 1, 5, 10, newCurrencyNotes, 1000 ];console.log(myAllCurrencyNotes);Output:[1, 5, 10, Array(2), 1000].0:1
1:5
2:10
3:(2) [50, 100]
4:1000
```

您可能不希望输出中出现第三步，因为它变成了一个嵌套数组。为了消除这样的问题，我们可以利用扩展运算符:

```
const myAllCurrencyNotes = [1, 5, 10, ...newCurrencyNotes, 1000]
console.log(myAllCurrencyNotes);Output:[1, 5, 10, 50, 100, 1000]
```

功能分布:

```
function checkFakeCurrencyNote(...notesVariations) {
    if (notesVariations.includes(1000)) {
        return ‘Contains an invalid currency denomination’;
    } else{
        return ‘Aha! nice currency’;
    }
}const currency = [ 1, 10, 1000 ];
	> checkFakeCurrencyNote(...currency);

Output:
	"Contains an invalid currency denomination"
```

# 承诺

对象在 JavaScript 中随处可见。承诺也是一个在未来返回其价值的对象。当您要执行异步操作时，使用承诺非常有用。一个承诺可以被解决，也可以被拒绝。

JavaScript 中的承诺类似于我们现实生活中的承诺。假设你邀请你的朋友参加一个聚会(请求已发送)，他们告诉你他们可能会组织聚会(`resolve`)或者不会(`rejected`)。

如果承诺被称为已解决，您可以使用`.then()`进一步处理它。如果承诺被拒绝，可以在`.catch()`中处理，或者作为`.then()`中的第二个参数处理。

可以用`new Promise`做出承诺。这个 Promise 构造函数接受一个包含两个参数的函数— `resolve`和`reject`。创造承诺的基本框架是:

```
new Promise((resolve, reject) => {
    /* Do something here */
});
```

在`.then()`和`error`的情况下，承诺以名称`promise`创建，正如我们在上面已经讨论过的:

```
promise.then((resolvedValue) => {
    console.log(resolvedValue);
}, (error) => {
    console.log(error);
});
```

让我们通过一个完整的片段来理解这一点:

```
const yourAge = window.prompt(‘Enter your age’, 13);
console.log(`Entered age is ${yourAge}`);
```

变量`yourAge`被声明，并准备在浏览器提示中存储用户的随机输入值。我们将在程序的后面使用这个随机值:

```
new Promise((resolve, reject) => {
    If ( yourAge < 99  ) {
        resolve("Congrats!! you are still young Carlos");
    } else {
        reject(new Error(‘Now at this stage, you should start eating tons of veggies’));
    }
});
```

这里，我使用新的`Promise`初始化了一个承诺，并传递了两个参数。嵌套逻辑比较变量`yourAge`的值。如果值小于 99，promise 返回`resolve`，否则拒绝并错误输出。

如果我们简单地用一个正的 case 值`i.e value < 99`运行上面的代码片段，我们将得不到任何输出，如果我们提供一个负的 case 值`i.e value > 99`，我们将得到一个`Uncaught`错误:

```
Uncaught (in promise) Error:  Now at this stage, you should start  eating tons of veggies
    at Promise (:5:13)
    at new Promise ()
    at :1:17
```

注意:虽然您可以从`reject`中删除`new Error`并简单地返回一个类似`resolve`的字符串，但最好是出错拒绝，但这不是必需的。

```
reject(‘Now at this stage, you should start eating tons of veggies’);
```

但是，如果您的输入是一个负的 case 值，您仍然会得到:

```
Uncaught (in promise)
Now at this stage, you should start eating tons of veggies
```

这些反应是意料之中的。如果你看到上面的例子，我们必须使用`.then`和`.catch`来获得预期的响应。

```
promise.then((resolvedValue) => {
    console.log(resolvedValue);
}, (error) => {
    console.log(error);
});
```

假设您提供 89 作为输入值，输出将是:

```
Congrats!! you are still young CarlosPromise {: undefined}
__proto__: Promise
[[PromiseStatus]]: "resolved"
[[PromiseValue]]: undefined
```

对于负案例输入，假设您提供 120:

```
Now at this stage, you should start  eating tons of veggiesPromise {: undefined}
__proto__:Promise
[[PromiseStatus]]:"resolved"
[[PromiseValue]]:undefined
```

# 好处:JavaScript 中的浅层和深层复制

一个*浅拷贝*意味着两个对象的内存位置是相同的。因此，如果您更改副本对象的属性值，原始对象的属性值也会在对象的深层发生变化。

浅层副本将复制顶级属性，但嵌套对象在原始对象(源)和副本对象(目标)之间共享。

但是，在*深度复制*中，存储位置将会不同/分开。更改 copies 对象的属性值不会更改原始对象。

浅层复制示例:`Object.assign()`方法用于将属性的值从一个或多个源对象复制到目标对象:

```
let oldObj = {
    id:1
}> oldObj
  {id: 1}

let newObj = { ...oldObj };> newObj
  {id: 1}> newObj.id = 9;

> newObj
   {id: 9}> oldObj
    {id: 1}
```

在此之前，一切都很好，但是，根据声明，“浅层复制将复制顶级属性。”让我们用一个例子来理解这一点:

```
let oldObj = {
  a: 1,
  b: {
    c: 2,
  },
}
let newObj = Object.assign({}, oldObj );
>newObj
 { a: 1, b: { c: 2} }oldObj.a = 10;> oldObj 
// { a: 10, b: { c: 2} }>newObj 
// { a: 1, b: { c: 2} }newObj.a = 20;
>oldObj
 // { a: 10, b: { c: 2} }> newObj
 // { a: 20, b: { c: 2} }newObj.b.c = 30;
> oldObj
 // { a: 10, b: { c: 30} }> newObj
// { a: 20, b: { c: 30} }
```

为了解决这个问题，我们进行了深入的复制，检查如下。感谢我的同事 Mitchell 为我简化了这个。深层拷贝示例:

```
const oldObj = {
    id: 1,
    foo: {
        bar: 8
    }
};> const newObj  = JSON.parse(JSON.stringify(oldObj));
> newObj.foo.bar = 10
// 10> oldObj.foo.bar;
// 8
```

# 下一步是什么

我将在这里更新我即将发布的帖子的链接。在那里，我们将了解用户如何访问您的应用程序，即使他们的网络连接性很差或者根本没有网络。

我将分享您的 web 应用程序如何在三秒钟内加载(这是一个 web 应用程序的性能基准标准)。我还将讨论如何在移动设备上使用您的 web 应用程序，比如带有应用程序图标的混合移动应用程序。所以，请继续关注，还有更多精彩。

为了更好地理解 ES6，我建议阅读本主题的第[部分](https://moduscreate.com/blog/quick-practical-guide-for-es6/)。

我希望这是一本好书。请分享您的反馈和建议。

编码快乐！