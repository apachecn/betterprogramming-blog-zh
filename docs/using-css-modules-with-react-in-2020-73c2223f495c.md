# 将 CSS 模块与 React 一起使用

> 原文：<https://betterprogramming.pub/using-css-modules-with-react-in-2020-73c2223f495c>

## 有时候纯 CSS 是更好的选择

![](img/ab2efb3fc8cb191a00b2637358542176.png)

妮可·沃尔夫在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

我知道你在想什么。” [CSS 模块](https://github.com/css-modules/css-modules)？2020 年？你住在山洞里吗？”好的。我明白了。有很多很酷的 CSS-in-JS 解决方案: [styled-components、](https://www.styled-components.com/) [emotion](https://emotion.sh/) 、 [linaria](https://linaria.now.sh/) 等等。他们都很棒，我爱他们。但是在有些情况下，使用老式的、近乎纯粹的 CSS 是更好的选择。

# 故事

我想使用 React 作为模板引擎，创建一个带有 [AMP](https://amp.dev/) (加速移动页面)的 web 项目。我的风格解决方案是情感和风格系统。对我来说这是一个很好的组合。基于主题的风格、风格道具、创造变化的能力等。布伦特·杰克逊为这个伟大的工具赢得了荣誉。我也是他的新工具 [theme-ui](https://theme-ui.com/) 的忠实粉丝。

总之。您可能已经知道，AMP 对 CSS 和 HTML 有严格的限制。其中一些是，例如，整个页面的 CSS 需要在一个样式标签中，HTML 标签不能包含任何自定义属性。此外，您需要在服务器端渲染一切。一切都需要是静态的 HTML 和 CSS。不允许自定义 JS。在我开始使用 CSS-in-JS 解决方案后，我发现自己一次又一次地试图解决同样的问题。我意识到，即使这些 CSS-in-JS 解决方案总的来说很棒，但没有一个对我的工作效率有帮助。我花了那么多时间让事情运转起来。显然，我需要一个不同的解决方案。经过一番研究，我决定给一个老朋友一个机会，CSS 模块。

# 履行

由于 CSS Modules 只是带有一些构建时 webpack 魔法的 CSS，所以将其用于 AMP 不会有任何问题。

为了今天使用未来的 CSS，我使用了 [postcss-preset-env](https://preset-env.cssdb.org/) 。此外，我想从 CSS-in-JS 堆栈中我最喜欢的特性中受益，比如基于主题的样式、样式道具等。

总的来说，主题化是一个大问题。CSS 新特性的力量，比如 CSS 变量和自定义媒体查询，在这里很有帮助。有了 postcss-preset-env，您也不需要担心浏览器支持。您需要做的就是用 CSS 变量创建一个`theme.css`文件，并将这个文件提供给 postcss-preset-env config。这是我的配置和一个示例主题文件。

有了这个设置，你可以为你的主题全局定义任何变量，并在你的样式中使用它。主题化问题解决了。下一个。

正如我之前提到的，我是风格道具的忠实粉丝。有像 [classnames](https://github.com/JedWatson/classnames) 和 [clsx](https://github.com/lukeed/clsx) 这样的库来帮助你使用 props 创建动态类。让我们看一个例子。

如您所见，即使是非常基本的组件，也有许多样板代码。但是等一下。你能看出模式吗？有风格道具，道具值，相对 CSS 类。似乎有了一些约定、助手函数和组件，我们可以为这个任务创建一个小型的通用库。

首先，我们需要一些惯例。

*   使用`.root`级作为基础。
*   使用`.{prop}-{value}`约定俗成的风格道具。例如`.color-primary`
*   如果属性值是布尔值，使用`.{prop}`约定

为了从这些约定中获益，我们需要一个函数来获取一组属性和一个样式对象，然后使用 clsx 库将样式属性映射到样式对象。

有了这个助手函数，我们不需要编写所有的样板代码。让我们使用`mapPropsToStyles`函数重构按钮组件。

```
// Button.css
.root {
  ...
}.color-primary {
  background-color: var(--primary-color);
}.color-secondary {
  background-color: var(--secondary-color);
}.size-small {
  padding: var(--space-2);
}.size-medium {
  padding: var(--space-4);
}// Button.js
import mapPropsToStyles from 'src/common/mapPropsToStyles';
import styles from './Button.css';export default function Button({ color, size, ...props }) {
  const classes = mapPropsToStyles({ color, size }, styles); return <button className={classes} {...props} />;
}
```

好多了，对吧？是的，但是我们甚至可以更进一步，创建一个通用组件来为我们处理所有的样式工作。

.

使用这种方法，我们不需要为每个组件设计样板样式。我们可以用`as` prop 创建任何我们想要的元素，我们可以用`className` prop 传递额外的类来添加和覆盖样式。让我们最后一次重构我们的按钮组件。

现在我们的生活简单多了，我们的部件也很整洁。我们需要做的就是编写一个 CSS 文件，并将匹配的道具传递给我们的组件。

# 结论

当然，我们可以创建更多的例子，做更多的改进，比如使用数组的响应式道具支持。你可以从这里开始，根据你的需要进行定制。

相对于 CSS-in-JS 解决方案的一些优势:

*   只是 CSS 而已
*   没有运行时间
*   轻松将样式导出到 CSS 文件
*   SSR 和 CSR 的设置相同

有时候，坚持好的老解决方案，让事情变得简单，而不是追求炒作，会好得多。如果它符合你的需要，而且没有任何麻烦，其他的都不重要。

保持冷静，继续编码！