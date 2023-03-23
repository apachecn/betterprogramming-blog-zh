# JavaScript:普通函数和箭头函数有什么区别？

> 原文：<https://betterprogramming.pub/javascript-whats-the-difference-between-normal-and-arrow-functions-74c367324ae1>

## 提示:“这”是不一样的

![](img/36dc5a24be8552ce3548630d72740261.png)

由[émile Perron](https://unsplash.com/@emilep?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

从 ECMAScript 2015 开始采用了箭头函数。它非常强大和简单。

许多基于 ES5 的项目采用这个特性来重构代码；然而，箭头函数和你所知道的普通函数不是一回事。那么，有什么不同呢？

# 这个关键字

普通函数的`thisBinding`由调用函数的人决定。

```
var x = 10;
var obj = { 
  sayHi,
  x: 20
};function sayHi() {
  console.log(this.x);
}sayHi(); // 10
obj.sayHi(); // 20
```

`sayHi`给你一个不同的结果。这是因为普通函数的`this`被绑定到调用该函数的对象。

与普通函数相比，箭头函数的`this`总是绑定到内部函数周围的外部函数。

```
var x = 10;
var sayHi = () => {
  console.log(this.x);
}var obj = { 
  sayHi,
  x: 20
};sayHi(); // 10
obj.sayHi(); // 10
```

在这个例子中，`sayHi`是一个箭头函数。这是`obj`的属性。即使`obj`调用`sayHi`，它仍然打印`10`，因为函数的`this`总是引用外部环境的`this`。而全局`this`是`window`，所以指出`window.x`。

# 争论

一个普通的函数在被调用时有一个特殊的属性:参数。它包含许多有用的信息——传递给函数的参数、调用者等。当您不能保证将有多少个参数传递给函数时，这些参数非常有用。

```
function classroom() {
  console.log(`We have ${arguments.length} student(s) today`);
}// We have 3 student(s) today
classroom('Jane', 'Mark', 'James');// We have 1 student(s) today
classroom('Miller');
```

但是箭头函数没有 arguments 属性。

```
var classroom = () => {
  console.log(`We have ${arguments.length} student(s) today`);
}classroom('Jane', 'Mark', 'James');
// Uncaught ReferenceError:
// arguments is not defined
```

计算 arrow 函数时，ECMAScript 不会创建 arguments 属性。(参见[规格文件](https://www.ecma-international.org/ecma-262/6.0/#sec-arrow-function-definitions-runtime-semantics-evaluation)。)

# 约束

`Function.prototype.bind`是一种可以用来改变函数`this`的方法。

```
var name = 'Jane';function sayName() {
  console.log(this.name);
}sayName(); // Jane
sayName.bind({ name: 'James' })(); // James
```

`sayName`根据分配的`this`打印`Jane`和 `James`。

`Function.prototype.bind`返回一个新函数，所以你应该再次运行它来得到结果。

```
var name = 'Jane';var sayName = () => {
  console.log(this.name);
}sayName(); // Jane
sayName.bind({ name: 'James' })(); // **Jane**
```

但是箭头函数对`Function.prototype.bind`不起作用，因为它没有本地`thisBinding`。所以它的`this`只是看着外部环境的`this`。

# 构造器

当一个函数被调用时，这个函数是由一个内部核心方法用`kind`参数创建的，这个参数指的是它是一个什么样的函数。`kind`参数为`Normal`或`Arrow`。当一个箭头函数被调用时，`kind`是`Arrow`，所以它被归类为一个*非构造函数* *。*

```
function constructable() {}
var nonConstructable = () => {};new constructable(); // it works
new nonConstructable();
    ~~~~~~~~~~~~~~~~
// Uncaught TypeError
// nonConstructable is not a constructor
```

# 结论

箭头功能是一个方便的功能，并且更时尚，但是在普通功能和箭头功能之间存在差异。

我想告诉你，一个箭头函数并不总是你的最佳选择。你应该为每种情况选择合适的函数。

# 资源

*   [箭头函数表达式— MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)
*   [arguments 对象— MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/arguments)
*   [function . prototype . bind—MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_objects/Function/bind)
*   [箭头功能— ECMAScript 规范](https://www.ecma-international.org/ecma-262/6.0/#sec-arrow-function-definitions)
*   [函数环境记录— ECMAScript 规范](https://www.ecma-international.org/ecma-262/6.0/#sec-function-environment-records)
*   [箭头函数定义和运行时语义评估](https://www.ecma-international.org/ecma-262/6.0/#sec-arrow-function-definitions-runtime-semantics-evaluation)
*   [函数创建— ECMAScript 规范](https://www.ecma-international.org/ecma-262/6.0/#sec-functioncreate)