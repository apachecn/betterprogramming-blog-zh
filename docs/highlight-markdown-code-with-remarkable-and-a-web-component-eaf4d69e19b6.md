# 如何用卓越来突出 Markdown 代码

> 原文：<https://betterprogramming.pub/highlight-markdown-code-with-remarkable-and-a-web-component-eaf4d69e19b6>

## 用 Web 组件漂亮地显示代码

![](img/e0d39d25e3c01184a096137f864269e1.png)

照片由[蒂姆·帕奇](https://unsplash.com/@tdpatch?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/highlight?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

我们目前正在为即将发布的新版本的遥控器 [DeckDeckGo](https://deckdeckgo.com) 开发许多改进，这是我们用于演示的开源 web 编辑器。

其中一项改进将使我们的用户能够在我们的编辑器中写笔记，并更方便地在控制器中显示它们。但不仅如此，还可以使用 Markdown 来编写这些内容。

今天早些时候， [Foad Sojoodi Farimani](https://twitter.com/fsfarimani) 与我们分享了一个很好的想法，即更进一步，突出显示作为降价注释提供的代码块。

我发现这个主意棒极了，我几乎直接开始实施它(*几乎*因为我必须先打扫我的公寓，周末的工作是最好的……)。

虽然通常情况下，您会为您正在使用的 Markdown 解析器提供一个库，在我们的例子中，Jon Schlinkert 的[显著的](https://github.com/jonschlinkert/remarkable)，为了突出显示代码，我通过在我们的 web 组件`<deckgo-highlight-code/>`的帮助下实际解析它来解决它。

这个[组件](https://docs.deckdeckgo.com/components/code)是一个包装器，它使用 Lea Verou 和 James DiGioia 的 [Prism.js](https://prismjs.com) 。它的优势是在运行时获取它必须解释的语言的定义，如果提供的代码可以是任何类型的，这就有点方便了。

因为我花了一点时间来弄清楚如何将所有的东西整合在一起，所以我想通过这篇新的博客帖子向你展示一下它的踪迹。

# 装置

在您的项目中，安装 markdown 解析器和 web 组件:

```
npm install remarkable @deckdeckgo/highlight-code --save
```

# 进口

安装后，将库和组件导入到您的应用程序中:

```
import {Remarkable} from 'remarkable';import '@deckdeckgo/highlight-code';
```

# 解决办法

在实例化一个新的`Remarkable`对象时，我们将定义自己的自定义规则来解析代码，而不是像文档中显示的那样提供一个荧光笔函数，并且我们不提供任何荧光笔。

```
const md: Remarkable = new Remarkable({
    html: true,
    xhtmlOut: true,
    breaks: true
});
```

我们的目标是用我们的定制 web 组件`<deckgo-highlight-code/>`替换自动生成的 HTML 代码`<pre><code/><pre>`。因此，我们在自定义函数中创建自己的规则。

此外，由于代码可以是内联的，也可以是以块的形式提供的，所以我们在函数中添加了一个变量来处理两种情况和各自的风格。

```
const codeRule = (inline: boolean) => 
  (tokens, idx, _options, _env) => {
    return `<deckgo-highlight-code 
            ${inline ? 'class="inline"' : ''}
            language="${tokens[idx].params ?
                tokens[idx].params : 'javascript'}">
                    <code slot="code">${tokens[idx].content}</code>
        </deckgo-highlight-code>`;
};
```

我们的自定义规则被定义，我们将它设置为`Remarkable`来转换代码(“内联代码”)和栅栏(“代码块”)。

```
md.renderer.rules.code = codeRule(true);
md.renderer.rules.fence = codeRule(false);
```

为了将 Markdown 内容解析成 HTML，我们最后像往常一样，使用`render`方法。

```
md.render(content);
```

# 风格

为了处理内联和块两种样式，我们为内联样式提供了特定的显示。

```
deckgo-highlight-code {
  &.inline {
    --deckgo-highlight-code-container-display: inline-flex;
  }
}
```

# 试验

我们终于可以尝试我们的解决方案了。为此，我们尝试向控制台呈现一段简单的代码。

```
const content: string = `# Title

A line \`console.log('Inline code');\`

\`\`\`
console.log('Block code');
\`\`\`

                `;console.log(md.render(content));
```

如果一切按计划进行，输出应该如下所示:

```
<h1>Title</h1><p>A line <deckgo-highlight-code
              class="inline" language="javascript">
                  <code slot="code">console.log('Inline code');
                  </code>
          </deckgo-highlight-code>
</p><deckgo-highlight-code 
        language="javascript">
              <code slot="code">console.log('Block code');
              </code>
</deckgo-highlight-code>
```

瞧，就是这样，我们做到了。

我们用`Remarkable`和一个 web 组件动态突出显示 Markdown 代码。

# 结论

DeckDeckGo 是开源的，因此，你可以看看我们在 [GitHub](https://github.com/deckgo/deckdeckgo/blob/63a76e3a4d7b40ef0cb70dc30cae818da02d4d94/remote/src/app/components/app-notes/app-notes.tsx#L31) 上实现的源代码。

到无限和更远的地方。

大卫