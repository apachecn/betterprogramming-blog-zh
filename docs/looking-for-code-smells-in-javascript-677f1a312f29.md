# 在 JavaScript 中寻找代码味道

> 原文：<https://betterprogramming.pub/looking-for-code-smells-in-javascript-677f1a312f29>

## 这总是清理重复代码、复杂代码和更多反模式的好时机

![](img/143f7d29be3b171c60d08ac68924ee43.png)

照片由[奥利弗·黑尔](https://unsplash.com/@4themorningshoot?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/cleaning?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

在编程中，代码气味是一段代码的特征，表明可能有更深层次的问题。这是一种主观特征，用于通过观察代码来判断代码是否质量良好。

在 JavaScript 中，代码味道仍然适用。因为有旧的构造应该被消除，所以查看旧代码可能会产生额外的问题。

在本文中，我们将使用 JavaScript 代码作为例子来研究一些代码味道。我们看到的代码味道包括重复代码、复杂代码、猎枪手术和太大的类。

# 常见代码气味

## 重复代码

重复代码是在不同地方重复做同一件事的代码。非常相似的代码段也可能被认为是重复的。

这是一个问题，因为当我们必须改变重复的代码时，我们必须改变多段代码。此外，很容易忘记其他开发人员也在那里。

这也是为什么不重复自己(干)原则被强调很多的原因。它节省了开发人员的时间。

例如，如果我们有

```
let numApples = 1;
```

在一个代码文件中，如果我们想得到苹果的数量，我们可以在任何地方使用它。

如果我们有重复运行多次的代码，那么我们使用一个循环。如果我们想在多个地方运行一段代码，但不重复，那么我们可以编写一个函数并运行它。

例如，如果我们有这样的东西:

```
let str = '';
str += 'foo';
str += 'bar';
str += 'baz';
```

我们应该改为写:

```
const arr = ['foo', 'bar', 'baz'];
let str = '';
for (let word of arr) {
  str += word;
}
```

如果我们想让代码在多个模块中可用，我们导出它，然后导入到其他地方。

例如，我们可以在一个名为`module.js` 的 JavaScript 模块中编写以下代码，并在其他地方导入`foo`函数:

```
export foo = () => 'foo';
```

并将其命名为:

```
import { foo } from 'module';
const bar = foo();
```

# 太复杂的代码

如果有更简单的解决方案，我们应该去做，而不是写一些更复杂的东西。

简单的解决方案易于阅读和更改。

例如，如果我们有:

```
let str = '';
str += 'foo';
str += 'bar';
str += 'baz';
```

要创建字符串`'foobarbaz'`，我们可以将其简化为:

```
let str = ['foo', 'bar', 'baz'].join('');
```

# 猎枪手术

霰弹枪手术是一种改变，需要代码是多段代码才能改变。

这是一个问题，因为需要在多个地方进行更改，这使得更改变得困难。也很容易错过多个地方。

这通常是由于在错误的地方添加了在多个地方运行的代码。通常有比编写在多个地方使用的代码更好的方法来修改代码。

例如，如果我们有一个 Express 应用程序，我们想在其中添加日志记录，我们不应该执行以下操作:

```
const express = require('express');
const bodyParser = require('body-parser');const app = express();app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: true }));app.get('/foo', (req, res) => {
  console.log('foo called');
  res.json({ message: 'foo' });
});app.get('/bar', (req, res) => {
  console.log('bar called');
  res.json({ message: 'bar' });
});app.get('/baz', (req, res) => {
  console.log('baz called');
  res.json({ message: 'baz' });
});app.listen(3000, () => console.log('server started'));
```

上面的代码不好，因为我们必须给每条路线添加`console.log`。

相反，我们应该将`console.log`重构为一个路由中间件，它在代码路由代码运行之前运行:

```
const express = require('express');
const bodyParser = require('body-parser');const app = express();app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: true }));app.use((req, res, next) => {
  if (req.path.substring(1)) {
    console.log(`${req.path.substring(1)} called`);
  }
  next();
});app.get('/foo', (req, res) => res.json({ message: 'foo' }));app.get('/bar', (req, res) => res.json({ message: 'bar' }));app.get('/baz', (req, res) => res.json({ message: 'baz' }));app.listen(3000, () => console.log('server started'));
```

正如我们所看到的，上面的代码更加简洁，它实现了和以前一样的东西，除了我们只有一个`console.log`，而不是每条路线一个。

如果我们有更多的路由，那么更改日志代码将会更加困难。例如，如果我们想用某个日志库替换`console.log`，那么如果我们将日志代码放入每条路由中，就会变得更加困难。

# 大班

任何大的类都很难维护。类应该创建只做一件事的对象。我们不希望班级太大，以至于他们要做很多事情。

一个太大的物体被称为上帝物体。上帝是一个做得太多的物体。它非常大，而且它做许多事情。

每堂课只应该解决小问题。如果他们很大，那么他们可能做得太多了。

另一个可能由过大的类引起的问题是类的不同部分相互依赖。在整理代码时，这种紧密耦合会带来很多麻烦。

为了解决这个问题，我们应该把大班分成做不同事情的小班。它们之间的通用功能可以被重构到一个父类中。

例如，如果我们有下面的`Employee`类:

```
class Employee {
  eat() {
    //...
  }
  drink() {
    //...
  }
  work() {
    //...
  }
  sleep() {
    ///...
  }
  walk() {
    //...
  }
}
```

这是一个做得太多的类的例子，因为它有与雇员不直接相关的方法。

相反，我们可以将该类分为两个类，如下所示:

```
class Person {
  eat() {
    //...
  }
  drink() {
    //...
  }
  sleep() {
    ///...
  }
  walk() {
    //...
  }
}class Employee extends Person {
  work() {
    //...
  }
}
```

现在`Employee`只有与员工相关的方法，那就是`work`方法。我们将所有人通用的方法移到了`Person`类中，这样我们就可以重用它们并清理`Employee`类。

遵循一些基本原则，清理代码并使其易于阅读和更改是很容易的。首先，将重复的代码移动到一个公共位置，并重用公共位置的代码。第二，不要创建做太多事情的类。第三，如果我们需要一次在许多地方修改代码，可能有更好的方法。最后，不要做太复杂的代码。