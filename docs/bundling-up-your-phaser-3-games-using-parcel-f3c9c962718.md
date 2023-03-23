# 使用包裹捆绑你的 Phaser 3 游戏

> 原文：<https://betterprogramming.pub/bundling-up-your-phaser-3-games-using-parcel-f3c9c962718>

## 直观的布局节省了安装时间

![](img/479ad10df2cac04e66fda4a09b951187.png)

照片由[内特·格兰特](https://unsplash.com/@nateggrant?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/coding?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

作为初学者，当你想发展 Phaser 3 技能时，有时很难找到合适的资源。但它们仍然存在:教程让你兴奋地构建你一直梦想构建的平台。很多时候这些教程已经有几年的历史了。就我个人而言，我花了很多时间试图调试旧的、无人维护的教程，而我想做的就是直接进入 Phaser。

在本教程中，我想向您展示我当前的 Phaser 3 设置，它可以在几秒钟内启动我的项目，并依靠 Parcel 将一切打包得干净整洁。您应该能够将此设置应用于任何您想要的现有 Repo，或者替换 Webpack 配置，或者只是更新 Parcel。我们将使用 package 2 . 4 . 1，这是撰写本文时的最新版本。

# 先决条件

我们将在项目中使用两个包，我们将使用`yarn`或`npm`来安装它们。如果你不确定用哪一个，它可能是`npm`。在本文的其余部分，我将使用`yarn`，尽管它应该不会有太大的影响！

```
yarn add -D parcel parcel-reporter-static-files-copynpm i --save-dev parcel parcel-reporter-static-files-copy
```

这两种方法都会同时安装两个依赖项。如果一切进展顺利，我们可以跳到下一部分，看看应该如何设置项目来处理即将到来的示例。

## 文件夹结构

我当前的文件夹结构看起来像这样。当然，随着你在旅程中的进展，你的可能会看起来不一样，但对我来说，这是我每次开始的方式。

```
├── .parcelrc
├── .gitignore
├── package.json
├── src
│   ├── app.js
│   ├── assets
│   │   ├── audio
│   │   ├── fonts
│   │   └── images
│   │       └── example.png
│   ├── configs
│   │   ├── constants.js
│   │   └── phaserConfig.js
│   ├── index.html
│   ├── scenes
│   │   └── StartScene.js
│   └── styles.css
└── yarn.lock
```

# 配置宗地

我们将在两个地方配置包，从您的`package.json`文件开始。下面你会发现我的文件的完整版本。注意，如果您要将内容复制到您自己的 package.json 中，您需要在之后运行一个`npm install`。但是一般来说，我们不想这样做，因为最大的一部分`package.json`应该被生成而不是被粘贴。

## package.json

我们的`start`-脚本包含启动我们的开发环境的调用，在源文件夹中使用我们的`index.html`。`--open`参数会提示你的浏览器自己打开，这样你就可以马上开始了。

`build`做了更多的事情:`rm -rf .dist`确保每次我们创建一个新文件夹时，我们的`dist`文件夹都会被删除。这样我们避免了旧版本的大量堆积，并且只有最新的版本可用。请注意，如果您使用的是 Windows，您可能会在这一点上遇到问题。去掉`rm -rf .dist &&`就应该可以出发了。

`--no-source-maps`告诉宗地不要建，嗯——来源地图。我个人不需要我的 dist 文件夹中的额外文件，但是如果你想知道为什么你可能需要它们，[这个](https://firefox-source-docs.mozilla.org/devtools-user/debugger/how_to/use_a_source_map/index.html)是给你的。

我们需要注意的第二部分是我们的`package.json`中的`staticFiles`部分。这些选项对于处理我们的资产是必不可少的，所以当在我们的游戏中使用它们时，它们会出现在正确的地方。在我们的例子中，我们将整个`assets`文件夹从我们的源目录复制到我们新创建的`dist`文件夹的顶层。这是不言自明的，尤其是如果您让脚本运行并检查在`dist`文件夹中发生了什么。

## 。parcelrc

我们的最后一步是让 package 知道我们想要使用`parcel-reporter-static-files-copy`插件。为此，我们在项目的根目录下创建一个. parcelrc 文件，并用以下信息填充它:

您不需要完全理解这一点，但是如果您很好奇，可以查看 package 文档中的[插件部分](https://parceljs.org/features/plugins/#plugins)。

我希望这给了你一个关于如何有效地组织你的下一个项目的广泛概述。在我的 [GitHub](https://github.com/kpotschi/phaser-parcel-template) 上，整个库可以作为模板获得。

```
**Got any ideas?**Make sure to reach out via [Twitter](https://twitter.com/MelodyConnor2) if you have ideas for future guides or find any bugs along the way.
```