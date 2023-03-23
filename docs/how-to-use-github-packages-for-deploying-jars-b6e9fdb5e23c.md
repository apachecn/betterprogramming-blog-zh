# 如何使用 Github 包部署 jar

> 原文：<https://betterprogramming.pub/how-to-use-github-packages-for-deploying-jars-b6e9fdb5e23c>

## 访问您在当前项目中部署到 Github 包的所有类

![](img/fb33678ca99b1ab01cd8e22ec484e966.png)

照片由[克劳迪奥·施瓦茨](https://unsplash.com/@purzlbaum?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

在拥有多个微服务的项目中，每个微服务都有不同的部署。一些你用来连接 REST APIs 的微服务和一些你在项目中直接用作库依赖的 ***微服务。***

在本文中，我将向您展示如何使用 Github 包来创建和部署您的工件 jar，以及如何在您的项目中使用这个工件 jar 并进行适当的版本控制。

## 创建 jar 并部署

你需要做的第一件事是修改你的`pom.xml`

在这里，我们已经定义了我们的工件 jar 将要到达的分发路径。

现在我们只需要创建一个 GitHub 动作工作流，当我们在 GitHub 中创建一个发布时，它将发布一个 jar。

正如你在上面看到的，我们在 GitHub 行动中有两个任务。

当合并到 master 时，它将构建我们的 jar 以确保我们没有任何编译错误。

当我们在 GitHub 中创建一个新版本时，我们会触发一个任务，用`mvn -B package --file pom.xml`构建 jar，并用`mvn deploy`将生成的 jar 部署到 GitHub 包中

当这项工作完成时，您将能够在 Github repo 的 Github Packages 部分看到这个新创建的 jar。

## 使用 Github 包中的 jar

现在您已经将这个 jar 部署到 Github 包中，是时候在您的另一个项目中使用这个 jar 了。让我们看看如何实现这一目标。

在您的项目中，您需要在`pom.xml`中添加以下内容

```
<repositories>
    <repository>
        <id>github</id>
        <name>GitHub Apache Maven Packages</name>
        <url>https://maven.pkg.github.com/{github_org}/{github_repo_name}</url>
    </repository>
</repositories>
```

然后，您可以在依赖项部分添加依赖项:

```
<dependency>
    <groupId>com.example.repo</groupId>
    <artifactId>common</artifactId>
    <version>{version_name}</version>
</dependency>
```

现在您应该能够访问您在当前项目中部署到 Github 包的所有类了。

希望这有所帮助。