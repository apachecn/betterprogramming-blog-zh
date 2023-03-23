# 如何将单个 GitHub 存储库下的多个应用程序部署到 Heroku

> 原文：<https://betterprogramming.pub/how-to-deploy-multiple-apps-under-a-single-github-repository-to-heroku-f6177489d38>

## 使用一个存储库部署 React 和 Node/Express 应用程序

![](img/e567f77d88b3cb5064418582cd8c73df.png)

图片来自 [V 校](https://coursework.vschool.io/deploying-mern-with-heroku/)。

假设您正在开发一个全栈应用程序，其中客户端使用 React，服务器端使用 Node.js/Express.，您很可能会为每一个都拥有单独的 [GitHub](https://github.com/) 存储库。但是如果这两个都是子目录呢？让我们先来看看这个结构是什么样子的:

```
app (github repo)
  |__frontend (react application)
  |__backend (node/express application)
```

我使用这种方法来确定是否有可能将每个子目录部署到 Heroku。将应用程序作为子目录并不常见，理想情况下它们应该有自己的 GitHub 存储库。

假设我们将`app`初始化为存储库，将它部署到 Heroku 将不起作用。相反，目标是通过`app`的 GitHub 库将`frontend`和`backend` 部署到 Heroku。

本指南将介绍如何在一个 GitHub 资源库下部署多个应用程序。将假设应用程序已经被推送到 GitHub，并带有最近的更改。

# 先决条件

*   确保安装了 Heroku CLI 。

# 添加和/或重命名 Heroku 遥控器

我们将使用命令`heroku create`在 Heroku 上创建一个新的空应用程序。同时会自动创建一个名为`heroku`的遥控器(可以在终端中键入`git remote -v`进行检查)。

## 后端

我们将从建立`backend`子目录开始。在您的命令行界面中，导航到主`app`目录。到了那里，让我们用`heroku create app-backend`生成 Heroku 后端应用程序。我选择将 Heroku 上的应用命名为`app-backend`。您可以选择将其命名为任何名称，但是要确保它与其他命令保持一致。

如果您运行`git remote -v`但看不到遥控器`heroku`，您必须使用命令`heroku git:remote -a app-backend`添加遥控器。

下一步是重命名`heroku`遥控器，因为我们将有多个遥控器。运行命令`git remote rename heroku heroku-app-backend`。在这种情况下，我将其重命名为`heroku-app-backend`，但是您可以选择将遥控器重命名为什么。

## 前端

对于`frontend`存储库将采取类似的步骤，但是它需要用一个 buildpack 来创建。在终端中，仍然在`app`目录下，运行:

```
heroku create app-frontend -b https://github.com/mars/create-react-app-buildpack.git 
```

还是那句话，我选择把 app 命名为`app-frontend`。

如果您在名称上犯了错误，您可以随时使用以下名称对其进行重命名:

```
heroku apps:rename new_name --app old_name
```

如果你运行`git remote -v`并且没有看到遥控器`heroku`，你必须用命令`heroku git:remote -a app-frontend`添加它。

# 为正确的 Heroku 遥控器设置脚本

这一步可以被认为是可选的，但是通过设置这一步，以后向 Heroku 推送应用程序的新版本/更新版本会容易得多。因为我们有一个 React 和 Node/Express 应用程序，`npm`可以作为一个工具来帮助发布到 Heroku remotes。

在每个`frontend`和`backend`子目录中，我们可以更新`package.json`文件并添加命令来发布对 Heroku remote 的权限。让我们从`backend`开始。使用`cd backend`导航到该存储库，并打开`package.json`。我们将在`scripts`中添加以下内容:

```
"scripts": { "publishToHeroku": "cd ../ && git subtree push --prefix backend heroku- 
    app-backend master || true"}
```

我们来分解一下。`git subtree`命令必须从配置 git 的库的根目录运行。`push --prefix backend`指定了我们想要推送的目录。`heroku-app-backend master`指定遥控器的名称和要推送的分支的名称。`|| true`只是为了避免任何 npm 错误代码。

对`frontend`目录遵循相同的步骤，但是以下内容将被添加到`scripts`:

```
publishToHeroku: cd ../ && git subtree push --prefix frontend heroku-app-frontend master || true
```

*注意:如果在运行上面的命令时有任何问题，请确保您引用了正确的目录和 Heroku remotes 的名称。*

# 部署

在部署之前，如果您的应用程序的任何部分使用环境变量，请确保首先用 Heroku 设置它们。否则，部署的应用程序可能会崩溃。为此，从终端对每个环境变量使用`heroku config:set KEY=VALUE`命令，并用`heroku config`进行检查。

现在我们可以使用之前设置的`npm`和`script`命令将每个目录部署到 Heroku。让我们运行`cd backend`来导航到`backend`并运行`npm run publishToHeroku`。相同的步骤适用于`frontend`。

应该可以成功部署应用程序。

在我成功地使用这种方法进行部署之前，我确实面临着许多问题。一个问题是在部署之前没有设置环境变量。在已经部署之后设置它仍然会给我带来问题。我不得不再次推动和重新部署。

# 警告

本指南假设您正在使用`npm`作为内置工具，但是只要执行了正确的脚本，它应该可以与任何其他工具一起工作。如果您需要发布一个除了`master`之外的分支，您需要在`script`中进行修改，以匹配您想要部署的分支的名称。例如:

```
publishToHeroku: cd ../ && git subtree push --prefix frontend heroku-app-frontend <name_of_branch>|| true
```

如果你正在使用另一个没有`package.json`的框架，比如有一个 Rails 后端而不是 Node/Express 后端，你将不能设置一个脚本标签。相反，您可以尝试从根目录直接从终端运行该命令:

```
git subtree push — prefix backend heroku-app-backend master
```

我自己没有用 Rails 实现过这种方法，但是如果您正在将一个 Rails 应用程序部署到 Heroku，请记住数据库必须是`postgresql`。

# 结束语

如前所述，在 GitHub 上将应用程序作为子存储库并不常见。React 应用程序和 Node/Express 应用程序都应该有自己的存储库，但是为了便于组织，一些编码人员可能希望将两个存储库放在一个存储库中。通过一些额外的步骤，仍然有可能以这种方式部署到 Heroku。

感谢您的阅读。我希望这篇文章对你有用。

# 资源

*   [https://adampaxton . com/how-to-deploy-to-multiple-heroku-apps-from-the-same-git-repository/](https://adampaxton.com/how-to-deploy-to-multiple-heroku-apps-from-the-same-git-repository/)
*   [https://blog . heroku . com/deploying-react-with zero-configuration](https://blog.heroku.com/deploying-react-with-zero-configuration)