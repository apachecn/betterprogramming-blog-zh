# Kubernetes 实践-使用 Thumbor 和 AWS S3 设置影像服务

> 原文：<https://betterprogramming.pub/kubernetes-practice-set-up-image-service-with-thumbor-and-aws-s3-9056247c111e>

## 在这篇文章中，我们将学习如何在 Kubernetes 环境中安装 Thumbor，并使用 AWS S3 作为 Thumbor 的图像存储。

![](img/4148f56c0aa09724d975e90f9aabad82.png)

# 拇指

Thumbor 是一种智能成像服务。它支持按需裁剪、调整大小和翻转图像。

它采用最先进的面部和特征检测算法，非常智能地检测图像中的重要点，以便更好地进行裁剪和调整大小。

使用 thumbor 非常简单。例如，我们使用 Thumbor 来访问宽度为 300、长度为 200 的图像。

```
[http://thumbor-server/K97LekICOXT9MbO3X1u8BBkrjbu5/300x200/smart/example.jpg](http://thumbor-server/K97LekICOXT9MbO3X1u8BBkrjbu5/300x200/smart/example.jpg)
```

那么如果我们需要访问宽度为 350，长度为 300 的图像，只需将值从 300x200 更改为 350x300 即可。

# 设置缩略图

现在，我将指导你如何在 Kubernetes 上设置 Thumbor。

首先，创建一个名为`thumbor-deployment.yaml`的文件。

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: thumbor-server
spec:
  replicas: 1
  selector:
    matchLabels:
      app: thumbor
  template:
    metadata:
      labels:
        app: thumbor
    spec:
      containers:
        - name: thumbor
          image: minimalcompact/thumbor:6.7.5
          command: ["thumbor"]
          args: ["-p", "80", "-c", "/conf/thumbor.conf"]
          ports:
            - containerPort: 80
          volumeMounts:
            - name: thumbor-conf
              mountPath: /conf
      volumes:
        - name: thumbor-conf
          configMap:
            name: thumbor
```

我们将使用 Configmap 来存储 thumbor 服务器的配置，并将其挂载到 Pod 中的路径`/conf`，并使用两个属性`command`和`args`来指定 thumbor 在端口 80 上运行，并使用一个文件`/conf/thumbor.conf`作为配置文件。

接下来，您创建一个名为`thumbor.cm.yaml`的文件。

```
apiVersion: v1
kind: ConfigMap
metadata:
  name: thumbor
data:
  thumbor.conf: |
    UPLOAD_ENABLED = True
```

在`thumbor.cm.yaml`中，将有一个属性`UPLOAD_ENABLED = True`用于缩略图以启用上传文件。此环节的完整配置[https://thumbor.readthedocs.io/en/latest/configuration.html](https://thumbor.readthedocs.io/en/latest/configuration.html)。

接下来，您将创建一个服务和入口，为外部客户机公开 thumbor 服务器的流量。创建一个名为`thumbor.svc.yaml`的文件。

```
apiVersion: v1
kind: Service
metadata:
  name: thumbor
spec:
  selector:
    app: thumbor
  ports:
    - port: 80---
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: thumbor
  annotations:
    kubernetes.io/ingress.class: alb
    alb.ingress.kubernetes.io/scheme: internet-facing
    alb.ingress.kubernetes.io/target-type: ip
    alb.ingress.kubernetes.io/healthcheck-path: /healthcheck
spec:
  rules:
  - host: thumbor.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: thumbor
            port:
              number: 80
```

在本文中，因为我使用 AWS Kubernetes 服务，所以我使用`aws-load-balancer-controller`进行 Ingress，你可以在这个链接[https://AWS . Amazon . com/premium support/knowledge-center/eks-ALB-Ingress-controller-fargate](https://aws.amazon.com/premiumsupport/knowledge-center/eks-alb-ingress-controller-fargate)看到如何安装。至于内部环境，你应该使用 [NGINX Ingress](https://kubernetes.github.io/ingress-nginx) 。

如果您想在本地环境中测试，我们会创建一个节点端口服务。

```
apiVersion: v1
kind: Service
metadata:
  name: thumbor
spec:
  type: NodePort
  selector:
    app: thumbor
  ports:
    - port: 80
      nodePort: 30000
