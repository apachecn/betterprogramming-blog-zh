# 安装特定版本的节点。JS 包

> 原文：<https://betterprogramming.pub/install-a-specific-version-of-a-node-js-package-272a624a0c61>

## 学习如何用 npm 和 yarn 来做

![](img/47fa8ffb922ec27b37cd45934e3c55e5.png)

照片由[潘卡杰·帕特尔](https://unsplash.com/@pankajpatel?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

通常，当您想要使用某个软件包时，您会安装它的最新版本。但是在某些情况下，您可能需要安装特定的软件包版本。

一个例子是当包中的最新变化与你的代码不兼容时。

本文向您展示了如何安装特定的包版本来避免这种情况。你将学会使用`yarn`和`npm`来做这件事。

## 使用 npm

使用 npm，您可以按如下方式安装软件包:

```
npm install package_name

// or

npm i package_name
```

**注意** : `npm i`是`npm install`的快捷方式。

以上命令安装指定软件包的最新版本。

但是如果你想安装一个特定的版本呢？安装特定软件包版本的语法如下:

```
npm install package_name@version_number
```

运行上述命令时，npm 会安装在“@”符号后指定的版本。

## 包版本

如果您想要查看软件包的所有版本，可以使用以下命令:

```
npm view package_name versions
```

这将列出从第一个到最后一个的所有版本。

## 使用纱线

使用纱线，您可以完成如下相同的任务:

```
yarn add package_name@version
```

想法是一样的，但是语法不同。