# 如何创建一个苗条的包

> 原文：<https://betterprogramming.pub/creating-a-package-for-svelte-f44fccbc886b>

## 这很容易，你能做到！

![](img/bd8532a3e12d9ed1aca9e2ef24d0e6af.png)

由[杰西·拉米雷斯](https://unsplash.com/@jesseramirezla?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/package?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

# 介绍

这篇文章的想法是展示一种快速、实用和简单的方法来创建一个可以通过我们的项目中的 [npm](https://www.npmjs.com/) 下载的包。

我最近完成了我的第一个包，事实是，虽然网上的信息是完整的，但我想用简单而简短的方式解释我的过程。我没有找到很多文章解释如何让包工作苗条，所以这里是我的尝试。

# 1.先决条件

首先，需要做到以下几点:

*   在 [GitHub](https://github.com/) 上的一个账号
*   在 [npm](https://www.npmjs.com/) 的账户
*   安装在计算机上的[节点](https://nodejs.org/es/download/)、 [npm](https://www.npmjs.com/get-npm) 和 [GIT](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)

# 2.这个想法

在你开始做任何事情之前，有一个你想要做什么的想法是很好的，这样你就不会浪费时间和步骤去做和解开这个包。不管多基础，想一个你可以先用的东西，既可以用也可以练，而且你愿意上传。

不要担心是否已经做了，大多数事情可能已经做了，只是试着把你的旋转放在上面。

# 3.开始打包

现在，您可以在 PC 上创建一个带有该包名称(或您喜欢的名称)的文件夹，并打开您喜欢的编辑器。

在我的例子中，我使用 Visual Studio 代码，它有一个内置的终端。如果您使用不同的目录，请打开节点控制台并转到您刚刚创建的目录。

现在他们必须跑。

这将创建一个`package.json`文件来存放关于这个包的信息，比如名称、作者、描述、它使用的依赖项、版本等等。

```
{
      "name": "",
      "version": "1.0.0",
      "description": "",
      "main": "index.js",
      "scripts": {
        "test": "echo "Error: no test specified" && exit 1"
      },
      "keywords": [],
      "author": "",
      "license": "ISC"
}
```

您可以填写名称、描述、关键字、作者和[许可证](https://help.github.com/en/enterprise/2.18/user/github/creating-cloning-and-archiving-repositories/licensing-a-repository)等字段。

现在我们可以安装将使用我们的包的依赖项了。请注意，这是一个基本代码，这意味着我们将安装基本的依赖项。

在您的终端中执行以下命令:

`npm install -D rollup rollup-plugin-node-resolve rollup-plugin-svelte svelte`

这将安装像`devDependencies (*-D*)`这样的基本依赖项，所以它们将只用于开发。

`package.json`将添加以下字段:

```
"devDependencies": {
    "rollup": "^1.28.0",
    "rollup-plugin-node-resolve": "^5.2.0",
    "rollup-plugin-svelte": "^5.1.1",
    "svelte": "^3.16.7"
}
```

# 4.包裹

现在我们将在目录的根目录下的`index.js`中创建一个文件夹`src`。

我们将以下代码放在该文件中:

```
import Name from './Name.svelte';

export default Name;
```

其中`Name`是您想要给组件起的名字。

然后，我们在文件夹`src`中创建`Name.svelte`，其中`Name`与`index.js`中的相同。

一旦进入。苗条的文件，它是由你来建立你想出的想法的组成部分。在这里它只作为一个普通的。苗条的页面，有风格的脚本块和 HTML。

你可以毫无问题地导入苗条的元素。

# package.json

我们将对该文件进行一些更改，添加以下内容:

```
"main": "dist/index.js",
"module": "dist/index.mjs",
"svelte": "src/index.js",
"scripts": {
    "build": "rollup -c",
    "dev": "rollup -c -w"
},
```

在`main`中，我们将在运行构建脚本后设置输出(不需要创建`/dist`目录，它是自动创建的)

在`module`中，我们将定义相同的东西，但是输出一个`.mjs`文件，以便 Node 可以区分用 CommonJS 和 ES6 创建的模块。更多信息请点击这里。

在 Svelte 中，我们将定义前面创建的`index.js`文件的路径。

然后我们将定义脚本(如果你熟悉 Svelte，你就会知道它们)。

# rollup .配置. js

我们将创建另一个文件，但这次是在根级别(`src`文件夹和`package.json`所在的位置)，我们将其命名为`rollup.config.js`。

如果你以前用过 Svelte，你就已经知道这个文件了。

我们将在运行构建脚本时运行这个文件，并向它传递一些指令:

1.  在前两条生产线中，我们出口了之前安装的两台设备。
2.  然后，在常数`pkg`中，我们带来了`package.json`。
3.  然后我们告诉他，他将输入的输入是`.svelte`文件(再次将`Name`改为他的文件名)，输出将是`pkg.module` ——这是我们之前放在 package.json `"module": "dist/index.mjs"`和 main `"main": "dist/index.js"`中的模块值，也将`Name`值改为这个组件的值。
4.  然后我们执行这两个插件。

# 5.本地测试

有一种方法可以在上传到 npm 之前在本地测试我们的包。

1.  在你的电脑上创建一个新文件夹，然后[安装 Svelte](https://svelte.dev/) 。
2.  从终端，在您的包的目录中，运行命令`npm link`
3.  从我们在步骤 1 中安装 Svelte 的新创建的文件夹中，打开位于该目录中的新终端并运行命令`npm link /path/of/your-package`。

这将允许我们在本地测试这个包。此外，如果我们对它进行任何修改，也不需要更新或安装——它会自行更新。

# 6.在 npm 中发布

做完这些之后，我们就可以发布这个包了，并可以通过 npm 进行安装。

建议在根目录下创建一个文件`README.md`,包含关于我们的包的信息——如何安装、如何使用、配置等等。这个是你可以使用的好例子。

记住，npm 的`README.md`和 GitHub 库的一样。

# 出版

1.  在终端中，在我们的包的目录中，执行命令`npm adduser`，它将要求我们的帐户的凭证。
2.  然后运行`npm publish`。就是这样！

重要提示:在我们再次运行`npm publish`之前，我们必须更改`package.json`中的版本。

```
"version": "1.0.0"
```

# 7.在 GitHub 上发布

要将包放入我们的 GitHub 库，让我们执行以下操作:

1.  在包的根目录下创建一个`.gitignore`文件，包括以下内容:

```
# Directories
node_modules/
dist/
```

这防止了 dist 文件夹和`node_modules`文件夹被发布。

1.  在我们的 GitHub 帐户中，我们用我们的包的名字创建了一个存储库，并且我们把那个没有创建的 README.md 文件。
2.  我们在终端中执行以下命令:

```
git init
git remote add origin https://github.com/tu-usuario/el-nombre-del-repositorio.git
git add .
git commit -m "First Commit"
git push -u origin master
```

这将把我们的文件上传到存储库*(除了我们放在* `*.gitignore*` *文件中的文件)。*

# 推荐

一旦创建了我们的存储库并且发布了我们的包，向我们的 package.json 文件中添加以下几行是非常有用的:

```
"repository": {
    "type": "git",
    "url": "https://github.com/tu-usuario/el-nombre-del-repositorio"
},
"homepage": ""
"bugs": {
    "url": "https://github.com/tu-usuario/el-nombre-del-repositorio/issues"
}
```

这也将被添加到我们包的 npm 页面、`Homepage`和`Repository`字段，直接链接到我们的包站点和 GitHub 库。

# 结论

在发布这个包之前，还能做更多的事情吗？是的，你可以运行测试，拥有一个`LICENSE`文件，在`package.json`中添加[预构建和预发布](https://docs.npmjs.com/misc/scripts)脚本等等。

但这篇文章的想法是，至少你知道如何开始，然后由你来给这个过程添加更多的东西。