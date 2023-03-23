# 使用 Ansible 清理您的 Kubernetes 部署

> 原文：<https://betterprogramming.pub/clean-up-your-kubernetes-deployments-using-ansible-10a000db313b>

## 行动手册和模板使推出 K8s 对象变得轻而易举

![](img/11d4617ea59afb39cb4a9cbf8505fff1.png)

照片由[丹尼尔·伊德里](https://unsplash.com/ja/@ricaros?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/software-engineer?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

我们都听说过 Kubernetes 部署出错的噩梦般的恐怖故事。随着 pod 开始崩溃，请求开始被丢弃，尝试推出新的服务或部署以灾难告终。避免 Kubernetes 的混乱说起来容易做起来难。

库伯内特是一种极其复杂、强大的野兽。有许许多多的抽象层，每一层都有自己的术语词典。理解 K8s 的每一个运动部件是一项艰巨的任务。不仅如此，保持一切整洁是完全独立的家务。

Kubernetes 运行在 YAML。您使用 YAML 部署服务和 pod，大多数部署的真实来源归结于在幕后运行的 YAML。管理充斥着不同对象和重复语句的庞大 YAML 文件是一件令人头疼的事情。遗憾的是，对于 Kubernetes 来说，还没有一个很好的方法来实现这一点。你不能仅仅使用`kubectl`来模板化重复的 YAML。这就是 Ansible 派上用场的地方。

在本文中，我们将探索如何使用 Ansible 来管理 K8s 部署。您可以利用 Ansible 强大的模板引擎来保持整洁，同时能够使用现有的库存、分组变量等来实现最大的灵活性。

让我们看看这是如何工作的。

## 属国

为了使用 Ansible 中的`k8s`模块，你需要确保先做一些准备:

*   安装 [Kubernetes Python 模块](https://pypi.org/project/kubernetes/)
*   [安装 Kubectl](https://kubernetes.io/docs/tasks/tools/) 并将其配置为[连接到您想要的集群](https://kubernetes.io/docs/tasks/access-application-cluster/access-cluster/)(这假设您已经[拥有一个工作正常、经过适当认证的 Kubernetes 集群](https://kubernetes.io/docs/setup/)

一旦你完成这些设置并且`kubectl`正常工作，你就可以开始使用这个模块了。

## 构建基础行动手册

在我们开始 K8s 细节之前，我们需要建立一个简单的剧本。现在让我们创建一个新的基本行动手册:

```
# playbook.yml
---
- name: deploy kubernetes objects
  hosts: localhost
  tasks:
    - debug:
        msg: "first task"
```

这是一个非常标准的剧本。这里要记住的唯一要点是，我们已经瞄准了`localhost`,因为模块将使用您的本地机器的配置来连接到集群。

这与您如何通过`kubectl`部署对象非常相似，只是我们将所有东西都包装在 Ansible 中。

## 运行模块

现在我们已经有了一个基本的剧本大纲，我们可以开始添加更多的模块。让我们用实际的`k8s`模块用法替换我们的调试语句:

```
# playbook.yml
---
- name: deploy kubernetes objects
  hosts: localhost
  tasks:
    - name: deploy services
      k8s:
        state: present
        definition: "{{ lookup('template', item) | from_yaml }}"
      with_fileglob:
        - ./*_service.j2
```

让我们具体分析一下这项新任务中发生了什么:

*   该任务使用`k8s`模块，让您针对本地机器上当前配置的任何集群执行操作。
*   我们告诉`k8s`模块使用 YAML 定义，并将该定义中的任何内容部署到集群中。
*   我们遍历所有以`_service.j2`结尾的模板文件并部署它们。每个循环使用针对特定文件的`fileglob`模块执行模板查找。
*   模板被转换为定义的 YAML。我们这样做是为了利用模板，做一些有趣的事情，比如变量替换，但是要把它们翻译回原始的 YAML 以便最终应用。

行动手册已经准备好了，让我们继续看实际的模板。

## 构建服务定义模板

让我们看看如何创建一个模板，以便将新服务部署到集群中。下面是您可能在`test_service.j2`中为我们的部署找到的示例:

```
---
apiVersion: v1
kind: Service
metadata:
  name: {{ test_service_name }}-svc
  namespace: {{ test_namespace }}
spec:
  type: ClusterIP
  ports:
    - name: example-port
      protocol: TCP
      port: 12345
```

看起来很简单，对吧？除了您可以将其模板化之外，它看起来就像一个普通的服务定义。默认情况下，你不能用普通的 K8s YAML 做到这一点，但是当它在一个 Ansible 模板中时你可以。

为了简单起见，我们只添加了两个变量，但是您可以使用任何形式的 [Jinja2](https://jinja.palletsprojects.com/en/3.1.x/) 模板逻辑，就像其他 Ansible 模板一样。

## 添加一些变量

变量的一个重要用途是我们模板中的服务`name`和`namespace`。在模板中，我们用剧本中引入的变量替换它们。让我们现在就开始吧:

```
# playbook.yml
---
- name: deploy kubernetes objects
  hosts: localhost
  vars:
    test_service_name: test_service
    test_namespace: test_namespace
  tasks:
    - name: deploy services
      k8s:
        state: present
        definition: "{{ lookup('template', item) | from_yaml }}"
      with_fileglob:
        - ./*_service.j2
```

在我们行动手册的`vars`部分，我们添加了两个新变量。对于这个例子来说，这在剧本中是完全独立的。如果你已经有了一个扩展的 Ansible 配置，你可以引用任何你想要的变量。这可能是`host_vars`、`group_vars`等。

## 把所有的放在一起

为了进行部署，您只需运行行动手册:

```
ansible-playbook -i <inventory> ./playbook.yml
```

应该应用服务定义，并且使用`kubectl`您现在应该能够在适当的名称空间中看到您的新服务:

```
kubectl get svc -n test_namespace
```

如果您想删除您使用本行动手册部署的任何内容，只需将任务状态从`present`更改为`absent`。重新运行剧本后，这些对象应该会被删除。

此外，请记住，由于我们在行动手册中只针对`localhost`，清单并不真正用于任何主机或组信息。然而，如果你在你的库存文件中定义了全局变量，这些变量可能会影响游戏。

# 最后的想法

本指南展示了用 Ansible 部署 Kubernetes 对象的两个强有力的想法:

*   首先是你现在可以[干](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself)YAML 给 k8 加油了。您可以模板化重复部件、使用变量和执行循环。所有你之前默认不能轻易做到的事情。
*   下一个想法是在部署时能够有一个可利用的程序剧本。不再运行短暂的`kubectl`命令。现在你有了一个一步一步的剧本，告诉你到底会发生什么，以及到目前为止已经发生了什么。最好的部分是，因为剧本中会引用所有内容，所以如果有问题，您可以更轻松地回滚。

现在，您可以利用这些知识，使您的 Kubernetes 流程比以往更干净、更高效、更稳定。

感谢阅读！同时，这里还有几篇文章:

*   [*用基本的 GPG*](/keep-your-secrets-safe-with-basic-gpg-encryption-dffb292b2d37) 轻松实现文件加密
*   [*阅读这些书，你会写出更好的代码*](https://medium.com/geekculture/read-these-books-and-youll-write-better-code-e5bff4b0ee8f)