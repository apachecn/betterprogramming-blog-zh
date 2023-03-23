# 如何不需要私有的国家预防机制登记册

> 原文：<https://betterprogramming.pub/how-to-not-need-a-private-npm-registry-1327bfbe80c9>

## 从 git repo 安装 npm

![](img/0bcacc881587d84f24571a31bc16060e.png)

照片由[戴恩·托普金](https://unsplash.com/@dtopkin1?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

你有没有发现自己和我一样在网上寻找最便宜最简单的方法去做一件事？我花了几个小时研究如何使用 npm 而不使用 npm 来托管、部署和安装一个包。我想我找到了最便宜最优雅的解决方案，我希望你能从我学到的东西中受益。

简单的回答是:使用 Git 存储库。用哪个真的不重要。但它甚至可以是一个不公开的存储库。

使用 npm 管理您的包的好处是，您可以用一个命令安装所有的东西，并且它只会提供您需要导入的文件。

完成此操作的步骤如下。

1.  创建一个 Git 存储库(在本例中，我的存储库在 GitHub 中)
2.  在回购上运行`npm init`
3.  配置您的`package.json`
4.  在一个单独的应用程序中配置`package.json`,以指向回购协议

我将在这里跳过一些步骤，因为我将假设您知道如何做其中的一些。

我就直接给你看我的`package.json`长什么样。

# 配置 package.json

下面是我在 GitHub 上的组件库中的一个例子:

当提交组件库时，还需要确保将`build/index.js`文件添加到库中。这是您在`npm install`时需要并下载的文件。

我个人喜欢不用担心每次提交时都要构建的想法，所以我添加了`[husky](https://www.npmjs.com/package/husky)`库，并让它在每次提交时都构建。

# 自动构建和提交

`npm install husky --save-dev`

或者

`yarn add husky`

现在，把这个加到`your package.json`:

这将运行构建`pre-commit`。但是它也将确保添加构建文件来提交，而不会弄乱其他任何东西。

# 更新其他项目的 package.json 依赖项

现在，在您的另一个项目(您正在使用组件库)中，您可以将它作为一个依赖项添加，如下所示。

GitHub 位置可以是任何存储库位置。在这种情况下，回购是公共的，但也可以是私有的存储库。你可以使用 HTTPS 或者宋承宪。如果您使用的是私有存储库，那么您需要确保所有需要进行`npm install`的用户都允许保存在 SSH 目录中用于 HTTPS 认证的凭证。

要替换该 URL，您需要执行以下操作:

```
"{library name}": "git+{repository url}"
```

# **结论**

如果你试图创建一个私有的 npm 包，没有必要支付额外的托管费用——只需要从你的存储库中取出这个包。

希望这能帮到某个人！

干杯。