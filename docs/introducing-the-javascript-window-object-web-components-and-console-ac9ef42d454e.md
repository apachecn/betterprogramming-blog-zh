# JavaScript 窗口对象简介— XML 和控制台

> 原文：<https://betterprogramming.pub/introducing-the-javascript-window-object-web-components-and-console-ac9ef42d454e>

## 我们来看看如何创建 web 组件和控制台对象

![](img/5f5af5564c8604d1ac25022a06262b7e.png)

保罗·卡莫纳在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

`window`对象是一个全局对象，它具有与当前 DOM 文档相关的属性，这就是浏览器选项卡中的内容。`window`对象的`document`属性拥有 DOM 文档以及相关的节点和方法，我们可以用它们来操作 DOM 节点并监听每个节点的事件。因为`window`对象是全局的，所以它在应用程序的每个部分都是可用的。

当一个变量在没有`var`、`let`或`const`关键字的情况下被声明时，它们会被自动附加到`window`对象上，使它们在你的 web 应用程序的每个部分都可用。这仅适用于禁用严格模式的情况。如果它被启用，那么声明没有`var`、`let`或`const`的变量将被停止，因为让我们意外地声明全局变量不是一个好主意。

`window`对象有许多属性。它们包括构造函数、值属性和方法。有一些方法可以操作当前的浏览器标签，比如打开和关闭新的弹出窗口等。

在选项卡式浏览器中，每个选项卡都有自己的`window`对象，因此`window`对象总是代表代码运行时当前打开的选项卡的状态。然而，有些属性仍然适用于浏览器的所有选项卡，如`resizeTo`方法以及`innerHeight`和`innerWidth`属性。

注意，我们不需要直接引用`window`对象来调用方法和对象属性。例如，如果我们想使用`window.Image`构造函数，我们可以只写`new Image()`。

在这一部分，我们继续看看`window`对象中有什么。在第 1 部分中，我们探索了包含在`window`对象中的主要构造函数。在这一部分，我们继续看更多的`window`对象的构造函数和`window`对象的一些属性。

# 更多构造函数

## XMLSerializer

`XMLSerializer`构造函数用`serializeToString`方法创建对象来构建一个表示 DOM 树的 XML 字符串。构造函数不接受任何参数。`serializeToString`方法接收一个 DOM 树节点，然后返回节点中包含所有 DOM 树内容的字符串。当节点类型无法序列化时，该方法将抛出一个`TypeError`。如果树不能被成功序列化，它将抛出一个`InvalidStateError`。如果内容格式不正确，那么将抛出一个`SyntaxError`。以下类型的节点可以通过`serializeToString`方法序列化:

*   `DocumentType`
*   `Document`
*   `DocumentFragment`
*   `Element`
*   `Comment`
*   `Text`
*   `ProcessingInstruction`
*   `Attr`

例如，我们可以使用下面代码中的`XMLSerializer`对象:

```
const serializer = new XMLSerializer();
const doc = document;
const docStr = serializer.serializeToString(doc);
console.log(docStr);
```

上面的代码创建了一个`XMLSerializer`实例。然后我们将`document`对象传入`serializeToString`方法，该方法返回 DOM 树的字符串。如果运行上面的代码，最后一行的`console.log`输出应该类似如下:

```
<!DOCTYPE html><html ae ky" href="http://www.w3.org/1999/xhtml" rel="noopener ugc nofollow" target="_blank">http://www.w3.org/1999/xhtml"><head>
  <meta http-equiv="content-type" content="text/html; charset=UTF-8" />
  <title></title>
  <meta http-equiv="content-type" content="text/html; charset=UTF-8" />
  <meta name="robots" content="noindex, nofollow" />
  <meta name="googlebot" content="noindex, nofollow" />
  <meta name="viewport" content="width=device-width, initial-scale=1" /><script type="text/javascript" src="/js/lib/dummy.js"></script><link rel="stylesheet" type="text/css" href="/css/result-light.css" /><style id="compiled-css" type="text/css">

  </style><!-- TODO: Missing CoffeeScript 2 --><script type="text/javascript">//&lt;![CDATA[window.onload=function(){

const serializer = new XMLSerializer();
const doc = document;
const docStr = serializer.serializeToString(doc);
console.log(docStr);}//]]&gt;</script></head>
<body><script>
    // tell the embed parent frame the height of the content
    if (window.parent &amp;&amp; window.parent.parent){
      window.parent.parent.postMessage(["resultsFrame", {
        height: document.body.getBoundingClientRect().height,
        slug: ""
      }], "*")
    }// always overwrite window.name, in case users try to set it manually
    window.name = "result"
  </script></body></html>
```

![](img/607f6ed6019a909625cea59c78a0d4b3.png)

