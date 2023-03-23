# 作为 React 开发人员，我有 6 个遗憾

> 原文：<https://betterprogramming.pub/6-regrets-i-have-as-a-react-developer-52e95a8ff8a4>

## 我希望早点做的事情

![](img/07f5eb51a4782cb489cca9f9f8d9bedd.png)

弗朗西斯科·冈萨雷斯在 [Unsplash](https://unsplash.com/s/photos/sadness?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

React 是一个很好的学习工具。它允许我们以自己的方式做事。它既强大又有局限性。

对于新开发人员来说，对于哪种工具最适合哪种用例，没有明确的指导方针，因此每个问题都有多种解决方案。果不其然，我也陷入了这个错误，并在采用一些最佳实践时迟到了。

今天，我将分享我在 React 开发之旅中应该早些开始做的 6 件事。

# 1.测试

很长一段时间，测试是我的弱点。我没有为我的组件编写测试，正如所料，我经常不得不调试打字错误。

但是，尽管看起来令人望而生畏，在 React 中测试真的很容易(对于大多数用例来说)。

从长远来看，添加一个只需两分钟就能编写的非常基本的测试可以节省几个小时。这里有一个测试来检查`Title`组件是否会正确呈现:

```
***it***('checks if the title component is in the document', () => {
    ***expect***(***screen***.***getByText***('Title')).toBeInTheDocument()
})
```

如果您使用的是`create-react-app`，那么您已经有了测试设置。只要尽可能多地(尽早地)开始编写测试就行了。

# 2.使用正确的文件夹结构

我认为作为一个 React 初学者，我最大的错误是没有使用正确的文件夹结构。实际上，我所做的是根据文件的类型对它们进行分组:

```
|-store |--actions
    |---UserAction.js
    |---ProductAction.js
    |---OrderAction.js |--reducers
    |---UserReducer.js
    |---ProductReducer.js
    |---OrderReducer.js
```

但是随着项目越来越大，找到任何文件变得越来越困难。

所以最后我开始按特征组织我的文件。这意味着所有相似的文件现在都放在同一个文件夹中:

```
|-store |--user
    |---UserAction.js
    |---UserReducer.js |--product    
    |---ProductAction.js
    |---ProductReducer.js |--order    
    |---OrderAction.js
    |---OrderReducer.js
```

现在对我来说，在文件系统中查找任何东西都容易多了。

# 3.使用样式组件

一开始我开始使用`css`文件来设计我的组件，但是随着时间的推移，它变得非常混乱。

过了一段时间，我学会了`sass`，感觉棒极了！尽管它提供了一些超过普通`css`的句法优势，但是很难对任何组件进行样式化。重用任何一种风格都特别困难，因为我经常忘记已经有了一种特定的风格。

还有，我个人不喜欢在`JSX`里面使用`className`属性。

过了一段时间，我找到了一个名为`styled-components`的库，它拯救了我。现在，我只是将我的风格声明为独立的组件，我的代码更加简洁易读。

另外，`styled-components`接受了`props`，这帮助我减少了组件中的条件样式。

# 4.尽早切换到功能组件

一开始，我通过`class-components`被介绍给 React，大约有一年的时间，我只和一起处理类组件。

在我转向功能组件后，它们的巨大好处变得清晰起来。我认为`react-hooks`是自 React 出现以来发生的最好的事情。

在 2021 年，没有什么理由会有人尝试使用基于类的组件。

现在我正试图将我所有的组件重写为功能组件。

# 5.使用表单处理库

处理形式可能是任何应用程序最常见的特征之一。我用了香草`onChange`方法很长时间。处理数据和验证是非常痛苦的事情！

*直到我发现了* `*Formik*` *和* `*react-hook-form*` *。*

使用这两个库，表单处理变得更加容易和简洁。最重要的是，表单验证现在是声明性的，对我来说很简单。

# 6.使用棉绒和格式化程序

很长一段时间，手动格式化我的代码是一件非常麻烦的事情。我喜欢我的代码整洁干净，所以每当我需要:

*   移除未使用的变量
*   删除未使用的功能
*   移除未使用的导入
*   使用适当的缩进

我不得不手动操作。直到我遇到了`Eslint`和`Prettier`——这两个库使格式化的痛苦工作变得容易！

所以，这是我希望在职业生涯中早点开始使用的前 6 个库。你呢？

祝您愉快！如果你喜欢这个:D，这里还有一个

**通过**[**LinkedIn**](https://www.linkedin.com/in/56faisal/)**或我的** [**个人网站**](https://www.mohammadfaisal.dev/) **与我取得联系。**

[](/top-7-libraries-for-blazingly-fast-reactjs-applications-c0069e87c8b7) [## 快速反应应用的 7 大库

### 摇滚明星开发人员的必备工具

better 编程. pub](/top-7-libraries-for-blazingly-fast-reactjs-applications-c0069e87c8b7) [](/the-7-traits-of-a-rock-star-react-developer-747fbb001c05) [## 摇滚明星 React 开发者的 7 个特质

### 造成差异的习惯

better 编程. pub](/the-7-traits-of-a-rock-star-react-developer-747fbb001c05) 

## 相关库:

**测试**:[react-测试-库](https://testing-library.com/docs/react-testing-library/intro/)， [jest](https://jestjs.io/)
**造型:** [样式-组件](https://styled-components.com/)
**表单**:[react-hook-表单](https://react-hook-form.com/)
**工具** : [EsLint](https://eslint.org/) 和[漂亮点](https://prettier.io/)