# 使用 Nginx 作为反向代理在 Openshift 上运行 Flask API 应用程序

> 原文：<https://betterprogramming.pub/running-a-flask-api-application-on-openshift-with-nginx-as-reverse-proxy-6f3797f7b4db>

## 使用波德曼

![](img/eae8ac689456b16bc7c663a63155bbd2.png)

Photo by [愚木混株 cdd20](https://unsplash.com/@cdd20?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

在我上一篇关于在 Podman 上用 Nginx 代理运行 Flask 应用程序的文章中，我解释了如何在 Podman 上的 Nginx 反向代理服务器后面配置 gunicorn 提供的 Flask 应用程序。我们希望在 Red Hat Openshift containers 平台上实现同样的结果。要实现这一点，只需做很少的改动。

Openshift 上的容器通过服务进行通信。Nginx 反向代理必须通过其服务名和端口向 Flask 应用程序发送流量。

有几种方法可以启动 Openshift 的应用程序。对于本文，我选择使用一个预构建的容器映像，我将在本地构建该映像，将其推送到 quay.io 存储库，并指示我的新 Openshift 应用程序使用该映像启动一个容器。

首先，这里是应用程序的文件结构:

首先，让我们看看 Flask 应用程序 Dockerfile。我已经添加了一个在`port 8000`上公开应用程序的指令。

对于 Nginx Dockerfile，我们使用 Redhat ubi9/nginx-120 基本映像，它可以轻松地重新配置为反向代理。在[基础映像环境](https://catalog.redhat.com/software/containers/rhel9/nginx-120/61a609e4bfd4a5234d596286?container-tabs=dockerfile)中有一些参数，我们将使用它们来添加我们的定制 Nginx 配置。基本映像环境包含以下条目:

最重要的 Nginx 配置是服务器块，它将定义流量转发到哪里。

因此，我们的 Nginx 反向代理应用程序映像将具有以下 Dockerfile 指令:

构建两个映像并将其推送到 quay.io。

使用`oc cli`登录 Openshift 集群。

```
$ oc login -u <username> <openshift_api_url>
  Password: 
```

创建一个用于 Openshift 应用程序的项目。

```
$ oc new-project govtstructure
```

因为我们从 quay.io 提取图像，所以我们必须对 Openshift 进行认证，以便能够从图像注册中心提取图像。我们将创建一个包含 quay.io API 访问令牌的密码，并链接默认服务帐户，以便在提取图像时使用该密码进行身份验证。

```
$ oc create secret generic quayio --from-file \   .dockerconfigjon=${XDG_RUNTIME_DIR}/containers/auth.json \
--type kubernetes.io/dockerconfigjson#link secret to default service account for pull
$ oc secrets link default quayio --for pull
```

我们已经准备好启动应用程序。我们从 Flask 应用程序开始。

```
$ oc new-app --name govtapplication \
  --image quay.io/wainaina3/govtapplication#check all the services which have been launched
$ oc get all -l app=govtapplication#Verify the application is running
$ oc get pods
NAME                               READY   STATUS    RESTARTS   AGE
govtapplication-858ffb9777-nfgbg   1/1     Running   0        4m53s#verify service is up and listening on port 8000
$ oc get svc
NAME            TYPE      CLUSTER-IP     PORT(S)             AGE
govtapplication ClusterIP 172.30.220.218 8000/TCP,8080/TCP   5m57s
```

政府应用程序已经启动并运行。接下来，我们可以创建 Nginx 反向代理应用程序来服务我们的流量。

```
#create nginx application with image from quay.io
$ oc new-app --name govtapplication \
  --image quay.io/wainaina3/govtnginxproxy
```

验证 Nginx 容器正在运行，并按照预期监听`port 8181`。

```
#Verify application is running as expected
$ oc get pods
```

所有服务都正常，pod 运行正常。我们将通过公开 Nginx 服务来创建一个路由，并显式地选择`port 8181`，这样它就可以为我们的 Flask 应用流量提供服务。

```
$ oc expose svc govtnginxproxy --port 8181
```

瞧啊。我们的应用程序现在可以从 Nginx 路由访问，只需添加以下应用程序路由:

```
#Get the route
$ oc get routes#Access the application from the route. use the /sayhello or /getAllCounties(will require database connection) to test$ curl http://<route_url>/sayhello
{"greetings":"Hello there"}
```

您可以在 [my GitHub](https://github.com/Wainaina3/publications/tree/govt) 资源库中找到应用程序代码。