[科林·梅纳德](https://unsplash.com/@invent?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 性能

`window`对象有许多属性。它从`EventTarget`接口继承属性，并实现来自`WindowOrWorkerGlobalScope`和`WindowEventHandlers`混合的属性。

## 窗户关闭

`closed`属性是只读属性，指示引用的浏览器窗口是否关闭。如果车窗关闭，则为`true`，否则为`false`。例如，如果您运行:

```
console.log(window.closed)
```

在当前打开的窗口上，它应该记录`false`，因为浏览器窗口显然是打开的。

## 窗口.控制台

`console`属性是一个具有许多属性的对象，这些属性对于将信息记录到浏览器的控制台非常有用。它是一个只读对象。`console`方法对于调试很有用，不应该用于向最终用户呈现信息。`console`对象有以下方法:

*   `console.assert()` —如果第一个参数为`false`，则将消息和堆栈跟踪记录到控制台
*   `console.clear()` —清除控制台
*   `console.count()` —记录使用给定标签调用该方法的次数
*   `console.countReset()` —重置给定标签的计数器值
*   `console.debug()` —将消息记录到控制台，日志级别为“调试”
*   `console.dir()` —列出给定 JavaScript 对象的属性。内容将有三角形来显示子对象的内容。
*   `console.dirxml()` —如果可能，显示对象的 HTML 或 XML 表示
*   `console.error()` —将错误消息记录到控制台。我们可以使用字符串替换和附加参数来格式化日志消息。
*   `console.group()` —创建一组控制台消息，按级别缩进。我们可以用`groupEnd()`移出一个关卡。
*   `console.groupCollapsed()` —创建一组控制台消息，按项目折叠的级别缩进。我们可以用`groupEnd()`移出一个关卡。
*   `console.groupEnd()` —退出当前内嵌组
*   `console.info()` —记录信息性消息。我们可以使用字符串替换和附加参数来格式化日志消息。
*   `console.log()` —用于信息的一般记录。我们可以使用字符串替换和附加参数来格式化日志消息。
*   `console.table()` —以表格格式记录和显示数据
*   `console.time()` —以参数中指定的名称启动计时器。在给定的页面上可以同时运行 10000 个计时器。
*   `console.timeEnd()` —停止指定的计时器，并以秒为单位记录计时器启动后经过的时间。
*   `console.timeLog()` —将指定计时器的值记录到控制台
*   `console.trace()` —记录堆栈跟踪
*   `console.warn()` —向控制台记录警告消息。我们可以使用字符串替换和附加参数来格式化日志消息。

## window.customElements

`customElements`属性是一个只读属性，它返回一个对`CustomElementRegistry`对象的引用，该对象可用于注册新的定制元素并获取关于以前注册的定制元素的信息。自定义元素也称为 Web 组件。

Web Components 是一个标准，它允许我们创建封装其他 HTML 元素的自定义元素。我们需要这样做，以便我们可以在不同的地方重用 HTML 元素组。如果没有它，如果我们想要重用它们，我们必须在不同的地方重复相同的 HTML 元素组。定义的定制元素存储在`CustomElementRegistry`中，这样浏览器就知道定义的定制元素实际上是一个有效的元素。

要定义一个定制元素，我们可以使用下面代码中的`customElements`属性:

```
const customElementRegistry = window.customElements;
customElementRegistry.define('hello-element',
  class extends HTMLElement {
    constructor() {
      super();
      const shadow = this.attachShadow({
        mode: 'open'
      });
      const span = document.createElement('span');
      span.setAttribute('class', 'wrapper');
      span.innerHTML = 'Hello';
      shadow.appendChild(span);
    }
  });
```

然后在 HTML 文件中，我们添加:

```
<hello-element></hello-element>
```

在上面的代码中，我们使用了`window.customElements`对象的`define`方法。在`define`方法的第一个参数中，我们传入元素名。然后在第二个元素中，我们传入一个`HTMLElement`类，它实际上是 JavaScript 中的一个对象。在`class`的`constructor`方法中，我们创建一个`span`元素，将`innerHTML`设置为`‘Hello'`，然后将它附加到影子根，这是 Web 组件的根。如果我们运行上面的代码，我们应该会看到屏幕上显示单词 *Hello* 。

`define`方法有三个参数。第一个是`name`，它是一个具有定制元素名称的字符串。第二个参数是一个`HTMLElement`构造函数，其中，在构造函数类中，我们创建了想要附加到影子根的 HTML 元素，并显示了创建的元素。第三个参数是可选参数，它控制元素的定义方式。唯一受支持的属性是`extends`属性，它是一个字符串，允许我们指定要扩展的内置元素。这对于创建定制元素很有用，定制一个内置元素，如`div`、`span`或`p`。

# 摘要

在这一部分，我们几乎没有触及`window`物体的表面。我们只研究了在各种情况下可能会派上用场的几个构造函数。

我们创建了一个`XMLSerializer`对象，它允许我们使用`serializeToString`方法，该方法带有一个实例，该实例允许我们传入一个 DOM 树节点，并获取一个字符串，其中所有子元素都被转换为一个字符串。

然后我们查看了`window`对象的一些属性，包括`closed`、`console`和`customElements`属性。属性让我们检查当前的浏览器窗口是否关闭。`console`对象让我们以各种方式记录数据，用于调试目的。它有各种方法对日志输出进行分组，获得执行代码的时间，改变日志输出的风格，等等。属性让我们创建新的定制元素，这些元素被浏览器识别为 Web 组件，Web 组件是封装内置 HTML 元素的实体，这样我们就可以通过在代码中引用它们来重用它们。