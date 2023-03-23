# 使用 GitHub 事件和动作创建一个复古留言簿页面

> 原文：<https://betterprogramming.pub/create-a-retro-guestbook-page-using-github-events-and-actions-d60999a5a782>

## 用一点点命令行魔法从晦涩中拯救 GitHub 存储库事件的指南

![](img/0a50a2dc77ffd3ba691f010e1d3dd719.png)

[亚历山大·波波夫](https://unsplash.com/@5tep5?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/techno?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍照

在 GitHub 上工作的团队依靠事件数据进行协作。记录为问题、拉动请求和注释的数据对于理解项目变得至关重要。

有了 GitHub 动作，我们就有机会以编程方式访问和保存我们存储库中的 GitHub 事件数据。让数据成为存储库本身的一部分是在 GitHub 之外保存数据的一种方式——它还让我们能够在一个面向前端的网站上展示数据，例如 GitHub 页面。

而且，如果你像我一样，你可以把 [GitHub 问题评论](https://github.com/victoriadrake/github-guestbook/issues/1)变成[牛逼的 90 后留言簿页面](https://victoria.dev/github-guestbook/)！

不管是什么用法，基本概念都是一样的。我们可以使用动作来访问、保存和显示 GitHub 事件数据——只需一个工作流文件。为了说明这个过程，我将带你浏览一下让我的留言簿闪闪发光的[工作流代码](https://github.com/victoriadrake/github-guestbook/blob/master/.github/workflows/publish-comments.yml)。

关于 GitHub 动作的介绍，包括如何触发工作流，请参见[使用 GitHub 动作构建工具无关的 CI/CD 流](https://medium.com/better-programming/build-a-tool-agnostic-ci-cd-flow-with-github-actions-3d4f4a6238de)。

# 访问 GitHub 事件数据

动作工作流在具有一些默认环境变量的环境中运行。这里有很多方便的信息，包括事件数据。访问事件数据最完整的方法是使用`$GITHUB_EVENT_PATH`变量，这是包含完整 JSON 事件有效负载的文件的路径。

展开的路径看起来像`/home/runner/work/_temp/_github_workflow/event.json`，它的数据对应于它的 webhook 事件。你可以在 GitHub REST API [事件类型和有效载荷](https://developer.github.com/webhooks/#events)中找到 webhook 事件数据的文档。为了使 JSON 数据在工作流环境中可用，您可以使用类似于`jq`的工具来解析事件数据并将其放入环境变量中。

下面，我从一个[问题评论事件](https://developer.github.com/v3/activity/events/types/#issuecommentevent)中抓取评论 ID:

```
ID="$(jq '.comment.id' $GITHUB_EVENT_PATH)"
```

大多数事件数据也可以通过`[github.event](https://help.github.com/en/github/automating-your-workflow-with-github-actions/contexts-and-expression-syntax-for-github-actions#github-context)` [上下文变量](https://help.github.com/en/github/automating-your-workflow-with-github-actions/contexts-and-expression-syntax-for-github-actions#github-context)获得，无需解析 JSON。使用点符号访问这些字段，如下例所示，我获取了相同的注释 ID:

```
ID=${{ github.event.comment.id }}
```

对于我的留言簿，我想显示带有用户句柄以及日期和时间的条目。我可以像这样捕获这个事件数据:

```
AUTHOR=${{ github.event.comment.user.login }}
DATE=${{ github.event.comment.created_at }}
```

Shell 变量可以方便地访问数据，但是它们是短暂的。每次运行都会重新创建工作流环境——甚至在一个步骤中设置的 shell 变量也不会持续到其他步骤。要持久化捕获的数据，您有两个选择:使用工件，或者将它提交给存储库。

# 保留事件数据:使用工件

使用工件，您可以在工作流作业之间持久化数据，而无需将数据提交到存储库。例如，当您希望在将数据放在更永久的地方之前转换或合并数据时，这是很方便的。有必要在工作流作业之间保存数据，因为:

> 工作流中的每个作业都在虚拟环境的一个新实例中运行。当作业完成时，运行程序终止并删除虚拟环境的实例。 *—* [*使用工件*](https://help.github.com/en/github/automating-your-workflow-with-github-actions/persisting-workflow-data-using-artifacts) *持久化工作流数据。*

两个动作帮助使用工件:`upload-artifact`和`download-artifact`。您可以使用这些操作使文件对同一工作流程中的其他作业可用。完整示例见[在工作流程](https://help.github.com/en/github/automating-your-workflow-with-github-actions/persisting-workflow-data-using-artifacts#passing-data-between-jobs-in-a-workflow)中的任务间传递数据。

`upload-artifact`动作的`action.yml`包含关键字的[解释](https://github.com/actions/upload-artifact/blob/master/action.yml)。上传的文件以`.zip`格式保存。同一个工作流运行中的另一个作业可以使用`download-artifact`动作来利用另一个步骤中的数据。

您也可以在存储库的“操作”选项卡下的“工作流运行”页面上手动下载归档文件。

在作业之间保存工作流数据不会对存储库文件进行任何更改，因为生成的工件只存在于工作流环境中。就我个人而言，在 shell 环境中工作很舒服，我看到了工件的一个狭窄的用例，尽管我没有提到它们。除了在任务之间传递数据之外，它们还可以用来创建`.zip`格式的文档，比如说，测试输出数据。在我的留言簿示例中，我只是在一个作业中运行了所有必要的步骤，不需要在作业之间传递数据。

# 保留事件数据:将工作流文件推送到存储库

为了将工作流中捕获的数据保存在存储库中，有必要将这些数据添加并推送到 Git 存储库中。您可以在工作流中通过使用 shell 命令创建包含数据的新文件或将数据附加到现有文件来实现这一点。

# 在工作流程中创建文件

要在工作流程中处理存储库文件，使用`[checkout](https://github.com/actions/checkout)` [动作](https://github.com/actions/checkout)获取一个副本进行处理:

```
- uses: actions/checkout@master
  with:
    fetch-depth: 1
```

为了给我的留言簿添加评论，我将 shell 变量中捕获的事件数据转换成适当的文件，在 [shell 参数扩展](https://www.gnu.org/software/bash/manual/html_node/Shell-Parameter-Expansion.html)中使用替换来净化用户输入并将换行符转换成段落。我以前写过[为什么应该小心对待用户输入](https://victoria.dev/blog/sql-injection-and-xss-what-white-hat-hackers-know-about-trusting-user-input/)。

```
- name: Turn comment into file
  run: |
    ID=${{ github.event.comment.id }}
    AUTHOR=${{ github.event.comment.user.login }}
    DATE=${{ github.event.comment.created_at }}
    COMMENT=$(echo "${{ github.event.comment.body }}")
    NO_TAGS=${COMMENT//[<>]/\`}
    FOLDER=comments printf '%b\n' "<div class=\"comment\"><p>${AUTHOR} says:</p><p>${NO_TAGS//$'\n'/\<\/p\>\<p\>}</p><p>${DATE}</p></div>\r\n" > ${FOLDER}/${ID}.html
```

通过使用`printf`并使用`>`将其输出定向到一个新文件，事件数据被转换成一个 HTML 文件，用注释 ID 号命名，包含捕获的事件数据。格式化后，它看起来像这样:

```
<div class="comment">
    <p>victoriadrake says:</p>
    <p>This is a comment!</p>
    <p>2020–02-04T00:28:36Z</p>
</div>
```

使用注释时，使用注释 ID 命名文件的一个效果是，具有相同 ID 的新文件将覆盖以前的文件。这对于留言簿来说很方便，因为它允许对评论进行任何编辑来替换原始的评论文件。

如果你使用像 Hugo 这样的静态站点生成器，你可以构建一个 markdown 格式的文件，把它放在你的`content/`文件夹中，常规的站点构建会处理剩下的事情。在我的简单留言簿的例子中，我有一个额外的步骤将各个评论文件合并到一个页面中。每次运行时，它都会用`header.html`部分(`>`)覆盖现有的`index.html`，然后按照降序查找并追加(`>>`)所有注释文件的内容，最后追加`footer.html`部分以结束页面。

```
- name: Assemble page
  run: |
    cat header.html > index.html
    find comments/ -name "*.html" | sort -r | xargs -I % cat % >> index.html
    cat footer.html >> index.html
```

# 向存储库提交更改

由于`checkout`操作与克隆存储库并不完全相同，所以在撰写本文时还有一些[问题](https://github.community/t5/GitHub-Actions/Checkout-Action-does-not-create-local-master-and-has-no-options/td-p/31575)需要解决。对于`pull`、`checkout`，需要几个额外的步骤，并且成功地将`push`改变回`master`分支，但是这在 shell 中是相当普通的。

下面是添加、提交和将工作流所做的更改推回到存储库的`master`分支的步骤。

```
- name: Push changes to repo
  run: |
    REMOTE=https://${{ secrets.GITHUB_TOKEN }}@github.com/${{ github.repository }}
    git config user.email "${{ github.actor }}@users.noreply.github.com"
    git config user.name "${{ github.actor }}" git pull ${REMOTE}
    git checkout master
    git add .
    git status
    git commit -am "Add new comment"
    git push ${REMOTE} master
```

实际上，远程存储库是使用`github.repository`上下文变量指定的。为了让我们的工作流被允许推送到 master，我们使用默认的 `[secrets.GITHUB_TOKEN](https://help.github.com/en/github/automating-your-workflow-with-github-actions/authenticating-with-the-github_token)` [变量](https://help.github.com/en/github/automating-your-workflow-with-github-actions/authenticating-with-the-github_token)。

由于工作流环境是崭新的，我们需要配置 Git。在上面的例子中，我使用了`github.actor`上下文变量来输入启动工作流的帐户的用户名。使用[默认](https://help.github.com/en/github/setting-up-and-managing-your-github-user-account/setting-your-commit-email-address#setting-your-commit-email-address-on-github) `[noreply](https://help.github.com/en/github/setting-up-and-managing-your-github-user-account/setting-your-commit-email-address#setting-your-commit-email-address-on-github)` [GitHub 电子邮件地址](https://help.github.com/en/github/setting-up-and-managing-your-github-user-account/setting-your-commit-email-address#setting-your-commit-email-address-on-github)类似地配置电子邮件。

# 显示事件数据

如果你使用的 GitHub 页面带有默认的`secrets.GITHUB_TOKEN`变量，并且没有站点生成器，那么在工作流中将变更推送到存储库只会更新存储库文件。GitHub 页面构建将失败，并显示错误消息“您的站点在构建时遇到问题:页面构建失败。”

要启用触发 Pages 站点构建的操作，您需要创建一个个人访问令牌。这个令牌可以[作为一个秘密存储在存储库](https://help.github.com/en/github/automating-your-workflow-with-github-actions/creating-and-using-encrypted-secrets)设置中，并传递到工作流中，代替默认的`secrets.GITHUB_TOKEN`变量。在这篇文章中，我写了更多关于[动作环境和变量的内容。](https://victoria.dev/blog/a-lightweight-tool-agnostic-ci/cd-flow-with-github-actions/#environment-and-variables)

通过使用个人访问令牌，由操作工作流发起的推送也将更新页面站点。你可以通过[在我的](https://github.com/victoriadrake/github-guestbook/issues/1)[留言簿](https://victoria.dev/github-guestbook/)里留言来亲眼看看！评论创建事件触发工作流，然后需要大约 30 秒到 1 分钟来运行和更新留言簿页面。

在需要站点构建来发布变更的情况下，比如使用 Hugo 时，动作也可以做到这一点。然而，为了避免产生意外的循环，[一个动作工作流不会触发另一个](https://help.github.com/en/github/automating-your-workflow-with-github-actions/events-that-trigger-workflows#about-workflow-events)。相反，用 Makefile 处理[构建站点的过程非常方便，任何工作流都可以运行它。只需将运行 Makefile 作为工作流作业的最后一步，必要时使用存储库标记:](https://victoria.dev/blog/a-portable-makefile-for-continuous-delivery-with-hugo-and-github-pages/)

```
- name: Run Makefile
  env:
    TOKEN: ${{ secrets.GITHUB_TOKEN }}
  run: make all
```

这确保了工作流的最后一步是构建和部署更新的站点。

# 不再有事件数据范围

GitHub Actions 提供了一种捕捉和利用事件数据的简洁方式，因此它不仅在 GitHub 中可用。可能性只受你想象力的限制！这里有一些让我们创造的东西的想法:

*   面向公众的问题板，没有 GitHub 帐户的客户可以在这里查看项目问题并给出反馈。
*   任何存储库的新问题、评论或 PRs 的自动更新 RSS 源。
*   一个静态站点的评论系统，利用 GitHub 发布评论作为输入方法。
*   一个很棒的 90 年代留言簿页面！

我说过我做了一个 90 年代的留言簿页面吗？我内心深处的城市呆子有点兴奋！