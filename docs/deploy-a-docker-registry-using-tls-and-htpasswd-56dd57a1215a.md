# 使用自签名证书和 htpasswd 部署 Docker 注册中心

> 原文：<https://betterprogramming.pub/deploy-a-docker-registry-using-tls-and-htpasswd-56dd57a1215a>

## 使用 TLS(密钥/证书)和 htpasswd(身份验证)部署 Docker 注册中心

![](img/5913aa2a89c9e064168dfb2cbb8e4d04.png)

照片由 [Tirza van Dijk](https://unsplash.com/@tirzavandijk?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/registry?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

我们将首先创建一个目录，在其中存储我们的配置和证书。

```
# Create a directory and access it
$ mkdir registry && cd "$_"
```

在这个目录中，我们将创建两个子目录:一个用于 TLS 配置，一个用于`htpasswd`配置。

```
# Create subdirectories
$ mkdir certs
$ mkdir auth
```

我们会生成一个密钥并保护它。之后，我们将使用这个密钥来生成我们的自签名证书。

```
# Generate private key
$ cd certs/
$ openssl genrsa 1024 > domain.key
$ chmod 400 domain.key# Generate certificate 
$ openssl req -new -x509 -nodes -sha1 -days 365 -key domain.key -out domain.crt# Verify
$ ls
domain.crt domain.key
```

我们已经准备好了 TLS 配置。现在，我们访问我们的`auth/`目录，并开始使用`htpasswd`配置我们的凭证。

```
# Access auth/ directory
$ cd ../auth/# Use the registry container to generate a htpasswd file
$ docker run --rm --entrypoint htpasswd registry:2 -Bbn username password > htpasswd# Verify
$ cat htpasswd
username:$2y$05$mnaMdOsL7RCjyhTwYnGSp.7OUmZyd2EYLYj0WWKGKSpcVCl9
```

我们所有的配置都完成了。我们可以启动注册表容器。

```
# Go back inside your registry/ directory
$ cd ..
$ pwd
/Users/xxx/registry# Start Registry container
docker run -d \
  --restart=always \
  --name registry \
  -v `pwd`/auth:/auth \
  -v `pwd`/certs:/certs \
  -v `pwd`/certs:/certs \
  -e REGISTRY_AUTH=htpasswd \
  -e REGISTRY_AUTH_HTPASSWD_REALM="Registry Realm" \
  -e REGISTRY_AUTH_HTPASSWD_PATH=/auth/htpasswd \
  -e REGISTRY_HTTP_ADDR=0.0.0.0:443 \
  -e REGISTRY_HTTP_TLS_CERTIFICATE=/certs/domain.crt \
  -e REGISTRY_HTTP_TLS_KEY=/certs/domain.key \
  -p 443:443 \
  registry:2
```

您的注册表正在`[https://localhost/](https://localhost/)`上运行。让我们给它推一个图像。

```
# Pull busybox image
$ docker pull busybox# Tag the image
$ docker tag busybox localhost:443/busybox# Try to push the image
docker push localhost:443/busybox
The push refers to repository [localhost:443/busybox]
0314be9edf00: Preparing
no basic auth credentials# Perform a docker login
$ docker login -u username [https://localhost:443](https://localhost:443)
Password:
Login Succeeded# Push again
$ docker push localhost:443/busybox
The push refers to repository [localhost:443/busybox]
0314be9edf00: Pushed
latest: digest: sha256:186694df7e479d2b8bf075d9e1b1d7a884c6de60470006d572350573bfa6dcd2 size: 527
```

我的注册中心托管在 AWS EC2 实例上。我可以从`localhost`上的那台机器内部访问它，但我也应该能够使用我的注册中心所在的服务器的公共主机名或公共 IP 从远程机器访问它。我使用了`-p 443:443`，它将我的注册表容器的端口映射到我的主机的端口 443 上。

不要忘记打开端口 443(安全组或防火墙)。如果您尝试登录，您会看到以下错误:

```
# Perform a docker login
$ docker login -u username https://ec2–xx–xx–xx–xx.eu-west-1.compute.amazonaws.com:443
Password:
Error response from daemon: Get https://ec2–xx–xx–xx–xx.eu-west-1.compute.amazonaws.com:443/v2/: x509: certificate signed by unknown authority
```

这是因为我们使用自签名证书。[我们应该配置 Docker 守护进程来信任我们的自签名证书](https://docs.docker.com/registry/insecure/)。

在 Linux 机器上，您应该创建以下目录。该目录应该与托管注册表的服务器的主机名相匹配。

```
$ sudo mkdir -p /etc/docker/certs.d/ec2–xx–xx–xx–xx.eu-west-1.compute.amazonaws.com:443/
```

接下来，您应该将我们之前生成的`domain.crt`复制到这个目录，并将其命名为`ca.crt`。

现在尝试再次登录:

```
# Perform a docker login
$ docker login -u username https://ec2–xx–xx–xx–xx.eu-west-1.compute.amazonaws.com:443
Password:
Login Succeeded
```

# 结论

我们已经使用 TLS 和 htpasswd 部署了 Docker 注册中心。在推送图像之前，我们需要进行身份验证。我们还学习了如何从远程机器访问注册表。