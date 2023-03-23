# 在 JavaScript 中定义和操作类和对象

> 原文：<https://betterprogramming.pub/defining-and-manipulating-classes-and-objects-in-javascript-7f49c706f386>

## 在基本面上刷新自己总是好的

![](img/50966b4ffb60e58823cc2b307cba822d.png)

照片由[欧文·史密斯](https://unsplash.com/@mr_vero?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在 JavaScript 中，对象是允许我们将数据、函数和其他对象封装到一个我们可以访问和操作的实体中的实体。这是 JavaScript 程序的另一个基本构件。

对象用于建模真实世界的数据。一只鸟就是一个例子。我们知道它有翅膀，一个头，两条腿和两个翅膀。此外，我们知道它会唧唧喳喳，吃东西，还会飞。

我们可以通过使用对象在 JavaScript 中对它们建模。主体部分是对象的属性，它所做的动作是方法。

方法是作为对象一部分的函数。属性是 JavaScript 中用键值对表示的任何东西。方法是特殊的属性。当一个属性有一个函数作为值时，它被称为方法。

例如，如果我们将一只鸟建模为一个对象，我们可以写:

```
const bird = {
  numWings: 2,
  numLegs: 2,
  numHeads: 1,
  fly(){},
  chirp(){},
  eat(){}
}
```

`fly(){}`、`chirp(){}`、`eat(){}`为方法，其余为以数据为值的属性。

# 创建对象

在 JavaScript 中，我们可以用三种方式定义对象。我们可以通过使用对象构造函数方法，或者通过使用类，将它们定义为对象文字。

## 对象文字

用对象文字定义对象很简单。我们只需要直接指定对象的属性和方法。例如，我们写道:

```
const bird = {
  name: 'Joe',
  numWings: 2,
  numLegs: 2,
  numHeads: 1,
  fly(){},
  chirp(){},
  eat(){}
}
```

将对象定义为对象文字。

如果我们想添加更多的属性，我们写:

```
bird.sleep = function(){};
```

我们可以将任何东西指定为对象属性的值，因此字符串、布尔值、函数、数组和其他对象都可以设置为属性。

## 对象构造函数

同样，我们可以使用对象构造函数方法来定义一个对象。我们可以写:

```
let bird = new Object();
bird.name = 'Joe';
bird.numWings = 2;
bird.numLegs = 2;
bird.numHeads = 1;
```

这是一种更长的定义对象的方式。它比对象文字更慢也更难阅读。

## 用类定义对象实例

我们也可以定义 JavaScript，首先定义一个类，然后使用`new`关键字创建一个对象，它是该类的一个实例。

为此，我们写道:

```
class Bird {
  constructor(name, numWings) {
    this.name = name;
    this.numWings = numWings;
  } logProperties() {
    console.log(this)
  }
}const bird = new Bird('Joe', 2);
bird.logProperties();
```

我们有了`Bird`类，这是一个用`name`和`numWings`属性和`getProperties`方法创建对象的模板。

`Bird`类有一个`constructor`函数，让我们传入属性值，并将其设置为被创建对象的属性。它还具有`logProperties`功能。

`this`被设置为创建的对象，因为我们没有使用箭头函数作为方法。

通过使用`new`关键字并将数据传入`constructor`来定义对象。一旦我们定义了对象，我们就可以调用它内部定义的方法。

在这个例子中，我们调用了`bird.logProperties`来记录`this`的值，应该是`{name: “Joe”, numWings: 2}`。

# 原型

在 JavaScript 中，原型是允许您创建其他对象的模板。它们也可以用于 JavaScript 中的继承。所以，如果我们有一个`Animal`对象定义为:

```
function Animal(){
  this.name = 'Joe'
}
```

我们可以通过创建一个新对象来扩展它，就像这样:

```
let bird = animal.prototype;
bird.fly = function() {};
bird.chirp = function() {};
```

然后我们得到了`fly`和`chirp`方法，以及`bird`对象中的`name`属性。

# 在对象中定义函数

我们可以用几种方法在对象中定义一个函数。

我们可以像往常一样使用`function`关键字或箭头函数，但是我们也可以用`function`关键字的简写来编写。例如，如果我们有一个`bird`对象，我们想定义`chirp`函数，我们可以写:

```
const bird = {
 chirp: function(){
   console.log('chirp', this)
  }
}
```

或者使用下面的简写:

```
const bird = {
 chirp(){
   console.log('chirp', this)
  }
}
```

两者相同，因为`chirp`函数会将`bird`对象作为`this`的值。

另一方面，如果使用箭头功能:

```
const bird = {
 chirp: ()=>{
   console.log('chirp', this)
  }
}
```

`this`将被记录为全局`window`对象，因为箭头函数不会改变函数所在对象的`this`值。

# 获取和设置对象属性

在定义了一个对象之后，能够获取和设置对象的属性是非常有用的。有两种方法可以获得对象的属性。一种是使用点符号，另一种是方括号符号。

点符号允许我们获得属性名符合变量命名约定的属性。这意味着它们以字母、下划线或美元符号开头，没有空格或其他特殊字符。

所以，如果我们有:

```
const bird = {
  name: 'Joe',
  numWings: 2,
  numLegs: 2,
  numHeads: 1,
  fly(){},
  chirp(){},
  eat(){}
}
```

然后，我们可以通过写`bird.name`来访问`name`属性。

另一种语法是方括号符号，它可以做与点符号相同的事情，也可以动态地访问属性。

它还允许我们访问用不遵循变量名创建规则的名称定义的属性。为了使用方括号符号来获取对象属性，我们编写:`bird['name']`。我们也可以写:

```
const prop = 'name';
bird[prop];
```

这允许我们通过传入变量和字符串来获取对象的属性，这对于动态修改对象和将对象用作字典非常方便，因为我们可以遍历键并修改它们和值。

为了用这两种表示法设置对象属性，我们使用赋值运算符，如下所示:

```
bird.name = 'Jane';
```

或者，用方括号符号，我们写:

```
bird['name']= 'Jane';
```

# 获取对象的所有顶级属性

所有 JavaScript 都有以下函数来获取对象的所有顶级属性。

## 对象.键

`Object.keys`获取一个对象的顶层键列表并返回一个数组。例如:

```
const a = {foo: 1, bar: 2};
const length = Object.keys(a).length // 2
```

您可以调用 keys 数组上的`find`来查找密钥，如下所示:

```
Object.keys(a).find(k => k == 'foo') // 'foo'
```

## Object.getPropertyNames

`Object.getPropertyNames`还获取一个对象的所有顶级键的列表，并以数组的形式返回它们。例如:

```
const a = {foo: 1, bar: 2};
const length = Object.getOwnPropertyNames(a).length // 2
```

您可以调用 keys 数组上的`find`来查找密钥，如下所示:

```
Object.getOwnPropertyNames(a).find(k => k == 'foo') // 'foo'
```

## 对于…在循环中

还有一个特殊的循环，用于遍历对象的键。您可以执行以下操作:

```
const a = {foo: 1, bar: 2};
let keysCount = 0;
for (let key in a) {
    keysCount++;
}
console.log(keysCount) // 2
```

# 检查对象属性是否存在

## hasOwnProperty

您可以通过调用对象的`hasOwnProperty`来检查对象是否有属性。例如:

```
const a = {foo: 1, bar: 2};
a.hasOwnProperty(key); // true
```

# 删除对象的属性

JavaScript 有一个`delete`操作符来删除对象的属性。如果我们有:

```
const bird = {
  name: 'Joe',
  numWings: 2,
  numLegs: 2,
  numHeads: 1,
  fly(){},
  chirp(){},
  eat(){}
}
```

然后我们运行`delete bird.name`，那么`bird.name`属性将被移除。当您记录它时，它将记录为`undefined`。

# JavaScript 继承

在 JavaScript 中，我们可以创建一些类，这些类的属性可以包含在子类的属性中。

因此，我们可以有一个包含所有子类共有的属性的高级类，并且子类可以有自己的特殊属性，这些属性不在任何其他类中。

例如，如果我们有一个具有公共属性和方法的`Animal`类，比如`name`和`eat`方法，那么`Bird`类可以继承`Animal`类中的公共属性。它们不必在`Bird`类中再次定义。

我们可以用 JavaScript 编写以下代码来实现继承:

```
class Animal {
  constructor(name) {
    this.name = name;
  }
  eat() {
    console.log('eat');
  }
}class Bird extends Animal {
  constructor(name, numWings) {
    super(name);
    this.numWings = numWings;
  }
}const bird = new Bird('Joe', 2);
console.log(bird.name)
bird.eat();
```

在上面的例子中，我们有父类`Animal`，它有`eat`方法，来自`Animal`的`extends`的所有类都会有，所以它们不必再定义`eat`。

我们有扩展了`Animal`类的`Bird`类。注意，在`Bird`类的`constructor`中，我们有`super()`函数调用来调用父类的构造函数，以填充父类的属性和子类的属性。

# `this`关键字

关键字`this`允许我们在一个对象中访问当前对象的属性，除非你使用了箭头函数。

从上面的例子中我们可以看到，我们可以在对象中获得子类和父类的实例的属性。

# 复制对象

复制对象意味着对与原始对象具有相同内容的对象进行新的对象引用。

当你把一个变量赋给另一个变量时，它经常被用来防止修改原始数据。因为如果你把一个变量赋给一个新的变量，这个新的变量和原来的对象有相同的引用。

用 JavaScript 克隆对象有几种方法。一些函数进行浅层复制，这意味着不是对象的所有级别都被复制，所以它们可能仍然保存对原始对象的引用。

深层拷贝复制所有内容，这样就没有任何内容引用原始对象，消除了浅层拷贝带来的任何混乱。

如果将一个对象赋给另一个变量，它只是将引用赋给原始对象，所以两个变量都将指向原始对象。

当其中一个变量被操作时，两个变量都将被更新。这并不总是想要的行为。为了避免这种情况，您需要将一个对象从一个变量复制到另一个变量。

## 浅拷贝

在 JavaScript 中，这很容易做到。要浅层复制一个对象，我们可以使用`Objec.assign()`，它内置于最新版本的 JavaScript 中。

这个函数执行浅层复制，这意味着它只复制一个对象的顶层，而更深层的对象仍然链接到原始对象引用。如果在原始对象中有嵌套对象，可能不希望出现这种情况。

下面是一个如何使用`Object.assign`的例子:

```
const a = { foo: {bar: 1 }}
const b = Object.assign({}, a) // get a clone of a which you can change with out modifying a itself
```

您也可以像这样克隆一个阵列:

```
const a = [1,2,3]
const b = Object.assign([], a) // get a clone of a which you can change with out modifying a itself
```

## 深层拷贝

要做一个没有库的对象的深层拷贝，你可以`JSON.stringify`然后`JSON.parse`:

```
const a = { foo: {bar: 1, {baz: 2}}
const b = JSON.parse(JSON.strinfy(a)) // get a clone of a which you can change without modifying a itself
```

这是对对象的深层复制，这意味着对象的所有级别都被克隆，而不是引用原始对象。

`JSON.parse`和`JSON.stringify`只处理普通对象，这意味着它们不能有函数和其他运行的代码。

我们也可以对浅层克隆对象使用对象析构，就像这样:

```
const a = { foo: {bar: 1}}
const b = {...a} // get a clone of a which you can change with out modifying a itself
```

既然我们知道如何创建对象，我们就可以轻松地存储和操作数据。我们可以通过使用对象和类创建程序来做一些不平凡的事情。