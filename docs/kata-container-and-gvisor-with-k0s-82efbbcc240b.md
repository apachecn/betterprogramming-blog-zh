# 带 K0s 的 Kata 容器和 GVisor

> 原文：<https://betterprogramming.pub/kata-container-and-gvisor-with-k0s-82efbbcc240b>

## 使用另一个容器运行时来获得更好的隔离和安全性

![](img/d5ef5b303f5db5c0d5aad6ceb1416330.png)

由[罗克珊·德斯加涅斯](https://unsplash.com/@roxannedesgagnes?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/isolated?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

因为不是所有的 pods 都是可信的，所以本文将展示通过使用容器运行时而不是默认运行时(runc)来增强进程隔离的不同选项。我们将使用 Kubernetes [k0s](https://k0sproject.io) 分布来说明这一切。如果你不知道 k0s，你可以在[这篇文章](https://medium.com/better-programming/k0s-kubernetes-in-a-single-binary-224bb43f4520)中找到快速介绍。

# 创建 K0s 集群

在介绍文章中，我们详细介绍了轻松设置 k0s 集群所需的步骤。最近/usr/bin/qemu-vanilla-system-x86_64

这表明`qemu`在 worker 节点上创建了一个新的虚拟机。这些流程在以下链中被调用:

> kube let→container d→container d-shim-kata-v2→kata

# 关键要点

正如我们所见，使用额外的容器运行时并不复杂。gVisor 和 Kata 容器是两种已经广泛使用的技术，但也存在其他解决方案。所有这些`runc`备选方案的主要目标是更好地隔离箱/集装箱，增强安全性。