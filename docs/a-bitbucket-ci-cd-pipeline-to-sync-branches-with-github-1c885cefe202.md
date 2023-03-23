# 用 GitHub 同步分支的 BitBucket CI/CD 管道

> 原文：<https://betterprogramming.pub/a-bitbucket-ci-cd-pipeline-to-sync-branches-with-github-1c885cefe202>

## 在 BitBucket 和 GitHub repos 之间同步分支

![](img/8252926198f4ede0d2f40a7baa6d6c48.png)

由[塞萨尔·库托](https://unsplash.com/@xcrap?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

大多数时候，我们需要在两个不同的存储库之间同步分支(例如，BitBucket 存储库到 GitHub 存储库)。

这是在云存储库上工作时的常见场景，但是日常开发是通过本地存储库管理的。在这种情况下，本地存储库分支应该同步到云存储库进行部署。手动处理同步会产生很多问题，还会消耗开发团队更多的时间。更好的选择是自动化本地存储库和云存储库之间的分支同步。可以启用完整的存储库镜像，但这将同步每个分支。我的目标是只同步特定的分支。

![](img/78dd4416e6e539b69836e6ab485c3ea1.png)

作者照片。

在本教程中，我们将看到一种通过 BitBucket CI/CD 管道在 BitBucket 存储库和 GitHub 之间同步分支的方法。可以遵循相同的方法，只需进行最小的更改，就可以同步任意两个存储库的分支。

# 先决条件

*   比特桶存储库
*   空的 GitHub 存储库

# 设置位桶管道

我在 BitBucket 存储库中创建了一个名为`dev`的分支。对于任何变更，`dev`分支应该与 GitHub 存储库同步。在第一次同步时，`dev`分支将被创建在 GitHub 存储库中。

管道将在每一个被推送到指定分支的变更上执行(例如`dev`并将变更与远程 GitHub 存储库同步)。

登录到 BitBucket 存储库，单击“管道”，然后单击“创建您的第一个管道”:

![](img/63d724dff5e3d6c109fe8940b67eab7e.png)

选择“起始管道”:

![](img/55466835e5dbee6e4f314c73a9a0c391.png)

在`bitbucket-pipelines.yml` 中启用以下配置，并提交更改。该文件将被提交到主分支的根目录。将文件复制到其他需要的分支(如`dev`):

根据您的配置修改分支名称引用(`dev`、`dev1`和`uat`)。此外，相应地修改远程(GitHub)存储库 URL。

在提交对`dev`、`dev1`和`uat`分支的更改时，将触发管道，并执行以下特定于环境的脚本，以将本地分支(BitBucket)与远程存储库(GitHub)同步:

```
git remote add sync git@github.com:techforum-repo/test1.git - **add GitHub repository as the additional remote repository and tag with name "sync"**
git checkout dev - **Check out the latest changes from local dev bracnh( modify branch name accordingly), this step can be skipped as BitBucket pipeline already checked out the laetst branch**
git pull - **pull the latest change from local branch,**  **this step can be skipped as BitBucket pipeline already pulled out the laetst branch**   
git push sync dev - **push the local(BitBucket) branch to the remote GitHub repository (modify branch name accordingly)**
```

# 生成 SSH 密钥

远程存储库的 HTTPS Git URL([https://tech forum-repo:password @ github . com/tech forum-repo/test1 . Git](https://techforum-repo:password@github.com/techforum-repo/test1.git))可以与管道中的凭证一起使用，以将本地更改推送到远程存储库(将凭证存储在存储库或部署变量中，并在管道中引用它们)，但是最佳实践是使用 SSH URL([Git @ github . com:tech forum-repo/test1 . Git](http://git@github.com:techforum-repo/test1.git))。

SSH 密钥应该配置为启用基于 SSH 的集成。第一步，通过执行下面的命令生成 SSH 密钥(可以通过 GIT bash 执行)。如果需要，可以通过 BitBucket UI 生成 SSH 密钥:

```
ssh-keygen -t ed25519 -C "[test@techforum.com](mailto:test@techforum.com)"
```

![](img/f317c3a591e0b9c0fbf6f225bda6320e.png)

这将在下生成公钥(id_ed25519.pub)和私钥(id_ed25519)。ssh 文件夹。

# 设置 SSH 密钥— GitHub

现在登录 GitHub 存储库并创建一个新的部署密钥:

![](img/d9a23b4f0b27acadfc404212f9b73054.png)

给键添加一个标题(`sync-bitbucket`)。从文件`id_ed25519.pub`中输入公钥。

选择“允许写访问”点击“添加密钥”:

![](img/2fef4b42a58ad4f354b4e51c0edad9f2.png)

# 设置 SSH 密钥—位桶

登录 BitBucket 存储库并点击“存储库设置”:

![](img/31a8f0f81de176c1825699abd9c7a47d.png)

点击“SSH 密钥”和“使用我自己的密钥”(如前所述，您可以使用“生成密钥”按钮来生成私有和公共密钥)。

从`id_ed25519`文件添加私钥，从`id_ed25519.pub`文件添加公钥，并保存密钥对。

![](img/3ce2921b5d8f90a875126513afdab9fe.png)

现在将一些更改推送到 BitBucket 中的一个分支(例如`dev`)。这将触发相应的管道步骤:

![](img/469f63fa9b12be8663e8c7c03e435f42.png)![](img/c9b8889e079612a7131f1b5b3c089ea4.png)

一旦管道成功完成，来自 BitBucket 存储库的特定分支(例如`dev`)将被同步到 GitHub 存储库(相应的分支将在 GitHub 中为第一次同步创建):

![](img/2c80b3ae1300e0a842b6449753259654.png)

CI/CD 管道可以帮助我们在两个不同的存储库之间同步分支。这里，我们将特定的分支从 BitBucket 存储库同步到 GitHub 存储库，但是可以遵循相同的方法来同步两个不同的存储库。如果需要，可以使用外部 CI/CD 工具(例如 Jenkins)来同步两个不同的存储库。

在下一篇教程中，我们将探索如何使用 Jenkins 管道在两个存储库之间同步分支。