# 使用 Heroku，通过 7 个简单步骤免费部署您的应用程序

> 原文：<https://betterprogramming.pub/deploy-your-app-for-free-in-7-easy-steps-thanks-to-heroku-dfd0f387edd0>

## Heroku 让部署变得简单、快速且免费

![](img/f90bbfad845937744b34a77a3c20c565.png)

[Artem Sapegin](https://unsplash.com/@sapegin?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/search/photos/react?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍照

我最近决定加强对 Vue.js 和 Nuxt.js 的学习，并参加了一个关于 Vue Mastery 的令人敬畏的课程，如果你对发现 Vue.jc 生态系统感兴趣，我强烈推荐这个课程(非常感谢 Gregg 和他的同事们的高质量课程)。

在构建了一个小的示例应用程序来发现 Nuxt 之后，我们在 Heroku 上部署了该应用程序，这种极快的体验给我留下了深刻的印象，我不得不在这里分享几个步骤供每个人使用。

用例很简单:你用你最喜欢的前端框架开发了一个应用程序，你想在生产中测试它，并把它展示给你的用户和你的妈妈看，而不需要考虑技术细节。Heroku 让您只需七个步骤就可以直接从远程回购服务您的应用程序，如下所示。

**先决条件:**首先你需要[安装 Heroku CLI](https://devcenter.heroku.com/articles/heroku-cli#download-and-install) 。安装完成后，请遵循以下步骤。

# 1.在 Heroku 上创建应用程序

在您的终端中，将您自己放置在应用程序文件夹中。在开始之前，确保所有内容都被推送到你的 GitHub 或其他远程 repo。运行命令:

```
$ heroku create
```

这将在 Heroku 上创建您的应用程序(使 Heroku 准备好接收您的源代码),并生成两个链接:

```
Creating app... done, ⬢ fakestuff-farboo-84560https://fakestuff-farboo-84560.herokuapp.com/ | https://git.heroku.com/fakestuff-farboo-84560.git
```

第一个链接是你可以看到你的应用的网址。第二个链接是 Heroku 提供的 GitHub repo，你需要把你的应用程序推到它上面来部署。

# 2.设置节点服务器配置

```
$ heroku config:set NPM_CONFIG_PRODUCTION=false
```

这个命令将告诉 Heroku 安装`devDependencies`。更准确地说，这跳过了修剪步骤，在运行时访问在`devDependencies`下声明的包。这使得 Heroku 能够发射`npm run build`。

# 3.听主持人`0.0.0.0`

```
$ heroku config:set HOST=0.0.0.0
```

# 4.在生产模式下运行节点

```
$ heroku config:set NODE_ENV=production
```

# 5.**叫 Heroku 跑**

然后，我们需要在我们的`package.json`文件中添加一个脚本来告诉 Heroku 运行`npm run build.`，只需将行`"heroku-postbuild": "npm run build”`添加到您的`package.json`中的`"script"`对象:

```
"scripts": {
  "dev": "nuxt",
  "build": "nuxt build",
  "start": "nuxt start",
  "generate": "nuxt generate",
  "lint": "eslint --ext .js,.vue --ignore-path .gitignore .",
  **"heroku-postbuild": "npm run build"** }
```

# 6.为 Heroku 创建一个 Procfile

Heroku 需要一个配置文件来执行和运行我们的应用程序。我们只需在 app 文件夹的根目录下创建一个没有扩展名的文件，将其命名为`Procfile`，并在其中添加以下代码行:

```
web: npm run start
```

`npm run start`是启动应用程序的脚本，`web`将 HTTP 流量导向应用程序。

# 7.**将您的 GitHub Repo 推送到 Heroku 进行部署**

最后，剩下要做的就是准备、提交并向 Heroku 推送我们配置好的应用程序:

```
$ git add Procfile
$ git commit -a -m "Configuration to deploy to heroku"
$ git push heroku master
```

而且是直播。推送和部署过程需要几分钟，然后 Heroku 会给你一个网址，你可以在那里看到你的应用。

我希望这是有用的。你也可以在官方 Nuxt.js 网站[这里](https://fr.nuxtjs.org/faq/heroku-deployment/)找到这些相同的步骤和一些额外的视频。

# 额外收获:定制域名

如你所见，默认情况下，Heroku 以`[name of app].herokuapp.com`的形式将你的应用部署在自己的域上。但是你绝对可以在非 Heroku 域中使用你的应用。所有你需要做的是添加一个自定义域，没有额外的费用。

你可以在 Heroku 文档[中找到这样做的五个步骤。](https://devcenter.heroku.com/articles/custom-domains)

部署愉快。