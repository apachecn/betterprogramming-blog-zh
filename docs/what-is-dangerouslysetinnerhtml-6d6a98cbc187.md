# 什么是 DangerouslySetInnerHTML？

> 原文：<https://betterprogramming.pub/what-is-dangerouslysetinnerhtml-6d6a98cbc187>

## 真的有那么危险吗？

![](img/2d16765dce272634d1e1c8fb23ceecbc.png)

照片由 [WOCinTech Chat](https://www.flickr.com/photos/wocintechchat/) 在 [Flickr](https://www.flickr.com/photos/wocintechchat/25926572291/in/photostream/) 上拍摄

在本文中，我们将讨论什么是`dangerouslySetInnerHTML`，它是如何使用的，`dangerouslySetInnerHTML`和`innerHTML`之间的区别，以及一个它被证明有用的真实项目。

# 什么？

`dangerouslySetInnerHTML`是 React 中 DOM 元素下的一个属性。

根据官方文档，`dangerouslySetInnerHTML`是 React 对浏览器 DOM 中使用`innerHTML`的替代。

这意味着，如果在 React 中，您必须以编程方式或从外部源设置 HTML，您将必须使用`dangerouslySetInnerHTML`而不是 Javascript 中的传统`innerHTML`。

简单来说，使用`dangerouslySetInnerHTML`，可以直接从 React 设置 HTML。

# 怎么会？

首先，我们来看看`innerHTML` 是如何工作的。([https://codepen.io/lelouchb/pen/MWaMYde](https://codepen.io/lelouchb/pen/MWaMYde))

innerHTML

以下是 Javascript 代码:

```
let root = document.querySelector('#root')
root.innerHTML = `<h1>This text is set using innerHTML</h1>`
```

使用`innerHTML`很简单。您只需在 DOM 元素中使用点符号，并使用引号或模板文字传递 HTML 字符串。

现在让我们看看如何使用`dangerouslySetInnerHTML`实现同样的效果。([https://codepen.io/lelouchb/pen/PoPrqEw](https://codepen.io/lelouchb/pen/PoPrqEw))

`dangerouslySetInnerHTML`

代码:

```
function App(){
return(
<div dangerouslySetInnerHTML={{ __html: `This text is set using dangerouslySetInnerHTML` }}>

  </div>)
  }ReactDOM.render(<App />, document.querySelector("#root"));
```

使用`dangerouslySetInnerHTML`时，您必须用`__html`键传递一个对象。(注意，该键由两个下划线组成)。对象`dangerouslySetInnerHTML` 可以通过不同的方法传递。

作为一个变量:

```
function App(){
  const markup={ __html: 'This text is set using dangerouslySetInnerHTML'}
return(
<div dangerouslySetInnerHTML={markup}>

  </div>)
  }
ReactDOM.render(<App />, document.querySelector("#root"));
```

作为一项功能:

```
function App(){
  const markup=()=>{return{ __html: 'This text is set using dangerouslySetInnerHTML'}}
return(
<div dangerouslySetInnerHTML={markup()}>

  </div>)
  }
ReactDOM.render(<App />, document.querySelector("#root"));
```

# 差异

使用`innerHTML`和`dangerouslySetInnerHTML`的直接效果是一样的:DOM 节点将使用注入的 HTML 进行更新。然而，在幕后，当您使用它时，它让 React 知道组件内部的 HTML 不是它所关心的。

因为 React 使用虚拟 DOM，所以当它比较实际 DOM 的差异时，它可以直接绕过检查该节点的子节点，因为它知道 HTML 来自另一个源。因此性能会有所提高。

更重要的是，如果简单地使用`innerHTML`，React 没有办法知道 DOM 节点已经被修改。下次调用`render`函数时，React 将用它认为 DOM 节点的正确状态来覆盖手动注入的内容。

# 为什么危险？

对`innerHTML`的不当使用会让您面临[跨站脚本(XSS)](https://en.wikipedia.org/wiki/Cross-site_scripting) 攻击。prop 名称`dangerouslySetInnerHTML`被有意选择为吓人的，prop 值(一个对象而不是一个字符串)可以用来表示清理过的数据。在将 HTML 插入页面之前，您需要确保它的结构正确，并且经过净化。您可以使用像 [dompurify](https://www.npmjs.com/package/dompurify) 这样的库来实现。

# 什么时候？

适当的名字被有意地选择是令人害怕的，所以它应该在什么时候被使用？让我们讨论一个现实生活中的 React 项目，其中开发者将不得不使用`dangerouslySetInnerHTML`。

## [黑客新闻 API](https://github.com/HackerNews/API)

使用 HackerNews API 制作的项目非常常见，制作简单，也很有趣，但其中的大多数 API 请求都是针对`topstories`、`beststories`等项目的 id。下面是`topstories`其中一项的响应 JSON 数据:

[https://hacker-news.firebaseio.com/v0/item/23331287.json?打印=漂亮](https://hacker-news.firebaseio.com/v0/item/23331287.json?print=pretty)

```
{
  "by" : "lostmsu",
  "descendants" : 251,
  "id" : 23331287,
  "kids" : [ 23332582, 23333565, 23332777, 23334075, 23331976, 23332122, 23332664, 23332047, 23333745, 23332119, 23332909, 23332118, 23332674, 23333572, 23334374, 23331882, 23334410, 23331780, 23332176, 23331823, 23332297, 23333192, 23333690, 23334269, 23332755, 23333735, 23333025, 23331297, 23333672, 23332123, 23332298, 23332913, 23333112, 23334176, 23333814, 23333849, 23334245, 23333261, 23332823, 23332079, 23331890, 23332360, 23332197, 23332840 ],
  "score" : 844,
  "time" : 1590620950,
  "title" : "The Day AppGet Died",
  "type" : "story",
  "url" : "https://keivan.io/the-day-appget-died/"
}
```

但是对`askstories`中条目的请求返回了一些有趣的东西。让我们来看看吧。

[https://hacker-news.firebaseio.com/v0/item/23325385.json?print=pretty](https://hacker-news.firebaseio.com/v0/item/23325385.json?print=pretty)

```
{
  "by" : "behnamoh",
  "descendants" : 3,
  "id" : 23325385,
  "kids" : [ 23326663, 23326382, 23325618 ],
  "score" : 1,
  "**text**" : "As a heavy user, I basically **<i>live</i>** in my browser. However, extensions do slow down the browser, which makes me think there&#x27;s gotta be a better way. I believe it&#x27;s high time we had an operating system which is fully optimized as a browser. Extensions then, would be apps on the OS, and probably won&#x27;t slow it down. Have you seen any OS that is browser-first?",
  "time" : 1590595712,
  "title" : "Ask HN: Can operating systems be browsers?",
  "type" : "story"
}
```

如您所见，`text`字段包含 HTML 标签< i > live < /i >，与包含字符串、数字等的其他字段相比，您不能简单地在 HTML 中显示它们。这只是一个例子。有包含许多 HTML 标签的响应数据。

这里是`dangerouslySetInnerHTML`的实际用例，上面讨论的响应使用`dangerouslySetInnerHTML`呈现在 HTML 中。

感谢阅读！