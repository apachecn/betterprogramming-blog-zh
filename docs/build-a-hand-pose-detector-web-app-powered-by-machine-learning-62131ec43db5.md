# 构建由机器学习驱动的手部姿势检测器 Web 应用程序

> 原文：<https://betterprogramming.pub/build-a-hand-pose-detector-web-app-powered-by-machine-learning-62131ec43db5>

## [软件工程](https://rakiabensassi.medium.com/list/software-engineering-7a179a23ebfd)

## 结合机器学习和网络开发

![](img/1a3d80dd698f94983715c8f68c028f13.png)

[亚历山大·奈特](https://unsplash.com/@agk42?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

所以你听说过机器学习(ML)的热门话题，但不知道它可以处理哪些任务和业务需求？或者说如何整合到你的项目中？

我抓住你了。通过使用机器学习算法，而不是仅仅依靠人类逻辑来处理信息，现代 web 应用程序已经变得更加先进。机器学习的关键优势是软件可以自己从数据中学习。

本文将阐明这一主题，展示机器学习的七个用例以及著名 web 应用程序的示例，并就如何作为 web 开发人员使用它给出一些见解。

```
**Table of Contents****∘** [**What Is Machine Learning?**](#e8ec) **∘** [**Use Cases**](#d1cf)[1\. Customers segmentation](#383e)[2\. Tailored search results](#4b84)
    [3\. Increase customer satisfaction in online shopping](#117b)[4\. Interact with software in natural language](#0094)
    [5\. Increase audience engagement in social journalism](#aa64)
    [6\. Code completion in software sevelopment](#afc8)
    [7\. Improve a Q&A platform](#cafc) **∘** [**How To Use ML in a JavaScript Project**](#19a0)
    [Case Study: Navigation with the hand gesture](#f1ae)
**∘** [**Final Thought**](#d42b)
```

# 什么是机器学习？

让我们从 ML 到底是什么的快速定义开始。机器学习提供了一套算法，允许计算机从它们过去的经验和信息中学习，这使它们能够适应新数据和新情况的出现。

如果有一个从未见过的输入，这将自动提高程序在特定任务上的性能。

# 用例

## 1.客户细分

智能系统使用机器学习来识别人类，并预测他们接下来可能会做什么。ML 最常见的应用之一是客户细分:根据某些因素将一组客户分成不同的组，以确定如何最好地营销或宣传产品和服务。

客户细分有多种应用:

*   一个企业可能想把它的顾客分成两类:以前从他们那里买过东西的顾客和从未买过东西的顾客。
*   银行可能希望将其客户划分为欺诈风险较高的子组，以便不浪费时间在欺诈风险较低的客户中调查交易。

在尝试预测客户行为时，可以参考许多变量:

*   传统上，人口统计学(年龄、性别、收入)和地理变量(邮政编码、城市)被用来细分客户。
*   最近，在线行为也被使用。例如:如果一个人在过去的十分钟内访问了一个特定的网站或浏览了一个产品页面，那么与他们还没有看到任何相关内容相比，他们可能更有可能购买一件商品。

## 2.定制的搜索结果

搜索引擎使用机器学习，通过分析用户的搜索行为，为用户搜索产品、价格比较、最新交易或其他特定信息提供定制的结果。

通过谷歌的搜索算法你可能对这个比较熟悉。

## 3.提高在线购物的客户满意度

网上购物是一个价值数十亿美元的产业，并且还在逐年增长。这也是一个开发不同的广告方法的机会，以获得潜在客户的更多关注。

例如，德国跨国电子商务公司 Zalando 通过使用一种应用程序，根据以前在该公司网站上的购买情况，了解什么类型的服装最受欢迎，从而将退货率降低了 40%。

Zalando 于 2008 年在德国推出了第一个网站，现在已经在 T2 17 个国家拥有超过 3500 万客户。

这是如何工作的？

根据专家的说法，机器学习预测可以基于客户过去的点击模式和他们以前订购的东西。然后，这些数据可用于仅显示相关产品的集合，并根据每个消费者的偏好提供量身定制的结果。

## 4.用自然语言与软件交互

通过自然语言与软件交互变得越来越流行。在过去的几年里，我们已经看到了智能机器人的崛起，它们可以与客户进行对话并为他们提供指导。

美国美国国家铁路客运公司铁路公司就是一个例子，该公司在其网站上使用聊天机器人，每年回答 100 多万顾客的询问。它的人工智能机器人[朱莉](https://www.amtrak.com/about-julie-amtrak-virtual-travel-assistant)——是一个屡获殊荣的虚拟助理。

## 5.提高受众对社会新闻的参与度

对于 Medium 这样的平台，机器学习使算法能够通过选择与观众正在阅读并感兴趣的内容相似的内容，向观众推荐故事。

我们在自己的生活中每天都在使用 ML 的算法——在搜索引擎、分类器、推荐系统等等。然而，当集成到像 Medium 这样具有大数据的应用程序中时，这些工具的威力变得更大，在这些应用程序中，大量的信息被一种算法挖掘出来，这种算法与用户在线阅读材料的经验相结合。

## 6.软件开发中的代码完成

IntelliJ IDEA 是一个强大的 IDE，它提供了一些有趣的特性——比如对许多语言和框架的智能编码帮助——来改善开发体验和提高生产力。

在 ML 模型的帮助下，IntelliJ 收集有关开发人员与代码完成 UI 交互的信息，以增强代码完成功能。

代码完成

## 7.完善问答平台

[Quora](https://www.quora.com/) 是一个问答网站，人们在这里分享他们的知识。每月用户超过 3 亿，是全球最受欢迎的问答平台之一。

Quora 使用机器学习算法来有效地挖掘数据，理解并给予用户他们想要的东西，并检测垃圾邮件——这有助于建立一个安全的提问环境。

通过使用自然语言处理技术，像 Quora 这样的平台可以挖掘用户之间的交互，并找出他们想要的东西。拥有这样一个有效的方法可以让企业改进他们的产品和服务。

# 如何在 JavaScript 项目中使用 ML

机器学习让很多程序员望而生畏。这通常很复杂、耗时，并且需要大量的设置。但是有一些方法可以让你在自己的 [JavaScript](/javascript-history-and-future-71b0ceb737aa) 项目中使用它，而不会让它变得如此困难。

有一些非常酷的 ML 库，如 TensorFlow.js、 [Synaptic](https://caza.la/synaptic/#/) 、 [Brain.js](https://brain.js.org/#/) 和 [ConvNetJS](https://cs.stanford.edu/people/karpathy/convnetjs/) ，它们有助于在浏览器中快速构建可扩展的人工智能应用程序。

例如，TensorFlow.js 允许您使用 [TensorFlow API](https://www.tensorflow.org/js/) 在您的 JavaScript 应用程序中创建/训练模型、构建图表和运行推理，而无需经历安装 C++或 Python 依赖项的麻烦。

如果你对使用这个库感到好奇，你可以遵循 Jeff Delaney 的这个教程:“[通过在 JavaScript 应用程序](https://fireship.io/lessons/tensorflow-js-quick-start/)中构建机器学习模型来开始使用 TensorFlow.js。”

## 案例研究:手势导航

通过遵循 Angular 团队的[Minko Gechev](https://www.youtube.com/watch?v=f2xav7GhoZA)的[简单演示](https://www.youtube.com/watch?v=f2xav7GhoZA)中的说明，我已经实现了在我自己的 Angular 应用程序(在本[教程](/angular-library-dynamic-stepper-2ba05ab40228)中实现)中控制订单创建的不同步骤之间的导航，而不是点击下一个和上一个按钮。

下面的步骤解释了你应该做些什么来获得类似的结果。

## **步骤 1。添加 TensorFlow.js**

安装 TensorFlow.js 和几个与之相关的包:tensorflow-models、core、converter 和 backend-webgl:

```
$ yarn add @tensorflow-models/handpose
           @tensorflow/tfjs-core
           @tensorflow/tfjs-converter
           @tensorflow/tfjs-backend-webgl$ yarn add fingerpose
```

## **第二步。更新库的对等相关性**

更新`dynamic-stepper`库下`package.json`中的`peerDependencies`:

## **第三步。添加视频和画布元素**

将`video`和`canvas`元素添加到`dynamic-stepper.component.html`(在/projects/dynamic-stepper 下)以渲染用户的视频和我们的机器学习模型的输出:

并将这些样式表行添加到`dynamic-stepper.component.scss`:

## **第四步。添加手势服务**

创建一个新的服务来封装手势逻辑。(我已经在一个新文件夹`ml`下创建了它。)

```
$ ng g s ml/hand-gesture
```

## **第五步。向您的组件添加手势逻辑**

将手势逻辑添加到`DynamicStepperComponent`。我想在这里添加一个手势，而不是点击“转到下一个”和“转到上一个”按钮，在不同步骤之间导航的可能性:

你可以在这个 [GitHub 资源库](https://github.com/rakia/ng-dynamic-stepper)中找到最终的源代码。

# 最终想法

机器学习对于理解用户行为很有用。它识别数据中的模式，以自动做出预测或采取行动。在个性化分析的环境中，它甚至更有用，因为有许多可能的变量需要预测和分析。

但是使用机器学习不仅仅是预测。ML 还可以通过代表用户做出决策来减轻工作量。训练有素的机器学习模型可以提高网站的 UX(用户体验)。这将有助于您的用户完成他们的任务，从搜索到功能发现和个性化。

希望这篇文章很有见地，能够帮助你理解 ML 在你的项目中的潜力。

感谢阅读！

🧠💡我为一群聪明、好奇的人写关于工程、技术和领导力的文章。 [**加入我的免费电子邮件简讯独家访问**](https://rakiabensassi.substack.com/) 或在此注册媒体。