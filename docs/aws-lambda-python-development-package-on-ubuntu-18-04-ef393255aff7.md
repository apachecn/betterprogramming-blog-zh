# Ubuntu 18.04 上的 AWS Lambda Python 开发包

> 原文：<https://betterprogramming.pub/aws-lambda-python-development-package-on-ubuntu-18-04-ef393255aff7>

## 学习使用外部包部署 Python Lambda 函数

![](img/baef3d1646b00ee357f4ee2a104dfa82.png)

[AWS Lambda](https://aws.amazon.com/lambda/features/) 是一种运行和部署简单功能的便捷方式。然而，对于 [Python](https://www.python.org/) 来说，导入外部包可能是一件痛苦的事情。有几种方法可以解决这个问题，也许最实用的方法是使用 [Docker](https://github.com/lambci/docker-lambda) 。然而，在本教程中，我将演示从远程 Ubuntu 服务器使用 [numpy](https://www.numpy.org/) 部署一个简单的函数。

## 远程服务器

第一步是获得 VPS。对于这个例子，我选择使用 [DigitalOcean](https://www.digitalocean.com/) 。您可以使用以下链接获得一笔[50 美元的信用点数](https://try.digitalocean.com/virtual-private-servers/?_campaign=DO_Dev_Signup_G_Search_NB_VPS&_adgroup=VPS_VPS&_keyword=vps&_device=c&_copytype=&_adposition=1t3&_medium=nb_sem&_source=google&_dkitrig=&_2dkitrig=&gclid=CjwKCAjwmNzoBRBOEiwAr2V27Sm4udly1_1dfLWTqYV9GpqDRNvUz-fopVA0mL8uBl6QFbblySsNphoCLdYQAvD_BwE)。其他要考虑的服务有 [Linode](https://www.linode.com/) 、 [Vultr](https://www.vultr.com/) 、 [AWS EC2](https://aws.amazon.com/ec2/) 和 [Google 计算引擎](https://cloud.google.com/compute/)。

## 基础

启动并登录到远程服务器后，创建以下脚本:

```
$ touch install.sh
```

将以下命令复制到脚本中。

```
#!/usr/bin/env bashsudo apt update
sudo apt full-upgrade --yes
sudo apt autoremove --yessudo apt install zip --yes
sudo apt install python3-pip --yes
```

使脚本可执行。

```
$ chmod +x install.sh
```

运行脚本。

```
$ ./install.sh
```

## AWS CLI

安装[亚马逊命令行接口](https://aws.amazon.com/cli/)，方便与 AWS 服务交互。

```
$ pip3 install awscli
```

为了配置`awscli`，您需要拥有或者创建一个 [IAM 用户](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users.html)。您可以按照本指南生成一个访问密钥。

您的凭据应该类似于下图:

```
Access Key ID: AKIAIOSFODNN7EXAMPLE
Secret Access Key: wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
```

运行`aws configure`并输入您的凭证。

```
$ aws configure
*# AWS Access Key ID [None]:
# AWS Secret Access Key [None]:
# Default region name [None]:
# Default output format [None]:*
```

## 测试功能

创建目录`numpy_tester`并将`cd`放入其中。

```
$ mkdir numpy_tester$ cd numpy_tester
```

创建 lambda 函数`numpy_tester.py`并添加以下内容:

## Numpy

为了在我们的函数中访问`numpy`，我们需要直接安装包。

```
$ pip3 install numpy -t .
```

您可以通过运行以下命令在本地测试该函数:

```
$ python3 numpy_tester.py
*# Your numpy array:
# [[ 0  1  2  3  4]
#  [ 5  6  7  8  9]
#  [10 11 12 13 14]]*
```

## 部署

为了创建部署包，我们需要压缩函数中包含的所有文件。`-r9`标志将包含隐藏文件。

```
$ zip -r9 numpy_tester.zip .
```

为了部署，我们必须指定一个`role-arn`。最简单的方法是通过 AWS 控制台。

*   导航到**我的安全凭证**，然后选择**角色**
*   选择**创建角色**并选择**λ**
*   选择策略名称:**管理员访问权限**(仅针对本例)
*   命名角色和**创建角色**
*   选择新角色并复制**角色 ARN**

接下来，创建一个 S3 桶并上传 zip 文件。

```
$ aws s3 mb s3://numpy-tester$ aws s3 cp numpy_tester.zip s3://numpy-tester/
```

最后，检查 Python 3 的版本。列出的值是我们将用于下面的`runtime`参数。

```
$ python3 --version
*# Python 3.6.8*
```

使用`awscli`命令`create-function`创建 Lambda。您需要为`region`和`role-arn`参数指定一个值。其他一切都可以保持原样。

导航到 AWS 控制台。您应该看到以下内容:

![](img/eb55f6428f47ae6290772a9f273ed1b0.png)

最后的检查是测试功能。选择界面右上角的`Test`按钮。如果运行成功，您应该会看到以下输出。

![](img/f4085ab24809f0be68acf626e71e388d.png)

剩下唯一要做的就是摧毁原来的服务器。

就是这样。如果您想在某个时候更新代码，您可以参考`awscli`命令[更新功能代码](https://docs.aws.amazon.com/cli/latest/reference/lambda/update-function-code.html)。