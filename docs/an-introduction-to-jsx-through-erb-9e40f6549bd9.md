# 通过 ERB 介绍 JSX

> 原文：<https://betterprogramming.pub/an-introduction-to-jsx-through-erb-9e40f6549bd9>

## 从《ERB》看《反应》中的 JSX

![](img/6b8c80eb4de07140060661a72e81d981.png)

[Ridwan Meah](https://unsplash.com/@riddywankenobi) 在 [Upsplash](https://unsplash.com/s/photos/ice-cream-cone) 上拍摄的冰淇淋蛋筒照片

如果你在 Rails 中与 ERB 一起工作过，并且需要学习 React 中的 JSX，这篇文章可能会帮助你将你已经学到的东西带到新的格式中。这也可能有助于你理解两者之间的重要区别。

# ERB 的快速回顾

ERB 是一个模板引擎，允许你在一个 HTML 文件中编写 Ruby 代码来生成用户在客户端看到的 HTML。在一个`.erb`文件中，ruby 和 HTML 之间的界限很清楚，Ruby 包含在“squids”中:

```
<%= ruby code here will render %> 
```

或“冰淇淋甜筒”:

```
<% ruby code here will run but not render %> .
```

冰淇淋甜筒是用来运行 ruby 代码的，不显示任何东西——在里面你可以运行任何 ruby 逻辑。您可以有一个类似这样的`if else`语句:

```
<% if some_variable >= 5 %>
  <p>Here’s your conditional p tag</p>
<% end %>
```

如果您想将标记的内容生成到 HTML 中，可以使用 squids:

```
my_h1_text = "Hello World!"<h1><%= my_h1_text %></h1>
```

# JSX 有什么不同？

JSX 和 ERB 的相似之处在于，它们都允许使用非 HTML 语言来呈现 HTML 文件中的内容。就像 ERB 让你把 Ruby 代码插入 HTML 一样，JSX 也让你把普通的 JavaScript 代码插入 JSX，尽管有一些不同。除此之外，这两种语言几乎在每个方面都不同。要正确地学习使用 JSX，还有很多东西需要了解，所以我不会在这里做一个完整的教学演示。相反，我将关注 JSX 与 ERB 和其他模板语言的重要区别，以帮助您在开始时了解您正在使用什么。

那么，JSX 看起来像什么？以下是片段:

```
const titleText = “Here’s your Main Title!”const mainTitle = <h1 *className*=”main-title”>{ titleText }</h1>
```

乍一看，这看起来几乎像是 HTML 中加入了一点 Javascript。您可能会推断方括号`{}`的使用方式类似于 squids 在 ERB 的使用方式，允许我们将 Javascript 插入到我们正在创建的元素中。

不过有一点我们马上就能发现——使用了`className`而不是`class`。这不是 HTML，这是 JSX。这是第一个迹象，即使在花括号`{ titleText }`中的 Javascript 产生之后，我们也没有直接将 HTML 字符串保存到变量中。

在页面上，上面的内容呈现为:

```
<h1 *class*=”main-title”>Here’s Your Main Title!</h1>
```

但是编译器如何知道 HTML 外观部分和 JavaScript 部分之间的区别呢？没有 squids 来描述应该将`className`解释和呈现为`class=‘main-title’`。这是因为这些都不会被解读为 HTML。这是因为 JSX 告诉 Javascript 创建一个元素，它看起来可能与您最初键入的内容相似，但已经通过 JavaScript 生成为一个对象，就像这样:

```
React.createElement(“h1”, {class: “main-title}, “Here’s Your Main Title!”)
```

# 有什么事是我能对 ERB 做却不能对 JSX 做的吗？

在上面的 ERB 例子中，我使用了一个`if`语句来说明 ERB 的功能。如果使用不当，这种操作可能会破坏您的 JSX 代码，因为 React 将 JSX 转换为 JS 的方式。

如果我们试图包含一个 If 语句来给 h1 标签一个条件类，这是行不通的:

```
const titleText = “Here’s your Main Title!”let bool = true<h1 *class*= { 
  *if* *(bool)* {
    *“main-title”
  }* *else* *{
    “another-title”
  }
}*>{ titleText }</h1>
```

有两种方法可以解决这个问题。一种方法是使用三元表达式:

```
<h1 *class*= { *bool* *?* *“main-title”* *:* *“another-title”* *}*>{ titleText }</h1>
```

我们还可以在其他地方执行条件逻辑，并将其输出保存到一个变量中，然后将该变量插入到花括号中:

```
let bool = trueconst ourTitle;if (bool) {
  ourTitle = “main-title” ;
} else {
  ourTitle = “another-title” ;
}const mainTitle = <h1 *class*= { *ourTitle* *}* *}*>{ titleText }</h1>
```

如果逻辑变得太复杂而无法包含在三元表达式中，那么在其他地方执行这种条件逻辑就变得更加必要。

如果你正在寻找下一个阅读 JSX 和 React 的地方，从源头开始——JSX 上的 [React 文档。](https://reactjs.org/docs/introducing-jsx.html)