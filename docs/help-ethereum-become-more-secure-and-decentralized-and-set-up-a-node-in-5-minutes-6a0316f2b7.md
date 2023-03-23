# 帮助以太坊变得更加安全和分散，并设置一个节点

> 原文：<https://betterprogramming.pub/help-ethereum-become-more-secure-and-decentralized-and-set-up-a-node-in-5-minutes-6a0316f2b7>

## 五分钟后！

![](img/4af0ed4521e1c0cd1783658d24c690ca.png)

[以太坊](https://pixabay.com/vectors/ethereum-logo-ethereum-icon-6278328/)由 pixabay 上的 [imnamlas](https://pixabay.com/users/imnamlas-15727080/)

**免责声明:**教程中使用的实例是`m5zn.xlarge`，定价大致为 0.33 美元/小时(240 美元/月)。请不要忘记在最后一步中清理资源，除非您需要持续、无限制地访问 mainnet APIs，或者您打算成为 ETH 2.0 上的验证者。

# 环境变量:

*   `TF_VAR_AWS_ACCESS_KEY`
*   `TF_VAR_AWS_SECRET_KEY`
*   `TF_VAR_SSH_PASSWORD`
*   `TF_VAR_REGION`

依赖关系:

*   aws-cli
*   将（行星）地球化（以适合人类居住）
*   ansible
*   jq(用于解析 json)
*   ssh 键(`~/.ssh/id_rsa`和`~/.ssh/id_rsa.pub`)

在 macOS 上，那些都可以用`brew` (aws，terraform，jq)或者`pip` (ansible)安装。

# 使用

*   获取分配的 IP 地址并将其附加到 ansible hosts 文件中，将该地址导出到环境中

```
aws ec2 allocate-address > /etc/allocated-address
export NODE_IP=$(cat /etc/allocated-address | jq -r ".PublicIp")
export TF_VAR_ALLOCATION_ID=$(cat /etc/allocated-address | jq -r ".AllocationId")
echo $NODE_IP >> /etc/ansible/hosts
```

如果希望保持相同的地址，可以将`NODE_IP`和`TF_VAR_ALLOCATION_ID`变量的设置附加到`~/.profile`上。

*   启动并设置实例

```
git clone [https://github.com/piotrostr/eth-mainnet-light-node](https://github.com/piotrostr/eth-mainnet-light-node)
cd eth-mainnet-light-node
cd ./terraform && terraform init && terraform apply
cd ../
ansible-playbook ansible/main.yml
ssh ubuntu@$NODE_IP
```

*   选择一个节点并运行，可以是一个轻节点

```
geth --syncmode light --http
```

或者一个完整的节点

```
geth --http
```

*   在 geth 节点与 mainnet 同步后，它可以接受请求

```
curl [http://$NODE_IP:8545/](/$NODE_IP/)
```

# 打扫

破坏资源

```
cd ./terraform
terraform destroy
```

释放弹性 IP

```
aws ec2 release-address --allocation-id $TF_VAR_ALLOCATION_ID
rm /etc/allocated-address
```

并且不要忘记从`/etc/ansible/hosts`中移除主机`$(echo $NODE_IP)`

# 许可证

麻省理工学院