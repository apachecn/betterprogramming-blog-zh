# GitOps 兼容生产类 Kubernetes 集群指南

> 原文：<https://betterprogramming.pub/a-guide-to-gitops-compatible-production-like-kubernetes-cluster-f87404391d8a>

## 使用 Terraform、Ansible 和 Kubeadm 在 AWS 上构建自由层 Kubernetes 集群

![](img/a0077eb5b04bbdfec5927eea211ede74.png)

照片由埃里克·麦克莱恩在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在寻找一种简单、廉价、大胆(就像我们图中的朋友)的方式来创建一个类似 Kubernetes 集群的产品时，我遇到了许多选择。但是没有一个完全符合我的要求。所以，我决定自己来。由于结果相当不错，我正在写它！

在本文中，我们将讨论所使用的 Terraform 和 Ansible 代码以及先决条件。这是目录结构和文件:

```
/
├── terraform
│   ├── main.tf
├── ansible
│   ├── master-playbook.yml
│   ├── node-playbook-yml
```

我们不会深入讨论 Terraform 或 Ansible，但会保持简单明了。我希望你喜欢它。

# 先决条件

*   安装了 Terraform 和 Ansible:对于 windows 用户，我推荐 WSL。在我的例子中，我将在 Ubuntu 中使用 WSL。
*   AWS 访问具有管理 EC2 实例权限的密钥。
*   [使用 AWS Terraform provider](https://registry.terraform.io/providers/hashicorp/aws/latest/docs#authentication) 配置认证(我将使用环境变量)。
*   EC2 密钥对:为了连接到实例并运行 Ansible 代码，需要一个预先生成的密钥对。
*   VPC、子网和安全组:对于本文，我们将使用默认设置，只在安全组上添加一个入站规则，以允许来自 VPC 之外的连接。

# 将（行星）地球化（以适合人类居住）

地形代码

我们通过定义后端和声明提供者来开始我们的 Terraform 代码。在这里，我们将使用一个本地后端，以保持尽可能简单。如果您已经过了初始配置和试验阶段，建议使用远程后端，如 AWS S3 或 Azure blob 存储。

接下来，我们配置 AWS 提供程序。这里我们将使用带有`AWS_ACCESS_KEY_ID`、`AWS_SECRET_ACCESS_KEY`和`AWS_DEFAULT_REGION`环境变量的基本 AWS 提供者配置。

接下来，对于主节点和工作节点的初始配置，我们声明相关的 ec2 资源:

地形代码

这是我们集群的基本设置。我们将旋转两个工作节点，因此有 count 参数。尽管这些是相似的资源，并且有可能被一起声明，但是我们将使用分离来添加 Ansible 配置。

## 集成 Ansible

受 [*Ansible 和 HashiCorp: Better together* 视频](https://www.youtube.com/watch?v=-gKTeT3BgHE)的启发，我决定使用 Terraform 来管理使用 Ansible 的集群的配置部分。

为了实现这一点，我们将通过添加以下代码来使用在我们的`aws_instance`资源中声明的`remote-exec`和`local-exec` provisioners:

地形代码

`remote-exec`参数试图连接到由`self.public_ip`属性指定的主机，该属性将是正在创建的`aws_instance`资源的`public_ip`属性。一旦连接，它将执行由 inline 参数指定的命令。我们需要这样做，因为 Terraform 将实例解释为在实际准备好接收连接和命令之前创建的。这样,`remote-exec`保证正在创建的 ec2 实例准备好接收 ansible-playbook。

一旦`remote-exec` provisioner 完成，Terraform 就会执行`local-exec` provisioner，运行我们的 ansible-playbook 来配置节点。这就是主节点和工作节点分离派上用场的地方。以下是分别用于主节点和工作节点的命令:

```
ANSIBLE_HOST_KEY_CHECKING=False \
    ansible-playbook \
    -T 300 \
    -i '${self.public_ip},' \
    --extra-vars 
      'private_ip=${self.private_ip} \
       hostname=${split(".", self.private_dns)[0]}' \
       public_ip=${self.public_ip} \
    --private-key ~/.ssh/k8s-lab.pem ../ansible/master-playbook.ymlANSIBLE_HOST_KEY_CHECKING=False \
    ansible-playbook -T 300 \
    -i '${self.public_ip},' \
    --private-key ~/.ssh/k8s-lab.pem ../ansible/node-playbook.yml
```

除了我们将为主节点和工作节点使用不同的剧本之外，我们还需要向工作节点资源添加一个`depends_on`子句。因为我们的剧本将执行 join 命令，所以我们需要完全完成主节点资源。以下是完整的 terraform 代码:

# Ansible

Ansible 代码基于[这篇文章](https://kubernetes.io/blog/2019/03/15/kubernetes-setup-using-ansible-and-vagrant/)做了一些修改——我将介绍最重要的修改。

第一个是标准化 cgroup 驱动程序和存储驱动程序配置，根据这个问题的[，通过将以下代码添加到 master-playbook.yml 和 node-playbook.yml:](https://github.com/kubernetes/kubeadm/issues/1394)

```
- name: Creating /etc/docker/daemon.json file with optimal configs
  copy:
    dest: "/etc/docker/daemon.json"
    content: |
      {
        "exec-opts": ["native.cgroupdriver=systemd"],
        "log-driver": "json-file",
        "log-opts": {
          "max-size": "100m"
        },
        "storage-driver": "overlay2"
      }- name: Restart docker
  service:
    name: docker
    daemon_reload: yes
    state: restarted
```

第二个是更改`kubeadm init`命令，这样我们可以使用运行时变量并忽略 CPU 和内存警告:

```
# Initialize the cluster with kubeadm
  - name: Initialize the Kubernetes cluster using kubeadm
    command: "{{ item }}"
    with_items:
     - systemctl stop kubelet
     - kubeadm init --apiserver-advertise-address {{ private_ip }} 
                    --apiserver-cert-extra-sans {{ public_ip }} 
                    --node-name {{ hostname }} 
                    --pod-network-cidr=10.244.0.0/16 
                    --ignore-preflight-errors="NumCPU,Mem"
```

请注意，我们在主公共 ip 地址中使用了`apiserver-cert-extra-sans` 参数，以允许我们从本地机器中使用`kubectl`。另一个调整是关于`pod-network-cidr`参数，因为法兰绒是我们选择的网络提供商，它必须被改变(我不能在这个设置上部署 Calico)。

因此，为了部署法兰绒网络解决方案，我们添加以下代码:

```
# Setup networking provider
  - name: Install flannel pod network
    become: false
    command: kubectl apply -f [https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml](https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml)
```

这是最重要的变化，其他的是用户相关的和 apt 库相关的，因为操作系统的不同。

## 完全可变码

# 将它投入使用…

因此，有了代码，我们只需要几个命令来启动我们的集群。在 Terraform 目录上运行:

```
terraform init && terraform apply
```

init 命令将初始化目录，安装 AWS 提供程序并设置后端。apply 命令将向您显示将要调配的内容并要求确认。

这样，大约 7 分钟后(可能会根据您的互联网连接和离您选择的 AWS 区域服务器的距离而有所不同)，您将拥有一个包含一个主节点和两个工作节点的集群。如果您添加更多的工作节点，它们将被并行配置，因此时间将大致保持不变。

现在您只需要从集群中导出一个`kubeconfig`到您的本地机器和 kubectl away！将下面的代码添加到 main.tf 中就可以做到这一点。

从集群中获取 kubeconfig

# 包扎

本文介绍了使用 Terraform、Ansible 和 Kubeadm 构建免费(或非常便宜)的 AWS Kubernetes 集群的任务。通过这种设置，可以很容易地水平扩展集群，通过使用`count` 参数添加更多的工作节点，或者垂直扩展集群，选择更健壮的 ec2 实例类型。肯定有改进的空间，欢迎留下任何意见或建议。

你可以在 [my GitHub](https://github.com/rdalbuquerque/declarative-k8s-lab) 上找到代码。

# 放弃

您应该知道，如果您正在处理一个更有意义的项目，无论是哪种形式，用于这个项目的 AWS 安全组和网络配置都不理想。