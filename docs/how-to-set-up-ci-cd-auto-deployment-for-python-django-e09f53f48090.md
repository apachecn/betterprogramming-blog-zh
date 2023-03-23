# 如何为 Django 设置 CI/CD

> 原文：<https://betterprogramming.pub/how-to-set-up-ci-cd-auto-deployment-for-python-django-e09f53f48090>

## 使用 Docker 和 Bitbucket 管道在每次提交后设置自动部署 AWS 实例

![](img/c3a0521ad4d29eab4b672ebb0c1af57b.png)

照片由 [Rodion Kutsaev](https://unsplash.com/@frostroomhead?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/pipeline?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

基本上，当我部署我的项目时，我每次都必须手动运行 Docker pull 命令来获得新的图像。然后，在将新的更改推送到我的 Bitbucket 存储库之后，我必须重启容器。我已经设置了一个 Bitbucket 管道，它为每次 Bitbucket 提交将新的映像推送到我的 ECR 注册表中。最后，我编写了一个 Bash 脚本来获取新的图像并重启我的 Docker 容器。

在这里，您将学习编写一个 crontab 脚本，该脚本检查 AWS ECR 中的新图像，并从 [AWS ECR](https://docs.aws.amazon.com/AmazonECR/latest/userguide/Repositories.html) 中提取新图像，用新图像启动容器。

在这里，我们主要关注以下事项:

1.  创建一个`Dockerfile`来构建 Docker 映像。
2.  设置一个 Bitbucket 管道，在每次提交时自动将 Docker 映像推送到 AWS ECR。
3.  在我们的 [EC2](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/concepts.html) 实例中创建一个`docker-compose.yml`文件，从 AWS ECR 中提取新图像。
4.  编写 [crontab](https://help.ubuntu.com/community/CronHowto) 脚本，每秒检查 AWS ECR 中的新图像。

# 1.创造一个`Dockerfile`来建立码头工人的形象

每当您提交对代码的新更改并推送到 Bitbucket 时，Bitbucket 管道允许您构建新的 Docker 映像以及对代码的新更改。它还将新图像推送到 AWS ECR。你应该把你的`Dockerfile`放在你的项目文件夹中来构建一个图像。

# 2.设置 Bitbucket 管道，在每次提交时自动将 Docker 映像推送到 AWS ECR

这里，Bitbucket 管道设置在 Docker 上。它使用`Dockerfile`构建图像，并将其推送到 AWS ECR。

# 3.在 EC2 实例中创建`docker-compose.yml`文件，从 AWS ECR 中提取新的图像

现在启动 AWS 实例，并安装 [AWS CLI](https://aws.amazon.com/cli/) 。之后，用`docker-compose.yml`文件创建一个目录文件夹，从 ECR 中提取新的图像并构建一个 Docker 容器。

# 4.编写 Crontab 脚本，每秒在 AWS ECR 中检查一个新图像

最后一步是编写一个 Bash 脚本，检查 Docker 容器中现有图像和 ECR 中存储的新图像的图像摘要 ID。每当 Docker 构建映像时，映像摘要 ID 都会改变。如果找到的映像 id 不同于从 ECR 中提取的新映像，脚本将匹配这两个映像 id。它还用新的图像重新启动 Docker 容器。下面是脚本的代码:`auto-deploy.sh`。

我们可以将`auto-deploy.sh`文件放在我们的 crontab 服务中，该服务在 crontab 中每秒检查一次新的映像，并在您提交 Bitbucket 中的更改时完成自动部署。日志消息显示在您的日志文件中:`deploy.log`。

# 结论

因此，我们有一个位桶管道来检测 Docker 文件并构建 Docker 映像。之后，它会在每次提交 Bitbucket 时将映像推送到 ECR。此后，在 crontab 中作为服务运行的脚本每秒检查一次新图像，如果发现新图像，则提取新图像，Docker 容器使用新图像自动启动。该脚本可以放在 crontab 中，如上所述。

我希望这篇文章对你有所帮助。感谢阅读！