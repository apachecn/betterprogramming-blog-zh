# 如何在 Podman 上用 Gunicorn 和 Nginx 运行 Flask API 应用容器

> 原文：<https://betterprogramming.pub/how-to-run-a-flask-api-application-container-with-gunicorn-and-nginx-on-podman-6aec86e58b84>

## 在 Flask 中创建的简要指南

![](img/934c286ad28326c52c601bb4a522eaef.png)

照片由[瑞典蓝水](https://unsplash.com/@bluewaterglobe?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

我们已经确定了我们希望如何按照这个[故事](/organizing-flask-api-with-blueprint-c1c76b025d5d)构建我们的应用程序。我改进了应用程序如何使用数据库凭证的代码。Flask 在本地运行时使用内置的 dev 服务器，这不是[推荐](https://flask.palletsprojects.com/en/2.1.x/deploying/)用于生产的。

您必须为应用程序配置一个 WSGI 服务器来服务生产流量。有几个可用的 WSGI 服务器，但是我选择了 Gunicorn，因为它比较成熟。然而，Gunicorn 主要不是为静态内容和图形服务的。我们应该让行业中的 HTTP 老大 Nginx 作为反向代理来处理所有的流量请求和负载平衡，而不是让 Gunicorn 来处理这些任务。

因此，我们将在一个 podman pod 中并排运行两个容器，流量将通过 Nginx 到达应用程序容器。

下面，Dockerfile 将基于`ubi/python38`映像创建应用程序映像，运行时，gunicorn 服务器将按照`appentry.sh`文件中的定义启动。

Nginx 服务器必须配置为向应用程序容器发送流量。在 Nginx 配置文件中，包含一些配置，告诉 Nginx 将通过/路由接收的所有流量发送到位于`port 8000`的 gunicorn 服务器。这些配置将按照 docker 文件添加到 Nginx 映像中。

我们有`govt_structure`应用程序和 Nginx 的代码库和 docker 文件。我们将在`port 8181`上创建一个 pod，并将应用程序容器部署到 pod。运行以下脚本来执行这些任务:

最后，我们的应用程序启动并运行，可以在[http://localhost:8181/say hello](http://localhost:8181/sayhello.)进行测试(`/sayhello`只是返回一条消息)。

感谢您的阅读。你可以在政府部门的我的 [GitHub repo](https://github.com/Wainaina3/publications.git) 中找到完整的代码。

请注意以下内容:

*   pip 文件内容
*   用于提取 ubi8/python-38 基础映像的 Redhat 注册表
*   git 中没有提交数据库凭据
*   使用 podman 日志<containername>检查日志以防容器崩溃</containername>