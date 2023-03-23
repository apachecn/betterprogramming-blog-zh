# Heroku 替代品？以下是如何将 Web 应用程序部署到 AWS EC2 的方法

> 原文：<https://betterprogramming.pub/migrate-from-heroku-to-aws-ec2-756328d8e58a>

## 通过为您的 Python 和 PHP web 应用程序设置连续部署工作流，从 Heroku 迁移您的应用程序

![](img/e73bcf939961d578433af7636c8c8c96.png)

鲁拜图·阿扎德在 [Unsplash](https://unsplash.com/s/photos/3d-logo?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

Heroku 反对在 2022 年 11 月 28 日之前部署网络应用的免费层。如果你像我一样有许多低流量的投资组合项目，在一个 VPS 上托管它们要比为一个 PaaS 解决方案(如 AWS Elastic Beanstalk 或 DigitalOcean 的应用平台)按应用付费更便宜。

如果您是第一次部署 Flask 或 Django web 应用程序，以前没有使用过 Heroku，这篇文章也将帮助您部署应用程序。

在遵循本教程之后，当你将提交推送到 GitHub 时，你的 VPS 将创建一个新的 Docker 映像，在其中安装你的应用程序的依赖项，然后自己运行那个容器；你可以通过浏览器监控这一切。

![](img/82ed6b2cc9a3763e7ec99c078796c85c.png)

持续部署机制

## 目录

1.  **启动并配置一个 AWS EC2 实例** 1.1。安装 EBS 卷
    1.2。重启后自动挂载存储卷
    1.3。在 AWS 仪表板上设置入站规则以打开端口
2.  **安装并配置 Docker** 2.1。安装
    2.2。将您的用户添加到 Docker 组
    2.3。让 Docker 使用附带的卷
3.  **安装并配置 ca cover** 3.1。配置 DNS 记录
    3.2。安装顶盖
    3.3。配置 ca cover
    3.4。安装您的第一个应用程序
4.  将你的应用从 Heroku 移动到新的服务器
    4.1。创建一个 GitHub Repo(如果不存在)
    4.2。将你的 Python 应用【Dockerize。在 ca cover
    4.4 上创建新的应用程序。设置持续部署工作流
5.  **迁移一个 PostgreSQL 数据库** 5.1。从 Heroku
    5.2 出口。迁移数据库
6.  **多余的**
    6.1。PHP 应用呢？
    6.2。我的 app 不是“无状态”怎么办？

# 1.启动并配置 AWS EC2 实例

我们将从在 AWS 仪表板上创建 EC2 实例开始。如果你需要的话，你可以跟随像[这个](https://docs.aws.amazon.com/efs/latest/ug/gs-step-one-create-ec2-resources.html)这样的向导。出于我的需要，我在 t2.micro 上选择了 Ubuntu 22.04，并添加了第二个 20 GB 容量的弹性块存储(EBS)卷。

## 1.1.连接 EBS 卷

一旦您通过 ssh 进入您的服务器，使用以下命令列出所有分区。

```
lsblk -o NAME,FSTYPE,LABEL,SIZE,MOUNTPOINT
```

![](img/9c02e80f5c07b10a2b0c7cd06f95b8fd.png)

安装 EBS 卷之前的分区

基于上面的输出，我们想要挂载名为`xvdb`的 20GB 卷；但是我们在`xvdb`下没有看到 FSTYPE，就像我们在`xvda`下看到的一样，这意味着这是一个空卷。因此，我们将首先在其上创建一个文件系统。

```
sudo mkfs -t xfs /dev/xvdb
```

然后，让我们创建一个装载点:

```
sudo mkdir /mnt/ebs1
```

现在我们可以将 EBS 卷挂载到这个目录，如下所示。请注意，我们已经在上面的卷名前添加了`/dev/`:

```
sudo mount -t auto -v /dev/xvdb /mnt/ebs1
```

现在，如果我们再次运行以下命令:

```
lsblk -o NAME,FSTYPE,LABEL,SIZE,MOUNTPOINT
```

我们看到 EBS 卷被装载到`/mnt/ebs1`。

![](img/11ff6aa40230d9e7f408752558ab3248.png)

挂载 EBS 卷后的分区

## 1.2.重新启动后自动装载存储卷

重新启动后，EBS 卷将不再保持装载状态，除非我们将其设置为 automount。我们可以通过编辑`/etc/fstab`来实现。

```
sudo nano -w /etc/fstab
```

在使用前面看到的`lsblk`命令替换挂载点和文件系统类型之后，添加下面一行。

```
/dev/xvdb /mnt/ebs1 xfs defaults 0 2
```

**重要提示:**如果您在`fstab`中有无效配置，您的服务器将无法启动！在使用下面的命令重新启动之前，请验证这些设置。

```
sudo findmnt --verify
```

如果您没有看到成功消息，请检查您的`/etc/fstab`文件是否有错误。否则，您可以重新启动:

```
sudo reboot
```

…并查看您的卷已装入:

```
lsblk -o NAME,FSTYPE,LABEL,SIZE,MOUNTPOINT
```

如果您遇到有关您的卷的任何错误，请参见[EBS 卷的 AWS 指南](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-using-volumes.html)。

## 1.3.在 AWS 仪表板上设置入站规则以打开端口

现在，我们需要通过创建入站规则来打开一些端口。在 AWS 仪表板上选择 EC2 实例 id，选择 security 选项卡，然后单击 Security 组的 ID。然后单击“编辑入站规则”按钮，至少为端口 22、80、443、3000 和 5432 添加 TCP 规则。

![](img/08eaca6adcfeab59060150ae6b8cfc08.png)

EC2 实例的安全组的入站规则

在`CapRover`设置之后，您可以删除 TCP/3000 规则。如果您计划使用需要其他端口的应用程序，您也可以在此处添加它们。

# 2.安装和配置 Docker

我们将使用 CapRover，它是一个平台即服务(Paas)，来处理自动部署、Nginx 配置等等。CapRover 将使用 Docker 容器来部署你的应用程序，所以我们需要先安装 Docker。

## 2.1.装置

首先，运行以下命令来更新 apt 软件包索引并安装软件包，以允许 apt 通过 HTTPS 使用存储库:

```
sudo apt-get update
sudo apt-get install ca-certificates curl gnupg lsb-release
```

然后，添加 Docker 的官方 GPG 密钥并设置存储库:

```
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

现在我们可以安装 Docker 引擎了。

```
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin
```

如果运行以下命令时一切顺利:

```
sudo docker run hello-world
```

…您应该会看到下面的消息:

![](img/d0b07394ab7797ae52d0c3a3f9852aa9.png)

名为 hello-world 的 docker 映像的成功输出

如果您遇到任何错误，请参见[本指南](https://docs.docker.com/engine/install/ubuntu/)。

## 2.2.将您的用户添加到 Docker 组

如果您想避免在每个 docker 命令前键入`sudo`,运行下面的命令将您的系统用户添加到 docker 组并激活对组的更改。如果需要，用你的用户名替换 ubuntu。

```
sudo usermod -aG docker ubuntu
newgrp docker
```

## 2.3.让 Docker 使用附带的卷

如果您希望 docker 映像保存在挂载的卷中，请运行以下命令:

```
sudo mkdir -p /etc/systemd/system/docker.service.d
sudo nano /etc/systemd/system/docker.service.d/docker-storage.conf
```

然后在用您之前创建的路径更新`/mnt/ebs1`之后放置以下内容。

```
# For docker after 17.06-ce:
[Service]
ExecStart=
ExecStart=/usr/bin/dockerd -H fd:// --data-root="/mnt/ebs1"
```

重启 Docker:

```
sudo systemctl daemon-reload
sudo systemctl restart docker
```

旧的 Docker 目录将不再使用，因此您可以通过以下方式删除它:

```
sudo rm -rf /var/lib/docker
```

如果您有任何问题，请参见此处的 [Docker 安装指南](https://docs.docker.com/engine/install/ubuntu/)。

# 3.安装和配置 CapRover

首先，您需要选择主域，该主域将在其子域中部署 web 应用程序。主域本身可以是子域。对于这个例子，我将使用`test.cansin.net`作为我的主域，应用程序将安装在子域，如`flaskapp.test.cansin.net`。

## 3.1.配置 DNS 记录

为了实现这一点，我们需要设置两个 DNS 记录:一个用于子域，另一个通配符用于所有二级子域。在谷歌域名，这些记录如下，它应该类似于你的注册。

![](img/3d3d8ebe132c607bd5159e0c8a72632b.png)

使用测试子域及其二级子域的 DNS 记录示例

将 34.238.245.243 替换为 EC2 实例的公共 IP 地址，将“test”替换为您想要使用的子域。

如果您想使用根域名(如 example.com)并将应用程序部署为一级子域(如 flaskapp.example.com)，则删除第一个 A 记录中的“test”并将其留空(或替换为 example.com，具体取决于注册商)，然后删除。test”从第二条记录中删除，只留下星号(*)。

## 3.2.安装盖罩

现在我们准备安装我们的 PaaS，CapRover。它就像运行任何其他 Docker 容器一样:

```
docker run -p 80:80 -p 443:443 -p 3000:3000 -v /var/run/docker.sock:/var/run/docker.sock -v /captain:/captain caprover/caprover
```

如果安装成功，您将看到下面的消息。

![](img/8726896729935cdd981ba6847eabfea7.png)

如果您使用带有防火墙的现有 VPS，您将在此步骤中收到一个错误。如果是这样，请配置您的防火墙打开我们在第 1.3 节 AWS 仪表板上打开的端口。更多信息参见[ca cover 安装文档](https://caprover.com/docs/get-started.html)。

## 3.3.配置 CapRover

现在，我们可以通过导航到 EC2 实例的 IP 地址的端口 3000 来登录 CapRover 仪表板。使用默认密码 captain42 登录。

![](img/75fdff6af9d2583201f01fa1117cf1d2.png)

CapRover 登录屏幕

登录后，您将看到以下配置您的主域的表单。输入你的域名，点击“更新域名”,这将引导你再次登录。当你这样做时，点击“启用 HTTPS”，然后“强制 HTTPS”让 certbot 给你一个 SSL 证书，并根据需要更新它。

![](img/7c76b6d668d09f10e0015e6194edd797.png)

然后，从设置菜单中更改默认密码。

## 3.4.安装您的第一个应用程序

我们将从一键式应用程序选项开始启动 PostgreSQL 服务器。当您在应用程序页面上选择此选项时，您将看到大量可供选择的应用程序，只需一次点击即可部署。

![](img/18c09fffd59fbf72f96dc10158193719.png)

一键式应用程序页面

简单地说，搜索 PostgreSQL，选择它，给它一个应用程序名称，然后单击部署。

![](img/f7f64f54a8c3accd73dfe3e96a6b3452.png)

这将在安装了 PostgreSQL 的服务器上创建一个 docker 映像。

要导入我们正在迁移的应用程序的现有数据库，我们需要从本地计算机连接到此数据库。因此，我们需要将这个容器的端口 5432 映射到我们主机上的同一个端口。为此，转到应用程序页面，选择我们创建的 PostgreSQL 应用程序，找到添加端口映射按钮，并在两个框中添加一个 5432 规则，如下所示。

![](img/9ccf99c3584178f477447c9b8f04f7a9.png)

最后，点击保存和更新。

在第 5 节中，我们将把一个数据库移到这个容器中。

# 4.将你的应用移动到新服务器

在本节中，我们将部署[这个测试瓶应用程序](https://github.com/cansinacarer/flask-migration-demo)，并建立一个持续的部署工作流，以便在我们向 GitHub 推送每个提交时重新部署它。

如果您是第一次部署应用程序，您可以遵循这些相同的步骤。

## 4.1.创建一个 GitHub Repo(如果不存在)

这部分将取决于您现有的工作流程。如果您已经将 GitHub 存储库连接到 Heroku 帐户进行自动部署，那么您可以跳过这一节。

如果您的项目在 GitHub 上还没有资源库，请创建一个。如果你之前使用的是 Heroku git，你可以从那里拉出来，推到新的 GitHub 库。

## 4.2.将您的 Python 应用程序 Dockerize(参见 PHP 的结尾)

我更喜欢使用 docker 文件，而不是 CapRover 本机 Captain 定义文件，以防将来我想使用不同的容器服务。在存储库的根目录下添加一个名为 Dockerfile 的文件，其内容如下。

```
FROM ubuntu:22.04
RUN apt-get -y update
RUN apt-get install --no-install-recommends -y python3 python3-dev python3-venv python3-pip python3-wheel build-essential libmysqlclient-dev && \
 apt-get clean && rm -rf /var/lib/apt/lists/*
ADD . /my-flask-app
WORKDIR /my-flask-app
RUN pip install -r requirements.txt
EXPOSE 5000

# Ensure that the python outputs are streamed to the terminal
ENV PYTHONUNBUFFERED=1

# Run the app with gunicorn on port 5000 with 4 workers, using gevent worker
CMD ["gunicorn","-b", "0.0.0.0:5000", "-w", "4", "-k", "gevent", "--worker-tmp-dir", "/dev/shm", "wsgi:app"]
```

每次 CapRover 在容器中部署你的 app 的新实例，Docker 都会使用这些指令用 Ubuntu 22.04 创建一个新的镜像，在 requirements.txt 中安装 python 和依赖项，然后用 gunicorn 从`wsgi.py`文件运行 app 函数。

在你的`requirements.txt`中，你将需要一些版本的`gevent`和`gunicorn`。在这里看我的测试 app 的 [requirements.txt。](https://github.com/cansinacarer/flask-migration-demo/blob/main/requirements.txt)

对于 PHP 应用程序，请参见本文最后一节的 docker 文件示例。

## 4.3.在 CapRover 上创建一个新的应用程序

在 CapRover 的应用程序页面，为你的应用程序选择一个名字，然后点击创建新应用程序。此名称将成为此应用的默认子域([http://flaskapp.test.cansin.net](http://flaskapp.test.cansin.net/))，但你也可以为此应用配置其他域。

![](img/33019e369f63408de344f456eb6bb13d.png)

对于这个例子，我们希望这个应用程序在 flaskapp.cansin.net。在我们的注册商网站上为 flaskapp 添加了一个 A 记录后，我们点击我们刚刚创建的应用程序，并键入新的域名，如下所示；然后点击连接新域。

![](img/10a7ae2cb6dc40991024141dcd60fadd.png)

将新域解析到您的应用程序

然后单击“启用 HTTPS”，选择“强制 HTTPS”选项，在“容器 HTTP 端口”字段中键入 5000(除非您在 docker 文件中选择了另一个端口)，然后单击“保存并更新”。这将自动完成必要的 Nginx 和 certbot 配置，不过如果需要，您可以在这里添加您的自定义 Nginx 规则。

如果需要设置数据库或 SMTP 凭据等环境变量，可以在应用程序配置页面上输入这些变量。

## 4.4.设置持续部署工作流

现在，您可以在 Deployment 选项卡上部署您的应用程序了。虽然您可以在这个选项卡上简单地上传文件，但本文将描述一个工作流，它将使您推送到 GitHub 的提交触发 CapRover 自动从 GitHub 部署。

## **4.4.1。授权 CapRover 访问您的存储库**

如果您使用的是公共存储库，可以跳过这一步。如果您的存储库是私有的，CapRover 将需要一个 SSH 密钥向 GitHub 认证并查看您的存储库。虽然从技术上讲，您可以在这里粘贴 GitHub 帐户的 SSH 密钥，但是更安全的方法是使用 [deploy keys](https://docs.github.com/en/developers/overview/managing-deploy-keys#deploy-keys) ，它只提供对特定存储库的访问。

**注意:**作为使用部署密钥的替代方法，您可以创建一个新的 GitHub 用户，将其作为协作者添加到您将部署到 CapRover 的存储库中，为该用户创建 SSH 密钥，并将这些密钥添加到该用户的 GitHub 帐户中。然后，您可以将该用户的 SSH 密钥用于所有存储库。

**创建部署密钥**

在您的服务器上运行以下命令来创建 ssh 密钥。在下一个提示中为该密钥选择一个自定义名称，然后再次按 Enter 键跳过设置密码短语。

```
ssh-keygen -t ed25519 -C "Caprover Flask Deployment Key"
```

![](img/a310ac87b44f8d849519b152db4acd5e.png)

创建 SSH 密钥

在使用以下命令启动 ssh-agent 之后，将这个密钥添加到它。用您在上一步中选择的键名替换`flaskApp`。

```
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/flaskApp
```

![](img/26819941a1cf0abfef52db4fb138881b.png)

向 ssh 代理添加 SSH 密钥

现在，您的公钥和私钥创建在`~/.ssh/flaskApp.pub`和`~/.ssh/flaskApp`文件中，除非您更改了上面的示例代码。如果您选择了不同的路径或文件名，请相应地更改下面的命令。

**给 GitHub 和 CapRover 添加部署密钥**

运行以下命令并复制您的公钥:

```
cat ~/.ssh/flaskApp.pub
```

在 GitHub 上打开你的存储库页面，进入设置→部署密钥，点击添加部署密钥。

![](img/f42fbd937e9098238db3edaf9cef6f94.png)

如何向 GitHub 存储库添加部署密钥

然后在这里添加您的公钥的内容，并单击 Add key。

![](img/967c7e213eed095557dda3e56a6f7b28.png)

将部署密钥保存到存储库中

接下来，运行以下命令并复制您的私钥:

```
cat ~/.ssh/flaskApp
```

然后在 CapRover 上，点击你的应用程序，进入部署选项卡。在“方法 3”部分，添加您的存储库 URL、分支名称和您复制的私钥(即，默认情况下，您的 id_rsa 文件的内容)。

![](img/c07b9a67324c1db3adec2202a85ce6ae.png)

在 CapRover 的 deployment 选项卡上添加您的部署密钥

点击保存和更新，然后强制构建。

**设置 GitHub Webhook**

在 CapRover 上保存 GitHub 凭证后，API 端点的 URL 将出现在存储库 URL 的上方。复制此 URL。

![](img/0372b3151d28d645089332e57cb61ec2.png)

CapRover 接口上的 API 端点 URL

然后在 GitHub 上打开你的存储库页面，进入设置→ Webhooks，将这个 URL 粘贴到 Payload URL 字段，点击添加 webhook。

![](img/bd7a3ea8f30f36718c2d3f7758b6baf1.png)

向 GitHub 存储库添加 webhook

现在向您的存储库提交一个 commit，并在 CapRover 上的 Deployment 选项卡上查看您的应用程序的构建日志。您应该会看到下面的成功消息。

![](img/fceac33cca768ebfc7adac42377d1187.png)

显示“构建已成功完成！”在 CapRover 的构建日志部分。

仅此而已。现在每次你推 GitHub，webhook 都会触发 CapRover 部署你的应用的新版本。如果有任何错误，比如缺少依赖项，您将能够在构建日志部分看到它们。

# 5.迁移 PostgreSQL 数据库

本例中的测试应用程序使用了 Heroku 的 PostgreSQL 数据库。我们将把它迁移到我们在第 3 节中创建的 PostgreSQL 服务器上。

## 5.1.从 Heroku 出口

在 Heroku 上选择您的应用程序后，您的数据库的连接字符串可以在`Settings → Config Vars → DATABASE_URL`中找到。如果您从另一个平台迁移，请在应用程序的环境变量或设置中找到数据库连接字符串。

```
postgres://qzhzootzdkvfdi:19813b61af59c9711899304ccfe2db0cb0c402f317e90754788eb0770ff6aeb9@ec2-54-163-34-107.compute-1.amazonaws.com:5432/db4ejdhq0vnp5r
```

我们可以使用找到的凭证使用 pgAdmin 连接到该数据库。右键单击服务器组并选择注册→服务器，如下所示:

![](img/879d04184905113e13427be77fb4084e.png)

如何在 pgAdmin 4 上添加新的 PostgreSQL 服务器连接

然后填写 Heroku 提供的凭证，如下所示。

![](img/a9527d95f093e68c041a7d8389b116f4.png)

连接到 pgAdmin 4 上的 Heroku PostgreSQL 服务器

该服务器上将有许多数据库，我们将在我们的连接字符串中找到这个数据库:db4ejdhq0vnp5r。然后，右键单击它，选择备份，并保存备份文件。

![](img/1cc2ed85aa68983bfbe2fc1d82325cb9.png)

## 5.2.迁移数据库

在您创建的 PostgreSQL 应用程序的应用程序配置菜单中，在 CapRover 上找到您的新 PostgreSQL 数据库凭据，如下所示。

![](img/74b7b3c943e7cfbbf1e05b6bc8639119.png)

您可以使用这些凭证和 EC2 实例的 IP 地址，使用 pgAdmin 连接到这个数据库，就像我们对 Heroku 上的数据库所做的那样。不要忘记使用 EC2 服务器的 IP 或主机名来更改主机名。

![](img/671d6c334193006f59037fea2972c6bb.png)

使用 pgAdmin 4 连接到新的 PostgreSQL 服务器

当您连接到新服务器时，它将出现在左栏中。展开它，右键单击登录/组角色。然后选择创建→登录/组角色，如下所示。

![](img/ee4873d42c427a37b857508892df486e.png)

从 pgAdmin 4 向 PostgreSQL 数据库添加新用户

在常规选项卡中设置用户名，在定义选项卡中设置其密码，启用“可以登录吗？”选项，然后单击保存。

现在，我们可以为我们的应用程序创建一个数据库，方法是右键单击 Databases 并选择 Create →Database，如下所示。

![](img/ac73281af0991438de7972882e520340.png)

从 pgAdmin 4 向 PostgreSQL 服务器添加新数据库

在 General 选项卡中，设置数据库名称，并从下拉列表中选择新创建的用户作为所有者，如下所示。

![](img/762c6a9029349b4dc983b12366bcb21c.png)

在 pgAdmin 4 上选择 PostgreSQL 数据库所有者

如果您有一个要继续使用的数据库，请右键单击您创建的数据库并选择 restore。

![](img/78555b91b9373247508abb467f059d86.png)

然后选择您之前使用备份选项从 Heroku PostgreSQL 数据库导出的文件，然后单击 Restore 按钮。然后右键单击数据库，选择 Properties，然后选择 Default Privileges 选项卡。在这里，请确保您新创建的用户被分配了必要的权限。

现在，您的 web 应用程序应该能够连接到这个新数据库。不要忘记更改您的应用程序设置，以将其连接到新的数据库。

这个测试应用程序使用硬编码的凭证(不再使用)，但是您应该在 CapRover 上的 Settings → Config Var 中将它们设置为环境变量，并让您的应用程序从环境变量中读取凭证。

# 6.临时演员

## 6.1.PHP 应用呢？

如果您的应用程序是无状态的，除了使用不同的 docker 文件之外，您可以按照相同的步骤来部署您的应用程序。下面的例子将把你的存储库的内容放入容器的/var/www 目录中，假设你希望这个应用程序在那个域的根目录中。

```
FROM php:7.3-apache-stretch

COPY . /var/www/html
WORKDIR /var/www/html
EXPOSE 80
```

## 6.2.我的 app 不是“无状态”怎么办？

持续部署工作流只适合只做计算的无状态应用。如果存储库中的文件在应用程序运行时发生变化(例如，如果应用程序将上传的文件存储在子文件夹中)，则下次从存储库中部署应用程序时，这些文件将被覆盖。为了防止这种情况，您可以部署一次您的应用程序，并在 CapRover 上为您的应用程序启用“具有持久数据”选项。点击阅读更多关于 CapRover [上持久应用的信息。](https://caprover.com/docs/persistent-apps.html)