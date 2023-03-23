# 如何向 React 应用程序添加可编辑的 HTML 内容

> 原文：<https://betterprogramming.pub/how-to-add-editable-html-content-to-a-react-app-c98564856761>

## 了解如何将 contentEditable 与 React 结合使用，以支持交互式可编辑内容

![](img/9fe9049a8d4a58593571adf2889bc231.png)

你有没有想过像 [Google Docs](http://docs.google.com) 或[concept](http://notion.so)这样的应用是如何在网络应用中实现交互式、可编辑的丰富内容的？使用像`textarea`这样的输入来构建表单的通常方法不能提供同样的体验。

相反，像这样的应用程序利用 HTML 的`[contentEditable](https://developer.mozilla.org/en-US/docs/Web/HTML/Global_attributes/contenteditable)`属性，允许用户直接编辑页面上的内容。虽然启用内容编辑就像设置`contentEditable`属性一样简单，但实际上将其集成到应用程序中并从中获取可用数据却是另一回事。

特别是，如果你尝试过在 [React](https://reactjs.org) 中使用 contentEditable，你会很快意识到这两者不能很好地配合——至少，没有一点额外的工作。在本帖中，我们将了解如何将`contentEditable`与 React 结合使用，以在 React 应用中实现可编辑的交互式内容。

# 什么是内容可编辑？

`contentEditable`是一个属性，可以放在 HTML 元素上，让用户可以编辑内容。我们可以通过下面的 HTML 片段演示如何使用`contentEditable`:

```
<div style="border: 1px solid #ccc; padding: 8px" contentEditable> 
  <h4>Editable Content - Edit Me!</h4> 
  <p>This piece of content has the contentEditable attribute set.</p> 
</div>
```

如前所述，使用`contentEditable`进行编辑很简单，但并不总是*容易*使用你的应用程序。为了用`contentEditable`完整地构建一个应用程序，你需要以某种方式解决以下问题。

*   为更新的内容添加更改处理程序
*   添加对编辑内容的验证和净化
*   添加内部数据表示的翻译(大多数应用程序不存储内容的原始 HTML)
*   使内容可编辑与应用程序数据生命周期的其余部分配合良好

最后这一点对于 React- `contentEditable`和 React conflict 来说是一个特殊的挑战，因为它们管理和更新 DOM 状态的方式不同。

# 在 React 中使用 contentEditable 有哪些挑战？

在用 React 查看`contentEditable`之前，让我们快速看一下如何用`textarea`实现*非富*文本编辑:

```
const TextAreaEditable = () => { 
  const [content, setContent] = React.useState("") 
  return ( 
    <textarea value={content} onChange={e => setContent(e.currentTarget.value)} /> 
  ) 
}
```

但是，如果我们试图在 React 应用程序中对`contentEditable`内容采用这种模式，我们很快就会遇到错误、警告和意外行为。让我们来看看为`contentEditable`改编上述代码的一个幼稚尝试:

```
🚫 Our naive attempt at using contentEditable: 

const Editable = () => { 
  const [content, setContent] = React.useState("") 
  return ( 
    <div onBlur={t => setContent(t.currentTarget.innerHTML)} contentEditable>
      {content} 
    </div> 
  ) 
}
```

如果我们查看控制台，我们会在呈现页面时看到以下警告:

> *警告:一个组件是* `*contentEditable*` *并且包含由 React 管理的* `*children*` *。现在，您有责任保证这些节点不会被意外修改或复制。这大概不是故意的。*

问题是启用了`contentEditable`后，React 和浏览器都想处理元素内部内容的状态。此外，当我们开始编辑内容时，我们会遇到一些其他问题。

如果我开始编辑内容，并按下 command-B 来加粗我正在编辑的一些文本，我会注意到两件事:

首先——它有效！-不需要做任何特殊处理，我们就能从交互式编辑器中进行文本样式操作。

但是我注意到的第二件事是，当视图模糊和状态更新发生时，我的文本变成了一堆 HTML！我没有看到文本“1 **2** 3”，而是看到了`1<span style="font-weight: bold;">2</span>3`

这不是我们想要的！

当然，问题是我们的内容字符串是纯 HTML，但是我们试图通过将它作为`div`的子元素传递来在 React 中呈现它。React 不会将这些子元素解释为 HTML，而是对内容进行转义。

# 危险的解决方案

为了让我们的`contentEditable`组件正确工作，我们需要告诉 React 呈现一个 HTML 字符串，而不是纯文本。幸运的是，这样的技术是存在的，但它有一个听起来很吓人的名字:`[dangerouslySetInnerHTML](https://reactjs.org/docs/dom-elements.html#dangerouslysetinnerhtml)`。

属性可以用来设置元素的内部 HTML，让我们重写我们的组件:

```
✅ Our second attempt at using contentEditable: const Editable = () => { const [content, setContent] = React.useState("") const onContentBlur = React.useCallback(evt => setContent(evt.currentTarget.innerHTML)) return ( <div contentEditable onBlur={onContentBlur} dangerouslySetInnerHTML={{__html: content} /> ) }
```

顾名思义，`dangerouslySetInnerHTML`就是*危险。*为什么？因为未经组织的 HTML 输入可能存在安全风险，并使用户面临跨站点脚本(XSS)攻击。想象一下，我们正在使用`contentEditable`来实现用户之间的协作。如果我们不小心，攻击者可能会在内容中嵌入 JavaScript，当使用`dangerouslySetInnerHTML`时，这些内容将在受害者的浏览器中执行。

因此，当使用`contentEditable`和`dangerouslySetInnerHTML`时，总是*建议使用某种形式的输入净化。这个包`[sanitize-html](https://www.npmjs.com/package/sanitize-html)`可以帮助执行一组白名单 HTML 标签来阻止 XSS 攻击。*

```
✅ Sanitized HTML with contentEditable: 

import sanitizeHtml from "sanitize-html" 

const Editable = () => { 
  const [content, setContent] = React.useState("") 
  const onContentBlur = React.useCallback(evt => { 
    const sanitizeConf = { 
      allowedTags: ["b", "i", "a", "p"], 
      allowedAttributes: { a: ["href"] } 
    }
    setContent(sanitizeHtml(evt.currentTarget.innerHTML, sanitizeConf)) 
  }, [setContent]) 
  return ( 
    <div contentEditable onBlur={onContentBlur} dangerouslySetInnerHTML={{__html: content}} /> 
  ) 
}
```

请注意，要确保您的应用程序不受 XSS 攻击，仅在编辑期间进行验证是不够的。您还需要净化和验证服务器端的内容或从服务器返回的内容！如果你只是在用户编辑内容时进行清理，聪明的攻击者可以绕过安全检查，直接向服务器发送不安全的数据。

# 如何使用 react-contenteditable

虽然上面的基本方法允许您在 React 应用程序中使用`contentEditable`，但是当您开始在应用程序中实现功能时，您可能会注意到一些边缘情况。具体来说，如果在模糊事件之前向`div`添加额外的事件处理程序来捕获输入，您可能会看到光标跳跃和其他不良行为。这是因为需要一点额外的逻辑来确定*何时*实际修改内容状态并触发重新呈现。

虽然我们可以在组件中添加一个解决这个问题和其他边缘情况的方法，而不会给*带来太多麻烦，但是为什么要重新发明轮子呢？`[react-contenteditable](https://www.npmjs.com/package/react-contenteditable)`包解决了您在 React 应用程序中可能遇到的大多数常见问题。*

使用这个包是对我们前面例子的一个非常简单的改变:

```
✅ Sanitized HTML with the ContentEditable package: 

import sanitizeHtml from "sanitize-html" 
import ContentEditable from 'react-contenteditable' 

const Editable = () => { 
  const [content, setContent] = React.useState("") 
  const onContentChange = React.useCallback(evt => { 
    const sanitizeConf = { 
      allowedTags: ["b", "i", "a", "p"], 
      allowedAttributes: { a: ["href"] } 
    } 
    setContent(sanitizeHtml(evt.currentTarget.innerHTML, sanitizeConf)) 
  }, [setContent]) 

  return ( 
    <ContentEditable onChange={onContentChange} onBlur={onContentChange} html={content} /> 
  ) 
}
```

通过使用`sanitize-html`和`react-contenteditable`，我们可以在 React 应用中快速构建丰富的交互式内容编辑的基础。

# 总结

在这篇文章中，我们学习了一些如何在 React 应用中添加用户可编辑的内容。主要考虑因素是:

*   `contentEditable`在 React 中需要一些特殊的处理。
*   `[react-contenteditable](https://www.npmjs.com/package/react-contenteditable)`包可以帮助处理您可能遇到的边缘情况。
*   编辑*和*时，记得在呈现不可信的内容之前净化 HTML。`[sanitize-html](https://www.npmjs.com/package/sanitize-html)`包可以在这方面有所帮助。

使用这种简单的方法，您可以开始向任何 React 应用程序添加可编辑的内容！

*原载于 2022 年 12 月 22 日 https://blixtdev.com*[](https://blixtdev.com/how-to-use-contenteditable-with-react/)**。**