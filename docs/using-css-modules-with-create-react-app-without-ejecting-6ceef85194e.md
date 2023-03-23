# 在不弹出的情况下使用 CSS 模块

> 原文：<https://betterprogramming.pub/using-css-modules-with-create-react-app-without-ejecting-6ceef85194e>

## 利用 CSS 模块避免类名冲突

![](img/e4efe9c3a9beb9a4c6449f45205a0cff.png)

照片由[freestocks.org](https://unsplash.com/@freestocks?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

"你如何在一个大项目中组织你的 CSS？"

这是我面试初级前端开发者时经常问的问题。你会惊讶有多少开发人员没有这个问题的答案。

随着应用程序的增长，类名冲突的风险也在增加。

我们来看一个具体的例子。

这里，我们在两个不同的 CSS 文件中使用了类名`**container**`。现在，我们赋予`**container**` className 的元素将这些样式混合在一起，这不是我们想要的。

在这个小例子中，它看起来很琐碎，很容易避免。但是当你和多个团队成员一起做一个大项目时，就不那么容易避免了。

## 在大型项目中，我们应该如何组织 CSS？

过去，开发人员依靠命名约定来处理 CSS 的全局性质。 [BEM](http://getbem.com/) (块元素修改器)是最流行的解决方案。

然而，所有这些解决方案都有相同的限制:它们依赖开发人员的勤奋来避免命名冲突。

如果不依赖开发人员使用唯一的类名，而是依赖一个在构建时由*为我们生成*类名的工具，会怎么样？幸运的是，这个工具已经存在了，它叫做 [CSS 模块](https://github.com/css-modules/css-modules)。

CSS 模块是一个 CSS 文件，默认情况下，所有的类名和动画名都在本地范围内

## 用`create-react-app`使用 CSS 模块

幸运的是，CSS 模块支持开箱即用的`create-react-app`。我们所要做的就是正确命名我们的 CSS 文件。如果我们导入一个扩展名为`.module.css`的文件，那么`create-react-app`会自动把它当作一个 CSS 模块。

```
npx create-react-app css-modules-app
```

在您最喜欢的编辑器中打开项目。将文件`App.css`重命名为`App.module.css`。接下来，将从`import './App.css'`的导入重命名为`import styles from './App.modules.css`。最后，在任何使用类名的地方，一定要访问`styles`对象，而不是直接访问字符串类名。

所以你应该写`className={styles['App']}`，而不是写`className="App"`。

接下来，运行您的应用程序并检查输出。你会注意到 CSS 模块已经为我们生成了类名。

类名`App-header`现在是`App_App-header__xLkWl`。前缀是文件名`**App**.module.css.`，然后添加一个下划线和我们原来的类名:`App-header.`，最后添加生成的散列`xLkWl`。这有助于确保我们的类名保持唯一。

## 编写全局样式

有时候你需要写全局风格。当您需要覆盖外部组件的样式时，这一点尤其重要。用 CSS 模块做到这一点非常容易。只需使用`:global`选择器从局部范围切换到全局范围。

## 撰写条件样式

CSS 模块非常适合条件样式。我们经常需要根据道具创建不同外观的组件。让我们以一个简单的`TextInput`组件为例。

为了让我们的生活更轻松，安装依赖项`classnames`

`yarn add classnames`

接下来，我们创建两个新文件`text-input.js`和`text-input-module.css`。

代码如下:

当属性`hasWarning`或`hasError`被传递给`TextInput`时，它会添加适当的类名。

# 结论

使用 CSS 模块允许我们编写本地范围的样式。这有助于我们防止类名之间的命名冲突。由于对 CSS 模块的支持被嵌入到`create-react-app`中，我们甚至不需要弹出来利用 CSS 模块。