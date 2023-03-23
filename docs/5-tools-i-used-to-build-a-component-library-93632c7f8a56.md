# 我用来构建组件库的 5 个工具

> 原文：<https://betterprogramming.pub/5-tools-i-used-to-build-a-component-library-93632c7f8a56>

## 你的设计师可能会因此而喜欢你

![](img/6783fdadcc7ca3ac6fe93a7d060dcfd8.png)

由[特尔纳瓦大学](https://unsplash.com/@trnavskauni)在 [Unsplash](https://unsplash.com/photos/BEEyeib-am8) 拍摄的照片

随着公司的成长，新的应用程序不断涌现并不罕见。开发人员的快乐和应用程序之间的一致性是我最看重的。我希望一个组件的风格和行为在所有应用程序中都是一样的。设计一次，构建组件，导入组件，然后继续您的一天。我想解决的另一个问题是组件文档。所有这些都让我创建了一个组件库。

# 通过故事书的组件文档

如上所述，组件文档是我想要解决的问题之一。基本上，当新工程师开始工作时，我想向他们展示如何独立使用我们的组件。我最后用故事书来做这个。

> “Storybook 是一个前端工作室，用于独立构建 UI 组件和页面。成千上万的团队使用它进行 UI 开发、测试和文档编制。它是开源和免费的。”— [故事书](https://storybook.js.org/)

在我职业生涯的这个阶段之前，我从来没有制作过故事书，而且制作和运行起来非常简单。

![](img/0617fa8caf89e3a41104c89f6287a2cb.png)

[https://storybook.js.org/docs/react/why-storybook](https://storybook.js.org/docs/react/why-storybook)

# 样式组件

> Styled-components 是一个 React 特定的 CSS-in-JS 样式化解决方案，它为开发人员创建了一个平台，可以编写实际的 CSS 代码来样式化 React 组件以及 React Native— [模仿开发](https://www.copycat.dev/blog/styled-components-react)

老实说，一开始我并不太喜欢`styled-components`，但是它让我越来越喜欢。我喜欢你可以通过将你的样式提取到一个`styled-component`中来清理你的实际组件。在下面的例子中，它们都封装在一个组件中，但是您可以很容易地将它们提取到一个单独的文件夹中并导入它们！

你可以用`styled-components`做一些非常棒的事情，比如主题化，这样你所有的组件看起来感觉都一样。你甚至可以传递道具，这在下面的例子中已经发生了，但是我们只是从道具中析构主题。准备一些伪代码:

如果您决定这样做，也可以用 JavaScript 对象符号编写样式化的组件。

```
import styled from 'styled-components';const *StyledButton* = *styled*(*Button*).attrs((props) => ({
  color: props.theme.colors.primary,
}))``;
```

# 组件库—定制或现成的

是自己构建所有组件，还是获取自己覆盖的组件库(如 reactstrap ),这一决定各不相同。有大量的变量需要考虑，包括:

*   你有多少时间
*   分配了多少开发人员
*   你认为你的定制组件会比现成的更好吗
*   你讨厌自己吗(lol！)?我当然是在开玩笑，但这些是你需要考虑的一些事情

在我的例子中，我选择从货架上抓取一个库并覆盖样式以满足我们公司的需求。这并不是说从货架上抓取更好，但它确实节省时间。这个决定的好处是，现在你有大量的组件准备好了。当然，也有缺点，现在您已经向该库添加了一个依赖项，所以要明智地选择。不要选择一个决定他们想要改变一切工作方式的库。

# 福米克

对于任何人来说，在任何应用程序中都会有表单，这并不奇怪。表单带来了验证，因为您不希望最终用户输入他们想要的任何内容。你想要好的数据！您还需要能够跟踪提交的值。

你还说需要向用户呈现错误消息吗？如你所见，名单越来越长了。你可以用手卷起所有这些东西，但是 Formik 给你所有这些开箱即用的东西。其他开发人员不需要知道幕后到底发生了什么。他们需要遵循合同来使用组件。准备一个简短的架构分解！

库中有一个组件；姑且称之为`<FormHolder />`。

`FormHolder`裹`Formik`。`Formik`然后通过道具获取`Formik`工作所需的东西。它还以`Formik`的形式包装并返回子元素。`Formik`接受`Formik Form.Inputs`。这变得很棘手，因为 Formik 的表单输入有自己的风格，但是您希望它使用您的组件库，对吗？您可以在这里编写这样的包装器。这样，Formik 就明白你的组件是什么了。此外，您将从您的组件库和`Formik`中获得您的样式/功能！

最终结果类似于下面的代码。这个例子没有展示它，但是我也喜欢创建一个组件来包装包含业务逻辑的`StyledFormInput`和`<StyledErrorMessage />`。因此，你可以创建如下所示的`FormHolder`，但是不引用`<StyledFormInput />`和`<StyledErrorMessage />`，而是使用组件，我们称之为`FormField`。

现在`FormField`确切地知道该做什么，如何行动，并封装了错误处理。很可爱，是吧？如果您注意到这里对`yup`的使用，它与 Formik 在验证方面配合得非常好，所以我们选择用它来处理它们。

```
const TestSchema = yup.object({
  email: yup.string().email().required().label('Email'),
}<FormHolder validationSchema={TestSchema} initialValues={{email: ''}}>
  <StyledFormInput name="email" label="Email" placeholder="Enter email" />
  <StyledErrorMessage name={*name*} component='div' />;
</FormHolder>
```

# Rollup.js

哦，伙计，这是多么痛苦啊。我花了很多时间在谷歌上搜索错误，并弄清楚如何配置 rollup 来满足我们的需求。对于那些不知道的人，

> Rollup 是 JavaScript 的一个模块捆绑器，它将小段代码编译成更大、更复杂的代码，如库或应用程序— [汇总](https://rollupjs.org/guide/en/#overview)

您需要找出最适合您需求的配置，因此我不会分享配置，但我会分享用于创建配置的包:

```
import { babel } from "@rollup/plugin-babel";
import external from "rollup-plugin-peer-deps-external";
import resolve from "@rollup/plugin-node-resolve";
import scss from "rollup-plugin-scss";
import { terser } from "rollup-plugin-terser";
import commonjs from "@rollup/plugin-commonjs";
import generatePackageJson from "rollup-plugin-generate-package-json";
```

如果你不知道你在做什么，像我一样，从一个简单的配置开始，不断调整，直到它捆绑。您可以全局安装 rollup，然后将运行 rollup -c 的构建脚本添加到您的根级别`package.json`。

一旦你捆绑了你的代码，并且需要私下发布它，就像我的情况，我强烈建议阅读这篇文章。

[](https://www.wearecogworks.com/blog/how-to-publish-npm-packages-with-github/) [## 如何用 GitHub | Cogworks 博客发布 Npm 包

### GitHub 引入了一个很棒的新功能，可以让你管理存储库中的包，叫做这是一个很棒的…

www.wearecogworks.com](https://www.wearecogworks.com/blog/how-to-publish-npm-packages-with-github/) 

# 外卖食品

由于之前从未创建过图书馆，这是一项艰巨的任务。有一次，我认为我不能创造它，因为我没有这方面的知识。老实说，我认为关键是聪明的谷歌搜索和毅力。

我一直在调整，直到它全部合二为一，开始工作。你可能遇到的一些陷阱是故事书想要一样东西，但是你的图书馆的消费者想要不同的东西。在我的例子中，这只是一个简单的文件名改变，解决了这两个问题。

最终，拥有一个结构良好、一致的 UI 库将提高开发人员的满意度和速度，同时保持所有应用程序的一致性。

我希望这是有点深刻的，我感谢你花时间阅读这一点。

```
**Want to Connect?**For more, check out my [YouTube channel](https://www.youtube.com/channel/UC_jotKMpT2DtczWY-ILVgoA).
```