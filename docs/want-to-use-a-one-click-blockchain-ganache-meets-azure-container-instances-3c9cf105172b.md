# 想用一键区块链？Ganache 遇到 Azure 容器实例

> 原文：<https://betterprogramming.pub/want-to-use-a-one-click-blockchain-ganache-meets-azure-container-instances-3c9cf105172b>

## 如何使用 Azure 容器实例托管 Ganache

![](img/1d006b70e95778b98bbb5a3f7aaa126c.png)

由 [Shubham Dhage](https://unsplash.com/@theshubhamdhage?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

当我开始学习更多关于 Web3 的知识时，我被介绍给了“一键式区块链”。

这对当地发展大有裨益。我想找出一种方法，将它作为公关工作流程的一部分，与 Azure Static Web Apps 配合使用。Azure 静态 Web 应用程序可以在公共关系期间为审查变更提供一个临时环境。

如果应用程序是分布式应用程序(dApp)，要查看更改，您必须部署智能合同。在这里，我想使用 [Azure 容器实例](https://docs.microsoft.com/en-us/azure/container-instances/container-instances-overview) (ACI)来托管 Ganache 的临时实例，以便在 PR 期间部署智能合约。

首先，我需要弄清楚如何部署 ACI 来托管来自 trufflesuite/ganache 的 Ganache Docker 映像。我将使用跨平台的 Azure CLI 和 PowerShell。要创建 ACI，我需要一个资源组。我可以创建一个新的资源组，或者将其存储在与 SWA 相同的资源组中。如果我想为 ACI 创建一个资源组，我可以使用以下命令:

```
az group create --resource-group test-ganache --location centralus
```

创建资源组后，我使用以下命令部署 ACI:

```
$aci = az container create --resource-group test-ganache --name ganache --image trufflesuite/ganache:latest --ports 8545 --ip-address Public --output json | ConvertFrom-Json
```

上面的命令使用 PowerShell 将`az container create`命令的 JSON 输出转换成 PowerShell 对象，并将其存储在 *$aci* 中。我需要这个，这样我就可以访问 ACI 的 IP 地址。`create`命令公开端口 8545，这是 Ganache 使用的默认端口，并请求容器获得一个公共 IP。

因为我是在安装了 geth 的交互式 PowerShell 会话中运行这些命令的，所以我使用以下命令连接到这个区块链:

```
geth attach [http://$($aci.ipAddress.ip):8545](/%24(%24aci.ipAddress.ip):8545)
```

如果我需要访问帐户地址或私钥，我可以查看容器的日志。为了获取启动时输出地址和密钥的容器的日志，我使用了以下命令:

```
$logs = az container logs --container ganache --resource-group test-ganache --name ganache
```

输出存储在 *$logs，*中，我使用下面的命令使用`Select-String` CmdLet 获取地址和私钥:

```
# Get first address
$address = ($logs | Select-String '\(0\) ([^\(]+) \(').Matches.Groups[1].Value # Get first private key
$key = ($logs | Select-String '\(0\) ([^\(]{64,})').Matches.Groups[1].Value
```

上面的命令获取第一个地址。如果我想要一个不同的帐户，我只需将正则表达式中的索引从 0 调整到所需的索引。例如，要获得第二个帐户，请将上面命令中的\(0\)更改为\(1\)。

为了清理我的 ACI，我使用了以下命令:

```
az container delete --resource-group test-ganache --name ganache
```

或者，如果我想删除整个资源组，我可以使用以下命令:

```
az group delete --resource-group test-ganache
```

感谢阅读。下次见！

*最初发表于*[](https://www.donovanbrown.com/post/How-to-use-Azure-Container-Instances-to-host-Ganache)**。**