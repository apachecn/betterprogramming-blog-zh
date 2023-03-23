# 可怕的 Kubernetes 命令行黑客

> 原文：<https://betterprogramming.pub/awesome-kubernetes-command-line-hacks-8bd3604e394f>

## 给你像专业人士一样学习的建议

![](img/d6d29ee01034d0c8a873ac0f147a05f2.png)

[目标](https://unsplash.com/@arget?utm_source=medium&utm_medium=referral)在[未飞溅](https://unsplash.com?utm_source=medium&utm_medium=referral)上拍照。

Kubernetes 管理员将大部分时间花在键入`kubectl`命令和编写 YAML 清单文件上。然而，有经验的 Kubernetes 管理员与新手的区别在于他们利用`kubectl`的方式。

这里有一些节省宝贵时间和充分利用`kubectl`的建议。

# 使用别名

每个有经验的系统管理员都会使用别名。这有助于他们节省键入命令的宝贵时间。例如，大多数系统管理员不输入`ls -l`，而是使用`ll`。同样，对于 Kubernetes，您可以使用以下有用的别名。

## k 代表 kubectl

用`k`代替`kubectl`。如果你能用一个字母做同样的事情，为什么要输入一个七个字母的单词呢？

```
alias k=kubectl
```

## kubectl 像专业人士一样进行模拟演练

键入整个`dry-run`标志可能会很累——尤其是当您在一天中多次运行它时。像下面这样简单的别名可以让你的生活变得更容易:

```
alias kdr='kubectl --dry-run=client -o yaml'
```

现在你只需将你的 YAML 文件收好:

```
kdr run nginx --image=nginx > nginx.yaml
```

## 轻松应用和删除

同样，您不需要在每次应用或删除配置时输入完整的`kubectl apply`或`kubectl delete`。请使用下面的别名:

```
alias kap='kubectl apply'
alias kd='kubectl delete'
```

## 像巫师一样用 busybox 进行冒烟测试

为了进行冒烟测试，我们大多数人键入以下命令:

```
kubectl run busybox-test --image=busybox -it --rm --restart=Never -- <command>
```

那是少数。想象一下，如果你每天需要打差不多 100 次，那有多痛苦。下面的别名可以很好地简化它:

```
alias kbb='kubectl run busybox-test --image=busybox -it --rm --restart=Never --'
```

然后简单地使用:

```
kbb <command>
```

同样，您可以根据一天中最常运行的命令来虚构您的别名。下面是我常用的一些:

```
alias kdb='kubectl describe'
alias kl='kubectl logs'
alias ke='kubectl exec -it'
```

# 对 kubectl 命令使用 Bash 自动完成功能

有多少次你在输入`kubectl`命令时按下 TAB 键，然后意识到它不起作用？好吧，这里有个窍门。您可以在您的`.bashrc`文件中安装一个 bash autocomplete 插件，它会非常好用。

为此，请使用以下命令:

```
echo "source <(kubectl completion bash)" >> ~/.bashrc
```

这非常有用，节省了大量时间，因为您现在可以通过简单地按 TAB 键来自动完成命令。

# 不要从头开始创建 YAML 清单

从头开始创建 YAML 清单既耗时又容易出错，尽管使用声明性 YAML 文件进行生产是一种最佳实践，但这并不意味着您需要手动创建它们。

利用`dry-run`标志从命令性命令中生成 YAML 文件。使用我描述的`kdr`别名来加快体验。

这不仅会节省宝贵的时间，还会让你的体验没有错误和压力。节省下来的时间，你可以投入更多的时间来解决实际问题。

管理员使用这种方法面临的一个问题是，并不是所有的 Kubernetes 资源都有命令式的命令行等价物。但是您可以使用与配置最匹配的资源来生成 YAML。

例如，如果你想生成一个`DaemonSet`，你可以用`kubectl dry-run`一个`Deployment`，然后根据`DaemonSet`规范修改它。

# 使用 kubectl 解释

使用`kubectl explain`比在在线文档中来回跳转更有帮助。它很容易理解，并为您提供了关于资源规格的足够信息。

例如，让我们用`kubectl dry-run`创建一个 pod，然后使用`kubectl explain`来理解如何向 pod 添加资源请求和限制。

声明别名:

```
$ alias k='kubectl'
$ alias kdr='kubectl --dry-run=client -o yaml'
```

现在让我们使用`dry-run`生成 pod 清单:

我们看到资源部分在 pod 的`spec.containers`部分中。现在让我们运行`kubectl explain`:

好的，这些信息足够让你开始了。您可以检查`requests`和`limits`规格，查看可能的值和相关细节。

```
$ k explain pod.spec.containers.resources.requests
$ k explain pod.spec.containers.resources.limits
```

# JSON 路径非常棒

JSON 路径乍一看似乎很可怕，但是相信我，一旦你掌握了窍门，它们一点也不难。您可以简单地使用`kubectl explain`和 JSON 路径来对`kubectl`输出进行高级操作。例如，您可以使用 JSON 路径从部署中获取 pod 名称，并将它们提供给单元测试脚本。

您可以使用 JSON 路径的高级排序来减少大量开销(例如，手动查找和过滤)。

下面是来自 kubectl cheatsheet 的一些例子，列出了使用 JSON 路径的多种方式:

# 结论

这些建议可以帮助你在 Kubernetes 的职业生涯中走得更远，并彻底享受你的 Kubernetes 体验。你可以有自己的创新方法来简化你的工作，并且可以随意使用任何能简化你工作的方法。

感谢阅读。我希望你喜欢这篇文章。