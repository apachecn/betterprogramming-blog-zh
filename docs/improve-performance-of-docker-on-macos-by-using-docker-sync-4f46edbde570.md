# 使用 Docker-Sync 提高 MacOS 上 Docker 的性能

> 原文：<https://betterprogramming.pub/improve-performance-of-docker-on-macos-by-using-docker-sync-4f46edbde570>

## Mac 上的 Docker 可能是一个资源猪，但它不一定是

![](img/96a3fafea357dac1733105e34edbd1b5.png)

[Md Mahdi](https://unsplash.com/@mahdi17?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/mac?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

如果您已经将开发环境迁移到 Docker，并且您在 Mac 上，您可能会注意到您的 web 应用程序堆栈比您已经习惯的另一个本地环境要慢。事实是，与 Linux 机器相比，Docker for Mac 的 IO 性能非常慢。

在 OSX/Windows 下用 docker 开发是一件非常痛苦的事情，因为将代码共享到容器中会降低代码执行的速度。为了解决性能问题，我们可以使用`[docker-sync](http://docker-sync.io/)` *。*为了展示如何在我们的应用程序中设置 *docker-sync* ，我们举一个 Rails 应用程序的例子。

在我的 rails 应用程序中，我有一个如下配置的`docker-compose.yml` :

首先，你需要安装`docker-sync`宝石:

```
sudo gem install docker-sync
```

然后你需要创建`docker-sync.yml`。这将具有以下配置，src 入口点将用作`docker-compose.yml`中的外部卷:

一旦你创建了`docker-sync.yml`，你需要更新`docker-compose.yml`文件:

因此，整个`docker-compose.yml`文件将如下所示:

要启动容器，您可以使用`docker-sync-stack start` *—* 该命令将启动您的同步过程以及服务器。

如果您想先启动同步过程，然后分别启动容器，可以使用以下命令:

```
docker-sync clean
docker-sync start
docker-compose up
```

设置好`docker-sync` 之后，您可能会遇到这样的问题:在数据库容器启动之前，您的主应用程序容器试图启动并运行。如果发生这种情况，那么您的主应用程序容器将无法启动。为了防止这种情况，我们可以对您的`*docker-compose.yml*` 文件进行以下更改。

```
command: bash -c "while !</dev/tcp/db/5432; do sleep 1; done; rm -f tmp/pids/server.pid && bundle exec rails s -p 3000 -b '0.0.0.0'"
```

这个配置是可选的——这完全取决于如何在`docker-compose.yml`文件中设置依赖容器。通过使用这种配置，除非数据库容器已经启动，否则主应用程序容器不会启动。

这个设置对我们来说非常好，使我们能够在 Mac 上运行 Docker。