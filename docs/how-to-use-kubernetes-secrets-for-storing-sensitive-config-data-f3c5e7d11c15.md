# 如何使用 Kubernetes 的秘密来存储敏感的配置数据

> 原文：<https://betterprogramming.pub/how-to-use-kubernetes-secrets-for-storing-sensitive-config-data-f3c5e7d11c15>

## 因为你的钥匙和秘密应该只是秘密

![](img/0ef9dfd012b7aa4fbb333be6363f18e2.png)

JOSHUA COLEMAN 在 [Unsplash](https://unsplash.com/s/photos/storage?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

你好，欢迎👋👋简而言之，我们继续我们的 Kubernetes 之旅。在之前的一篇博客中，我们看到了如何使用`ConfigMap`对象[配置 Kubernetes 应用](https://itnext.io/learn-how-to-configure-your-kubernetes-apps-using-the-configmap-object-d8f30f99abeb)。在本帖中，我们将探讨 Kubernetes `Secrets`以及如何使用它们来存储需要安全处理的敏感配置数据(例如，数据库凭证、API 密钥等)。).

![](img/c2b7ce851ff940c12505e1aa99bf6b43.png)

像往常一样，这将是一个示例驱动的过程，您将会学到:

*   如何创建`Secrets` (CLI，`yaml`等。)和
*   在应用程序中使用它们的各种方式(环境变量、体积等。)

代码(和`YAML`)可以在 GitHub 上[获得。](https://github.com/abhirockzz/kubernetes-in-a-nutshell/blob/master/secrets)

这篇文章分为两个逻辑部分:创建`Secrets`的方法和在应用程序中使用`Secrets`的技术。

# 先决条件

要浏览本文中的例子，您需要的只是一个 Minikube 集群和一个访问该集群的 kubectl CLI 工具。

将 [Minikube](https://kubernetes.io/docs/tasks/tools/install-minikube/) 作为单节点 Kubernetes 集群安装在您计算机上的虚拟机中。在 Mac 上，您可以简单地:

```
curl -Lo minikube https://storage.googleapis.com/minikube/releases/latest/minikube-darwin-amd64 \
  && chmod +x minikubesudo mv minikube /usr/local/bin
```

安装 [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) 与 Minikube 集群交互。在 Mac 上，您可以:

```
curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s [https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/darwin/amd64/kubectl](https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/darwin/amd64/kubectl)chmod +x ./kubectlsudo mv ./kubectl /usr/local/bin/kubectl
```

# 创造秘密

让我们看看创建`Secret`的技巧。

## 使用`data`部分

可以在定义的`data`部分创建一个`Secret`以及作为键值对存储的配置数据。

```
apiVersion: v1
kind: Secret
metadata:
  name: service-apikey
data:
  apikey: Zm9vYmFy
```

`Secret`包含代表敏感信息的键值数据，其中`apikey`是键，值是一个`base64`编码的字符串。

要在 Kubernetes 创建这个`Secret`:

```
kubectl apply -f [https://raw.githubusercontent.com/abhirockzz/kubernetes-in-a-nutshell/master/secrets/secret-data.yaml](https://raw.githubusercontent.com/abhirockzz/kubernetes-in-a-nutshell/master/secrets/secret-data.yaml)
```

为了简单起见，YAML 文件直接从 [GitHub repo](https://github.com/abhirockzz/kubernetes-in-a-nutshell) 中引用，但是您也可以将该文件下载到您的本地机器上，并以同样的方式使用它。

确认`Secret`已经创建:

```
kubectl get secret/service-apikey -o yaml
```

您将得到类似于以下内容的 YAML 响应:

```
apiVersion: v1
data:
  apikey: Zm9vYmFy
kind: Secret
metadata:
  annotations:
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"v1","data":{"apikey":"Zm9vYmFy"},"kind":"Secret","metadata":{"annotations":{},"name":"service-apikey","namespace":"default"}}
  creationTimestamp: "2019-12-17T11:11:27Z"
  name: service-apikey
  namespace: default
  resourceVersion: "113009"
  selfLink: /api/v1/namespaces/default/secrets/service-apikey
  uid: 671b547c-3316-4916-b6dc-be2b551b974e
type: Opaque
```

使用 kubectl get 获取`Secret`细节不会泄露其内容。

注意`apikey: Zm9vYmFy`是我们在 YAML 清单中提供的。您可以通过解码来检查纯文本格式:

```
echo 'Zm9vYmFy' | base64 --decode//foobar
```

## **使用字符串数据段**

上例中使用的`data`属性用于保存`base64`编码信息。如果您想安全地存储纯文本数据，您可以使用`stringData`部分。这里有一个例子:

```
apiVersion: v1
kind: Secret
metadata:
  name: plaintext-secret
stringData:
  foo: bar
  mac: cheese
```

`foo`和`mac`的值作为纯文本传递。创建此`Secret`并确认:

```
kubectl apply -f https://raw.githubusercontent.com/abhirockzz/kubernetes-in-a-nutshell/master/secrets/secret-plaintext.yamlkubectl get secret/plaintext-secret -o yaml
```

这是 YAML 回应的`data`部分。实际数据以`base64`编码格式存储。

```
data:
  foo: YmFy
```

如果您解码数据，您可以确认它与我们提供的原始纯文本输入(`bar`)相匹配

```
echo 'YmFy' | base64 --decode//bar
```

注意`data`部分不接受纯文本属性。试图这样做将导致类似于this: `*illegal base64 data at input byte 8*`的错误

## **文件内容**

您也可以将整个文件的内容作为输入提供给`stringData`部分。这可能是这样的:

```
apiVersion: v1
kind: Secret
metadata:
  name: secret-in-a-file
stringData:
  app-config.yaml: |-
    hello: world
    john: doe
```

## 创建此`Secret`

```
kubectl apply -f [https://raw.githubusercontent.com/abhirockzz/kubernetes-in-a-nutshell/master/secrets/secret-file.yaml](https://raw.githubusercontent.com/abhirockzz/kubernetes-in-a-nutshell/master/secrets/secret-file.yaml)
```

这个结果`Secret`将包含一个名为`app-config.yaml`的密钥，其内容(值)将是所提供数据的`base64`编码。

像往常一样，您在 Kubernetes 中确认这一点，并解码内容。

```
kubectl get secret/secret-in-a-file -o yaml
echo '<"data" content in yaml response> | base64 --decode
```

注意:当您使用这种技术时，您的应用程序负责解析出代表`Secret`配置的数据。在这种情况下，它碰巧是换行符分隔的键-值对，但也可能是其他任何东西。

# 使用`kubectl`

你可以使用`kubectl create secret`命令来创建`Secret`对象

## **使用** `**--from-literal**`

您可以使用 CLI 使用纯文本数据创建`Secret`(这将以`base64`编码格式存储在 Kubernetes 中):

```
kubectl create secret generic redis-credentials --from-literal=user=poweruser --from-literal=password='f0ob@r'
```

## **使用** `**--from-file**`

```
kubectl create secret generic topsecret --from-file=api_keys.txt
```

这将创建一个秘密(`topsecret`)

*   与文件同名的密钥，即本例中的`api_keys.txt`
*   和值作为文件的内容

## **从一个目录中的文件**

您可以简单地指向一个目录，其中的所有文件都将用于创建`Secret`。

```
kubectl create secret generic topsecrets --from-file=/home/credentials/
```

你最终会得到

*   多个密钥将与单个文件名相同
*   该值将是相应文件的内容

# 使用秘密

为了让秘密有用，我们需要确保它们对我们的应用程序(例如，pod)可用。让我们探索一下我们能做到这一点的方法

## 环境变量

您可以将`Secret`数据作为环境变量在`Pod`中使用(就像`ConfigMap`)。这里有一个例子:

```
apiVersion: v1
kind: Pod
metadata:
  name: pod1
spec:
  containers:
    - name: nginx
      image: nginx
      env:
        - name: API_KEY
          valueFrom:
            secretKeyRef:
              name: service-apikey
              key: apikey
```

我们使用`Secret` `service-apikey`中的键`apikey`，并确保它的值作为`Pod`中的环境变量`API_KEY`可用。

创建`Pod`(假设您有根据之前的示例创建的`Secret`)并确认。

```
kubectl apply -f [https://raw.githubusercontent.com/abhirockzz/kubernetes-in-a-nutshell/master/secrets/pod-secret-env.yaml](https://raw.githubusercontent.com/abhirockzz/kubernetes-in-a-nutshell/master/secrets/pod-secret-env.yaml)kubectl get pods -w
```

等待`Pod`转换到`Running`状态。然后，确认环境变量已经被注入到`Pod`中。

```
kubectl exec pod1 -- env | grep API_KEY
```

你应该会得到这样的回应——`API_KEY=foobar`。

您可以使用`envFrom`来方便地使用所有条目作为`Pod`中的环境变量，而不是引用`Secret`中的单个条目。你可以这样使用它:

```
apiVersion: v1
kind: Pod
metadata:
  name: pod2
spec:
  containers:
    - name: nginx
      image: nginx
      envFrom:
        - secretRef:
            name: plaintext-secret
```

我们用`envFrom.secretRef`来指代`plaintext-secret` `Secret`。要创建这个`Pod`:

```
kubectl apply -f [https://raw.githubusercontent.com/abhirockzz/kubernetes-in-a-nutshell/master/secrets/pod-secret-envFrom.yaml](https://raw.githubusercontent.com/abhirockzz/kubernetes-in-a-nutshell/master/secrets/pod-secret-envFrom.yaml)kubectl get pods -w
```

等待`Pod`转换到`Running`状态，然后确认环境变量的存在。

```
kubectl exec pod2 -- env | grep foo
//foo=barkubectl exec pod2 -- env | grep mac
//mac=cheese
```

这证实了`foo`和`mac`都作为环境变量与它们的解码值(即`bar`和`cheese`)一起被添加到`the` Pod 中

## **卷**

您可以将`Secrets`作为`Volume`安装在`Pod`内。例如:

```
apiVersion: v1
kind: Pod
metadata:
  name: pod3
spec:
  containers:
    - name: nginx
      image: nginx
      volumeMounts:
        - name: apikey-config-volume
          mountPath: /secret
          readOnly: true
  volumes:
    - name: apikey-config-volume
      secret:
        secretName: service-apikey
```

`apikey-config-volume`卷是指`service-apikey` `Secret`。要创建这个`Pod`:

```
kubectl apply -f [https://raw.githubusercontent.com/abhirockzz/kubernetes-in-a-nutshell/master/secrets/pod-secret-volume.yaml](https://raw.githubusercontent.com/abhirockzz/kubernetes-in-a-nutshell/master/secrets/pod-secret-volume.yaml)kubectl get pods -w
```

等待`Pod`转换到`Running`状态。然后执行以下命令:

```
kubectl exec pod3 -- cat /secret/apikey
//foobar
```

这确认了密钥`service-apikey`中的密钥`apikey`作为一个文件(名为`apikey`)被安装在`/secret`目录中(具体见 Pod)。该文件的内容只是秘密值，即本例中的`foobar`。

## **使用** `**imagePullSecrets**`

有一种方法可以使用`Secrets`,这样您的应用程序 Pod 就可以使用它来认证并从私有 Docker 注册中心获取 Docker 图像。

`Secrets`其实有三种类型:

*   `generic`——用于存储键值对，就像我们在前面的例子中看到的那样
*   `tls` -将公钥/私钥对信息存储为`Secrets`
*   `docker-registry` -用于向 Docker 注册表认证的凭证。

这项技术的使用方法非常简单:

*   使用`docker-registry` `Secret`类型在 Kubernetes 中存储私有 Docker 注册表凭证。
*   然后使用`imagePullSecrets`(在 Pod 中)来引用包含 Docker 注册凭证的`Secret`。

一个例子总是有帮助的:

```
apiVersion: v1
kind: Pod
metadata:
  name: pod4
spec:
  containers:
    - name: privateapp
      image: abhirockzz/test-private-repo:latest
      command: ["/bin/sh"]
      args: ["-c", "while true; do date; sleep 5;done"]
  imagePullSecrets:
    - name: docker-repo-secret
```

看看`imagePullSecrets.name`怎么指代一个叫`docker-repo-secret`的`Secret`。让我们创造它。

但在此之前，请确保您有一个私人 Docker 注册表。我用的是`[DockerHub](https://hub.docker.com/)`，其他的你也可以选。

首先使用`kubectl create secret docker-registry`命令创建一个包含您的 Docker 凭证的`Secret`(名为`docker-repo-secret`)。

```
kubectl create secret docker-registry docker-repo-secret --docker-server=DOCKER_REG_SERVER --docker-username=DOCKER_REG_USERNAME --docker-password=DOCKER_REG_PASSWORD --docker-email=DOCKER_REG_EMAIL
```

对于 Docker Hub:

```
kubectl create secret docker-registry docker-repo-secret --docker-server=https://index.docker.io/v1/ --docker-username=foobarbaz --docker-password=t0ps3cr3t --docker-email=foobarbaz@gmail.comkubectl get secret/docker-repo-secret -o yaml
```

`https://index.docker.io/v1/`是 Docker Hub 注册服务器

为了进行测试，我们将使用`[busybox](https://hub.docker.com/_/busybox)`图像和`tag`图像

```
docker pull busybox
docker tag busybox [DOCKER_REG]/[DOCKER_PRIVATE_REPO]:[IMAGE_TAG]e.g. 
docker tag busybox abhirockzz/test-private-repo:latest
```

…和`push` it

```
docker push [DOCKER_REG]/[DOCKER_PRIVATE_REPO]:[IMAGE_TAG]e.g. 
docker push abhirockzz/test-private-repo:latest
```

一旦私有回购准备就绪，您就可以创建`Pod`，它将使用通过`Secret`提供给它的注册表凭证从私有回购中提取映像

```
kubectl apply -f [https://raw.githubusercontent.com/abhirockzz/kubernetes-in-a-nutshell/master/secrets/pod-secret-docker.yaml](https://raw.githubusercontent.com/abhirockzz/kubernetes-in-a-nutshell/master/secrets/pod-secret-docker.yaml)kubectl get pods -w
```

等待`Pod`移动到`Running`状态。

如果您看到一个`ErrImagePull`错误，这表明可能存在向 Docker 注册中心认证的问题。要获取详细信息，请使用:`kubectl describe pod/pod4`

确认 pod 工作正常:`kubectl logs -f pod4`

由于`busybox`图像本身并不真正做任何事情，我们执行:`while true; do date; sleep 5;done`(如`Pod`规范中所提供的)。因此，您应该会看到日志(每 5 秒打印一次)。

```
Tue Dec 17 14:17:34 UTC 2019
Tue Dec 17 14:17:39 UTC 2019
Tue Dec 17 14:17:44 UTC 2019
Tue Dec 17 14:18:49 UTC 2019
```

一切都好！这意味着 Pod 能够使用 Docker 凭证从私有 Docker repo 中下载你的图像，Docker 凭证通过`imagePullSecrets`注入到`Pod`中，而`imagePullSecrets`本身引用了`Secret`

# 很高兴知道

以下是您在使用`Secrets`时应该谨记的事项列表(并非详尽):

*   `Secret`必须在任何想要使用它的`Pod`之前被创建。
*   `Secrets`在一个`namespace`内适用，即它们只能由同一个`namespace`内的`Pods`使用
*   如果在`Secret`中引用了一个不存在的键(使用`secretKeyRef`，则`Pod`不会启动
*   1mb 是个人的大小限制`Secrets`

这就是本期《果壳中的库伯内特》系列的全部内容。敬请关注更多内容！

如果你对使用 [Azure](https://azure.microsoft.com/services/kubernetes-service/?WT.mc_id=medium-blog-abhishgu) 学习 Kubernetes 和 Containers 感兴趣，只需[创建一个免费账户](https://azure.microsoft.com/en-us/free/?WT.mc_id=medium-blog-abhishgu)就可以开始了。一个好的起点是使用文档中的[快速入门、教程和代码示例](https://docs.microsoft.com/azure/aks/?WT.mc_id=medium-blog-abhishgu)来熟悉该服务。我也强烈推荐查看 50 天的 Kubernetes 学习路径。高级用户可能希望参考 [Kubernetes 最佳实践](https://docs.microsoft.com/azure/aks/best-practices?WT.mc_id=medium-blog-abhishgu)或观看一些[视频](https://azure.microsoft.com/resources/videos/index/?services=kubernetes-service&WT.mc_id=medium-blog-abhishgu)以了解演示、主要特性和技术会议。

我真的希望你喜欢这篇文章，并从中学到了一些东西。🙌