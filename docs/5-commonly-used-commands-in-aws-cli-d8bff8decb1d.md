# AWS CLI 中的 5 个常用命令

> 原文：<https://betterprogramming.pub/5-commonly-used-commands-in-aws-cli-d8bff8decb1d>

## 了解如何使用 AWS CLI 快速启动 AWS 实例

![](img/eb7a46deba5b8ae9dcad7f31aa16fbba.png)

[雅利安·迪曼](https://unsplash.com/@mylifeasaryan_?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

# 什么是 AWS CLI？

开源社区开发者为 AWS 构建的命令行工具。像许多其他工具一样，这是最容易安装的工具之一，而且非常简单。你会很快恢复正常。您可能会发现自己正在寻找关于常用 AWS CLI 命令的指南。

安装可以通过`curl`在您自己的终端上完成。确保在安装包时只使用来自`awscli.amazonaws.com`的安全链接。

```
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip" && unzip awscliv2.zip && sudo ./aws/install
```

在一个全新的 bash 终端窗口中输入`*aws*` 后，你会在你的终端中看到这个。

```
usage: aws [options] <command> <subcommand> [<subcommand> ...] [parameters]
To see help text, you can run: aws help
  aws <command> help
  aws <command> <subcommand> help
aws: error: the following arguments are required: command
```

以下是开发者日常使用 AWS 设置网站和网络服务的五大命令。

请务必将本指南加入书签，并在忘记时重新阅读！

## 如何使用 AWS CLI 创建密钥对

如果您发现自己需要将 SSH 同步到一个新的实例，那么您将不得不先创建它们的密钥对组合，然后再处理其余的代码。它通常被视为建立新实例所需的第一个命令，可以在建立新实例期间和之后持续使用。您永远不知道何时需要生成新的访问密钥！把这个做书签挺好的。

```
aws ec2 create-key-pair --key-name MyKeyPair --query 'KeyMaterial' --output text > MyKeyPair.pem
```

## 如何使用 AWS CLI 列出密钥对

当处理旧实例和新实例时，您可能需要仔细检查哪些键属于哪个实例。有了列出您的密钥对的能力，您就不必担心了。使用这个命令可以立即描述您的密钥对情况。另外，这是一个容易记住的命令。

```
aws ec2 describe-key-pairs --key-name MyKeyPair
```

## 如何使用 AWS CLI 设置安全组

每次创建 VPC 或互联网网关时，您都需要知道允许哪些访问进入您的工作流程。这就是安全组介入的地方。您可以在几秒钟内设置并连接到 VPC，以确保仅在需要时才允许访问。

```
aws ec2 create-security-group --group-name my-sg --description "My security group" --vpc-id vpc-1a2b3c4d
```

## 如何使用 AWS CLI 列出安全组

不知道哪些组连接到哪些 VPC，你会把自己逼疯。对于您需要了解实例上所有可用的安全组的类型，最好将该命令加入书签。

```
aws ec2 describe-security-groups --group-ids sg-803004f8
```

## 如何使用 AWS CLI 运行 AWS 实例？

每个开发人员需要知道的最后一项能力是如何运行实例。您需要在开始之前填写每个参数，否则会收到错误。但是使用这个命令，您将能够轻松快速地在 AWS 上启动远程服务器实例。

```
aws ec2 run-instances --image-id ami-xxxxxxxx --count 1 --instance-type t2.micro --key-name MyKeyPair --security-group-ids sg-903004f8 --subnet-id subnet-6e7f829e
```

学习这些命令并保存它们以备后用只是您使用 AWS 之旅的开始。一旦掌握，您将踏上掌握 AWS 认证的道路。

编码快乐！！