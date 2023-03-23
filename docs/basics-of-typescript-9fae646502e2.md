# 打字稿的基础

> 原文：<https://betterprogramming.pub/basics-of-typescript-9fae646502e2>

## 什么是 TypeScript，为什么要尝试它？

![](img/c70b5dc931a6e60a421ffc025c78a1ba.png)

照片由[阿诺·弗朗西斯卡](https://unsplash.com/@clark_fransa?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

Typescript 是编译成普通 JavaScript 的 JavaScript 的超集。顾名思义，typescript 为我们提供了一种编写具有类型安全的 JavaScript 的方法，就像您使用其他强类型语言(如 C#或 Java)一样。

*JavaScript 是一个有效的类型脚本，类型是可选的，因此人们可以很容易地将 JavaScript 项目迁移到类型脚本，并逐步考虑类型安全。*

它为我们提供了编译时类型安全和 ide 中更好的智能感知。这有助于你尽早发现错误，提高工作效率。

*告别无模式对象。向自我记录的输入和输出对象问好。*

# 键入注释

所有变量、函数参数和函数返回类型都是使用 typescript 中的类型批注定义的，如下所示:

```
**const** a: **number** = 124;
**const** b: **number** = 56;

**function** sum(a: **number**, b: **number**): **number** {
  **return** a + b;
}
```

## 原始类型

JavaScript 原始类型 ie `number, string, boolean`都可以用作 typescript 中的内置类型。

```
**let** a: **number**;
a = 5;
a = '5'; // error

**let** b: **string**;
b = '100';
b = 100; // error

**let** c: **boolean**;
c = **true**;
c = 'true'; // error
```

## 数组

如果我们想将一个变量定义为一个数组，我们可以简单地在`[]`后面加上任何有效的类型注释。这允许我们安全地操作数组对象，而不用担心给数组成员分配错误的类型。下面演示了这一点:

```
**let** numArray: **number**[] = [ 24, 57, 100 ];

numArray[0] = 10; // ok
numArray[1] = '10'; // error
```

## 接口/用户定义的类型

我们可以借助如下所示的*类型*或*接口*在 JavaScript 中定义 schemas 复杂对象:

```
**type** point = {
  x: **number**;
  y: **number**;
}

**let** a: point = {
  x: 1,
  y: 1
};

a.x = 0;
a.y = '1'; // error
a.z = 2; //error**interface** point2 {
  x: **number**;
  y: **number**;
}

**let** b: point2 = {
  x: 1,
  y: 1
};

b.x = 0;
b.y = '1'; // error
b.z = 2; //error
```

类型和接口都可以用来定义类型。它们都可以通过类来扩展/实现。接口和类型的详细区别可以在这里阅读[。](https://stackoverflow.com/a/52682220)

**可选属性**

我们可以简单地将`?`作为变量的后缀，我们知道这个变量是可选的，如下所述:

```
**type** point = {
  x: **number**;
  y: **number**;
  z?: **number**;
}

**let** a: point = { x: 1 }; // error missing property 'y'

**let** b: point = { x: 1, y: 1 }; // ok since z is optional
```

## 内嵌注释

不是为每个对象创建一个新的*类型*或*接口*，我们可以定义它的结构*内联*，如下所示:

```
**let** student: {
  age: **number**;
  name: **string**,
};

student = {
  age: 15,
  name: 'John doe'
};

**function** getAge(student: { age: **number** }) {
  **return** student.age;
}

console.log(getAge(student)); // 15
```

## 特殊类型

*   `any` →我们可以将变量的类型定义为 *any* ，表示任何东西都可以赋给它。应该避免使用这种方法，因为这违背了定义类型的目的。
*   `null`和`undefined` →除非 ts 编译器选项中的`strictNullChecks` 标志为`true`，否则我们可以将 null/undefined 赋给任何其他类型。
*   `:void` →当函数没有返回类型时使用。

## 无商标消费品

一些功能/算法不依赖于对象的实际类型。例如，一个函数接受一个项目列表和一个谓词，以返回一个基于谓词的过滤列表。

```
**type** student = {
  name: **string**,
  age: **number** }

**const** students: student[] = [
  {
    age: 17,
    name: 'John doe'
  },
  {
    age: 15,
    name: 'Jane doe'
  }
];

**function** filter<T>(list: T[], predicate): T[] {
  **return** list.filter(predicate);
}

console.log(filter(students, (s: student) => s.age > 15));
// [ { age: 17, name: 'John doe' } ]
```

这样，我们在返回的列表中也获得了类型安全。事实上，由于 JavaScript 已经有了对数组的过滤功能，因此在内部 typescript 使用泛型来定义其结构。

## 联合类型

在某些情况下，我们希望变量是多种类型中的一种，例如一个`string`或一个`number`。我们可以简单地这样定义它:

```
**let** a: **number** | **string**;
a = 100; // ok
a = '100'; // ok
```

## 交叉类型

在某些情况下，我们希望将两个对象的属性合并成一个新的对象。交集类型允许我们以一种类型安全的方式实现这一点，如下所示:

```
**function** extend<T, U>(a: T, b: U): T & U {
  **return** {
    ...a,
    ...b
  };
}

**const** test = extend({ name: 'John' }, { age: 25 });
console.log(test);
//{ name: 'John', age: 25 }
```

## 元组类型

在 JavaScript 中，我们通常使用数组作为元组。我们可以简单地在 typescript 中将元组定义为`:[typeOfElement1, typeOfElement2]`。

```
**let** a: [**string**, **number**];
a[0] = '100'; // ok
a[1] = 100; // ok

a[1] = '100'; //error
a[0] = 100; // error
```

## 枚举

枚举是相关值的集合。JavaScript 不支持将*枚举*作为数据类型。，但是 typescript 有。

```
**enum** WeekDay {
  *monday*,
  *tuesday*,
  *wednesday*,
  *thursday*,
  *friday*,
  *saturday*,
  *sunday* }

**function** isWorkingDay(day: WeekDay): **boolean** {
  **switch** (day) {
    **case** WeekDay.*saturday*:
    **case** WeekDay.*sunday*:
      **return false**;
    **default**:
      **return true**;
  }
}

**const** mon = WeekDay.*monday*;
**const** sat = WeekDay.*saturday*;
console.log(isWorkingDay(mon)); // true
console.log(isWorkingDay(sat)); // false

**enum** Color {
  *RED* = 'red',
  *GREEN* = 'green',
  *BLUE* = 'blue',
}

// string enums can be used for comparisons
**if** (someStringFromDb === Color.*GREEN*) {}
```

# 功能

我们可以定义函数的参数和返回类型，如下所示:

```
**interface** point {
  lat: **number**,
  long: **number** }

**function** getPoint(lat: **number**, long: **number**): point {
  **return** {
    lat: lat,
    long: long
  }
}
```

即使我们没有定义函数的返回类型，typescript 也足够聪明，可以自己推断出返回类型。所以，下面也行得通:

```
**function** getPoint(lat: **number**, long: **number**) {
  **return** {
    lat: lat,
    long: long
  }
}
```

但是总是建议定义返回类型以避免类似这样的错误:

```
**function** getPoint(lat: **number**, long: **number**) {
  **return** {
    lat: lat,
    lnog: long // no error since we left it to typescript to infer the return type
  }
}
```

## 可选参数

我们可以将参数定义为可选的:

```
**function** foo(bar: **string**, baz?: **number**) {
  // baz is optional
}

foo('str'); // ok
foo('str', 1); // ok
```

或者，我们可以为参数设置默认值:

```
**function** foo(bar: **string**, baz: **number** = 1) {
  console.log(bar, baz);
}

foo('str'); // str, 1
foo('str', 100); // str, 100
```

## 函数重载

Typescript 允许我们*声明*重载。这对于文档和安全目的是有用的。但是由于 JavaScript 不支持函数重载，我们不得不*为所有重载实现*一个通用函数。

```
**function** stringOrNumber(a: **string**): **string**;
**function** stringOrNumber(a: **number**): **number**;

**function** stringOrNumber(a: **any**) {
  **if** (**typeof** a === 'string') {
    // typescript knows 'a' is a string in this block
    // let b = a * 2; // error

    **return** a;
  } **else if** (**typeof** a === 'number') {
    // typescript knows 'a' is a number in this block
    // let b = a.substr('') // error

    **return** a * 2;
  }
}

stringOrNumber('hello'); // hello
stringOrNumber(100); // 200
stringOrNumber(**true**); // error
```

## 声明函数

有两种方法可以让*声明*一个没有实现的函数。

```
**type** longHand = {
  (a: **number**): **number** }

**type** shortHand = (a: **number**) => **number**;
```

如果我们想要声明函数重载，那么速记和手写声明的唯一区别就来了。例如:

```
**type** longHand = {
  (a: **number**): **number**,
  (a: **string**): **string**,
}
```

# 名称空间

命名空间可用于函数的逻辑分组，如下所示:

```
**namespace** Logger {
  **export function** info(message: **string**) {
    console.log(message);
  }

  **export function** error(message: **string**) {
    console.error(message);
  }
}

Logger.info('info text');
Logger.error('error text');
```

这种逻辑分组也可以通过基于文件的模块来实现，建议通过命名空间来实现。

# 班级

ES6 类和 typescript 类之间的唯一区别是，typescript 为类变量提供了类型安全和访问说明符。

访问说明符只提供编译时安全，因为编译后的 JavaScript 不支持它们——与类型安全相同。

```
**class** Animal {
  **private readonly** name: **string**;
  **protected** isPet: **boolean**;

  **constructor**(name: **string**, isPet: **boolean** = **false**) {
    **this**.name = name;
    **this**.isPet = isPet;
  }

  **public** isPetAnimal() {
    **return this**.isPet;
  }

  **protected** getName() {
    **return this**.name;
  }
}

**class** Snake **extends** Animal {
  **public** getName() {
    // return a.name; // error since name is private in parent class
    **return super**.getName();
  }
}

**const** animal = **new** Animal('tom', **true**);
console.log(animal.isPetAnimal()); // true
// animal.getName(); // error since its protected

**const** snake = **new** Snake('kaa');
console.log(snake.isPetAnimal()); // false
console.log(snake.getName()); // kaa
```

# 结论

Typescript 为我们提供了一种编写具有编译时类型安全的 JavaScript 的方法，同时还支持 JavaScript 中不支持的枚举、访问说明符等结构。

**参考文献**:【https://basarat.gitbooks.io/typescript/ 