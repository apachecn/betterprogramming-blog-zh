# Javascript:反斜线如何工作(``)

> 原文：<https://betterprogramming.pub/javascript-how-backticks-work-de269e0fb8ba>

## 探索 JavaScript 中反斜线的主要用途

![](img/82beb378decbb6fbb890e1bb4bf462d3.png)

照片由 [Nhu Nguyen](https://unsplash.com/@nguyendqnhu?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

众所周知，在 JavaScript 中有两种主要的方法来声明字符串:

*   使用单引号`''`。
*   使用双引号`""`。

然而，使用 ES2015 规范，可以创建所谓的*模板文字*或*模板字符串*，这足以将文本封装在反斜杠中:

```
`some text`
```

与我将在本文中描述的两种主要方法相比，这将给我们带来许多优势。让我们看看他们。

# 连接和插入字符串

这可能是反斜线最广泛和最流行的用法之一，因为它允许我们连接和插入字符串，所以我们的代码更干净。

我们将看几个例子，比较旧的语法和反斜线提供的语法。

```
const name = 'Gerardo';
const surname = 'Fernández';
const telephone = '123 123 123';// "Old syntax"const userInfo = 'User info: ' + name + ' ' + surname + ' ' + telephone;// "New syntax"**const userInfo = `User info: ${name} ${surname} ${telephone}`;**
```

正如你所看到的，感谢模板文字，我们获得了一个更容易阅读的代码，通过`+`操作符节省了多个字符串的连接。

当然，我们也可以在模板字符串中执行代码:

```
const user = getUserFromApi();// "Old syntax"const userInfo = 'User info: ' + user.getName() + ' ' + user.getEmail();// "New syntax"**const userInfo = `User info: ${user.getName()} ${user.getEmail()}`;**
```

# 没有必要转义字符“和 Using \

使用反斜线(````)的另一个好处是，不再需要对双引号(`""`)或单引号(`''`)进行转义，当我们使用英语等语言时，通常会被迫这样做:

```
const foo = 'Can\'t connect to the server';**const bar = `Can't connect to the server`;**
```

或者例如:

```
const foo = "Error: \"Introduce a valid email\"";const bar = `Error: "Introduce a valid email"`;
```

所以，再一次，我们得到了更多可读的代码。

# 多行字符串

在 JavaScript 中，不可能在几行中声明字符串。例如，如果我们尝试以下方法:

```
const html = "<article>
<h1>Article title</h1>
</article>";
```

我们得到以下错误:

```
SyntaxError: "" string literal contains an unescaped line break
```

这很尴尬，尤其是当我们创建 HTML 的时候。在 ECMA2015 之前，我们的替代方案是使用新的行字符(`\n`):

```
const html = "<article> \
<h1>Article title</h1> \
</article>";
```

但是这仍然会在编译时带来问题:

> 每行开头的空白在编译时不能被安全地去除；斜杠后的空格会导致棘手的错误；虽然大多数脚本引擎支持这一点，但它不是 ECMAScript 的一部分。— [堆栈溢出](https://stackoverflow.com/questions/805107/creating-multiline-strings-in-javascript/6247331#6247331)

另一个选择是使用`+`操作符:

```
const html = '<article>' +
'<h1>Article title</h1>' +
'</article>';
```

但是，使用反勾号，我们可以编写以下内容:

```
const html = `<article>
<h1>Article title</h1>
</article>`;
```

所以我们得到的代码非常干净。

当然，重要的是要记住空格没有被删除，所以如果我们创建一个字符串如下…

```
const string = `First line
                Second line`
```

…我们将得到以下内容:

```
First
                Second
```

所以我们将不得不求助于`trim`方法来避免额外的空间。

# 标记模板

backticks 提供的另一个特性是创建所谓的*标记模板*(也称为*标记函数*)的可能性，这是像 Apollo 或[风格组件](https://flaviocopes.com/styled-components/)一样流行的库所使用的东西:

```
// Apollo queryconst query = **gql**`
  query {
    ...
  }// Styled componentsconst Container = **styled.div**`
  width: 1000px;
  background: red;
`;
```

`styled.div`和`gql`实际上都是从文本模板中提取参数的函数(标记函数)(也就是说，用反斜线将文本括起来)。

例如，在样式化组件`Container`的情况下，我们得到的是一个 React 组件，它表示一个具有以下方面的`div`:

```
<div style="width: 1000px; background:red">
...
</div>
```

为了理解这些类型的函数如何工作，假设我们有如下声明的`sayHello`函数:

```
function foo() {
  console.log(arguments[0]);
  console.log(arguments[1]);
  console.log(arguments[2]);
}
```

如果我们现在像调用一个函数标签一样调用它:

```
const varOne = 'bar';
const varTwo = 'zeta';foo`This is bar: ${varOne} and zeta: ${varTwo}`;
```

我们将在控制台上看到:

```
["This is bar: ", " and zeta: ", ""] (array)
bar (string)
zeta (string)
```

也就是说，函数标签接收三个参数:

*   第一个是一个数组，其中文字匹配出现在几个字符串中，使用表达式`${...}`作为断点
*   其余的参数(`arguments[1]`和`arguments[2]`)是已经插值的变量的结果(`bar` y `zeta`)

利用扩展操作符，我们可以将`foo`函数重写如下:

```
function foo(literals, ...expressions) {
  console.log(literals);
  console.log(expressions[0]);
  console.log(expressions[1]);
}
```

这样，`literals`数组的长度将始终是`expressions`数组的长度加 1。

从这一刻起，标签函数提供的可能性是非常多样的，正如 Apollo 和 Styled Components 库所展示的，或者，例如，下面的例子，我们可以看到这些类型的函数如何用于翻译我们的应用程序中的文本:

```
const name = 'Gerardo';
const email = 'info@mail.com';console.log(i18n`Hi ${name}, your email is ${email}`);// Hola Gerardo, tu email es info@mail.com
```

# 最后的想法

正如您所看到的，虽然最初它可能是一个不被注意的 JavaScript 特性，但标记模板文字在编写代码时给了我们很多灵活性，并且由于诸如 Apollo 和 Styled Components 之类的库而变得越来越流行。

我希望这篇文章能够帮助你对它们有一个初步的了解，或者巩固一些概念，比如标签函数。