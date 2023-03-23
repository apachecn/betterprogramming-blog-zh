# 作为初级开发人员，两种 JavaScript 编码模式给你启示

> 原文：<https://betterprogramming.pub/two-javascript-coding-patterns-that-give-you-away-as-a-junior-developer-38b4437c03ac>

## 计算机编程介于艺术和科学之间

![](img/ea4532f8201a2b9b4cc2bf385443e90a.png)

照片由[像素](https://www.pexels.com/photo/boy-wearing-blue-t-shirt-using-black-laptop-computer-in-a-dim-lighted-scenario-159533/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)的 [Pixabay](https://www.pexels.com/@pixabay?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) 拍摄

计算机编程介于艺术和科学之间。您的代码必须符合计算机期望和理解的语法，但是您的程序的风格和流程最终取决于您。

大多数编程问题可以用许多不同的方法解决，判断一个比另一个更好是很棘手的。有时，事情归结于个人偏好，而其他时候一种方法只是比另一种更好。这可能是为了提高性能、简洁性或可读性。

这篇文章探讨了初级开发人员比高级开发人员更可能使用的两种模式。下面的例子是用 JavaScript 编写的，但是一般原则也适用于其他编程语言。

# 1.过度使用“if”和“`else”`”语句

假设我们正在编写一个用来表示辛普森一家角色的类。该类的构造函数接受该字符的名、姓和职业。

下面的代码创建了这个类，并实例化了一个示例角色`edna`。

```
class Character {
  constructor (firstName, lastName, occupation) {
    this.firstName = firstName
    this.lastName = lastName
    this.occupation = occupation
  }
}const edna = new Character(
  'Edna',
  'Krabappel',
  'Elementary School Teacher'
)
```

现在，假设我们要向类中添加一个 getter 属性，该属性返回一个布尔值，描述该角色是否是 Simpson 家族的成员。

埃德娜·克拉珀不是辛普森一家的成员，但丽莎·辛普森可能是。这里有一种方法可以做到这一点，但不是很好。

```
class Character {
  constructor (firstName, lastName, occupation) {
    this.firstName = firstName
    this.lastName = lastName
    this.occupation = occupation
  }

  get isSimpson () {
    if (this.lastName === 'Simpson') {
      return true
    } else {
      return false
    }
  }
}const edna = new Character(
  'Edna',
  'Krabappel',
  'Elementary School Teacher'
)
console.log(edna.isSimpson) // Logs false, as expected
```

这段代码按预期工作，但是不必要的冗长。

首先，不需要`else`块。如果发现条件是`true`，那么函数将返回值并终止——永远不会到达`else`选项。

这一事实允许我们将该方法简化为如下:

```
get isSimpson () {
  if (this.lastName === 'Simpson') {
    return true
  }
  return false
}
```

一般来说，避免`else`块在风格上更可取，因为它减少了代码嵌套。虽然这并不总是可能的，但通常是可能的。

但是即使有了这样的改进，这种方法还是有点傻。因为 getter 打算返回一个布尔值作为输出，所以根本不需要 if 语句。

这段代码做同样的工作:

```
get isSimpson () {
  return this.lastName === 'Simpson'
}
```

这样好多了。比较运算符通常与 if 语句结合使用，但也不是必须这样。有时候，直接返回一个布尔值更好。

# 2.以非函数方式使用函数式编程

JavaScript 数组可以通过过程或函数来操作。

函数式方法通常更可取，因为它避免了突变和不必要的变量，但是过程式方法在某些情况下也是合适的。

虽然您对范式的选择可能是个人喜好的问题，但是对函数式编程技术的误用可能会将您视为初学者。为了说明，这里有一个例子。

假设我们有一个可用的`Character`对象数组，并想使用这些数据创建一个名称数组。

```
// An example input array could look like this:
const characters = [
  new Character(
    'Edna',
    'Krabappel',
    'Elementary School Teacher'
  ),
  new Character(
    'Lisa',
    'Simpson',
    'Student'
  ),
  new Character(
    'Moe',
    'Szyslak',
    'Bartender'
  ),  
]// In that case the output we are looking for would look like this:
[
  'Edna Krabappel',
  'Lisa Simpson',
  'Moe Szyslak'
]
```

第一步是向我们的`Character`类添加一个 getter，返回角色的全名:

```
get fullName () {
  return `${this.firstName} ${this.lastName}`
}
```

有了这些信息，我们可以继续获取全名数组。以下是一个可行的解决方案，但仍有改进的空间:

```
const names = []
characters.forEach(character => {
  names.push(character.fullName)
})
```

这实现了`forEach`并提供了一个回调函数，但它也可能是程序化实现的。

循环的每次迭代不是返回值，而是改变外部`names`变量。一个`for`循环可以轻松实现同样的事情:

```
const names = []
for (let character of characters) {
  names.push(character.fullName)
}
```

`forEach`这不是正确的选择。为了确保回调函数保持“纯净”，我们应该使用另一个数组方法——让我们试试`reduce`。

```
const names = characters.reduce((names, character) => {
  return names.concat(character.fullName)
}, [])
```

这种尝试避免了与`forEach`相关的问题，但仍然不是很好。

问题出在动词“减少”上。除了防止外部声明的变量和变异，函数式编程的一个重要好处是可读性。

像“filter”或“reduce”这样的函数方法在正确使用的情况下可以使代码更具表现力和可读性。

例如，当程序员看到一个数组正在被“过滤”时，他们可以假设一组项目正在被输入，只有这些项目的子集将被输出。没有输出的项目被“过滤掉”。

同样，当程序员看到一个数组被“缩减”时，他们可以假设函数将接受输入集并将其“缩减”为更紧凑的输出。你可以把一系列的考试分数“简化”成一个平均值。

这给了你的代码读者一个有用的提示。如果数组是按程序进行操作的，那么读者需要深入底层代码才能理解发生了什么。

回到这个例子，这个解决方案并不理想，因为动词“减少”不能准确地描述正在发生的事情。因为目标是为每个输入项返回一个输出项，所以“map”是更好的选择。它也更加简洁:

```
const names = characters.map(character => character.fullName)
```

# 摘要

编写有效的代码是好的，但是我们也应该努力编写简洁的、高性能的、对其他人可读的代码。

消除多余的`if`和`else`条件并适当地选择数组方法是朝着这个目标迈出的一大步。

这些看似很小的细节是有经验的程序员和缺乏经验的程序员的区别之一。