```

现在，我们通过运行以下命令在 Kubernetes 上创建资源。

```
kubectl apply -f thumbor.cm.yaml
kubectl apply -f thumbor-deployment.yaml
kubectl apply -f thumbor.svc.yaml
```

让我们检查一下，我们上传一个文件到 thumbor 服务器。

```
curl -i -H "Content-Type: image/jpeg" -H "Slug: photo.jpg" -XPOST [http://thumbor.example.com/image](http://thumbor.example.com/image) --data-binary "[@example](http://twitter.com/example).jpg"
```

结果。

```
HTTP/1.1 201 Created
Content-Length: 0
Content-Type: text/html; charset=UTF-8
Location: /image/05b2eda857314e559630c6f3334d818d/photo.jpg
Server: TornadoServer/4.5.3
```

访问图像。

```
[http://thumbor.example.com/image/05b2eda857314e559630c6f3334d818d/photo.jpg](http://thumbor.example.com/image/05b2eda857314e559630c6f3334d818d/photo.jpg)
```

所以我们已经成功安装了 Thumbor。但是现在，如果您删除部署并重新创建它，并且您访问上面的映像路径，您将收到 404 Not Found 错误。

因为我们没有为 thumbor 服务器配置持久存储，所以我们可以使用 StatefulSet 并配置 PersistentVolume。但是当我们放大时，我们的图像将保存在不同的 PVs 中，这将导致我们访问图像时出错。

# 使用 S3 存储设置 Thumbor

我们将对整个 thumbor 服务器使用 AWS S3 作为存储，要使用 S3，我们遵循以下步骤。

首先，您需要创建一个 IAM 用户，该用户有权访问您将用来存储图像的 S3 存储桶。你按照这个链接[https://docs . AWS . Amazon . com/Amazon S3/latest/user guide/example-walk throughs-managing-access-example 1 . html](https://docs.aws.amazon.com/AmazonS3/latest/userguide/example-walkthroughs-managing-access-example1.html)创建一个 IAM 用户。然后为这个 IAM 用户创建一个访问密钥和秘密密钥，并使用这两个值创建一个 Kubernetes 秘密。

```
kubectl create secret generic s3-credentials --from-literal=AWS_ACCESS_KEY_ID=AKIAR4725YR2POTRRS5G --from-literal=AWS_SECRET_ACCESS_KEY=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
```

更新文件`thumbor.cm.yaml`以添加配置，以便 thumbor 可以使用 S3。

```
apiVersion: v1
kind: ConfigMap
metadata:
  name: thumbor
data:
  thumbor.conf: |
    UPLOAD_ENABLED = True
    TC_AWS_REGION = 'us-west-2'
    TC_AWS_STORAGE_BUCKET = 'thumbor-test'
    TC_AWS_STORAGE_ROOT_PATH = 'storage'
    TC_AWS_RESULT_STORAGE_BUCKET = 'thumbor-test'
    TC_AWS_RESULT_STORAGE_ROOT_PATH = 'result_storage'
    STORAGE = 'tc_aws.storages.s3_storage'
    UPLOAD_PHOTO_STORAGE = 'tc_aws.storages.s3_storage'
    RESULT_STORAGE = 'tc_aws.result_storages.s3_storage'
```

你可以在这个链接[https://github.com/thumbor-community/aws](https://github.com/thumbor-community/aws)看到完整的配置。上面我们使用配置`STORAGE = 'tc_aws.storages.s3_storage'`和`UPLOAD_PHOTO_STORAGE = 'tc_aws.storages.s3_storage'`来配置 thumbor 使用 S3 作为图像存储。

更新一个文件`thumbor-deployment.yaml`。

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: thumbor-server
spec:
  replicas: 1
  selector:
    matchLabels:
      app: thumbor
  template:
    metadata:
      labels:
        app: thumbor
    spec:
      containers:
        - name: thumbor
          image: minimalcompact/thumbor:6.7.5
          command: ["thumbor"]
          args: ["-p", "80", "-c", "/conf/thumbor.conf"]
          ports:
            - containerPort: 80
          envFrom:
            - secretRef:
                name: s3-credentials
          volumeMounts:
            - name: thumbor-conf
              mountPath: /conf
      volumes:
        - name: thumbor-conf
          configMap:
            name: thumbor
```

重新创建配置图和部署。

```
kubectl apply -f thumbor.cm.yaml
kubectl apply -f thumbor-deployment.yaml
```

现在，我们上传一个文件到拇指上。

```
curl -i -H "Content-Type: image/jpeg" -H "Slug: photo.jpg" -XPOST [http://thumbor.example.com/image](http://thumbor.example.com/image) --data-binary "[@example](http://twitter.com/example).jpg"
```

如果你去 S3，你会看到你的档案。

![](img/159e6003663648215e6244835dec74b0.png)

我们已成功配置。

# 结论

因此，我们已经学会了如何配置拇指与 S3。Thumbor 是一个很棒的图像处理服务，我们可能会在生产环境中使用它。如果你有任何问题或者需要更多的澄清，你可以在下面的评论区提问。