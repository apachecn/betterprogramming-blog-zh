# Kubernetes 从来不是为批量作业设计的

> 原文：<https://betterprogramming.pub/kubernetes-was-never-designed-for-batch-jobs-f59be376a338>

## 如果是这样的话会是什么样子

![](img/d1085e26f686236d52ef2651dfad01f6.png)

一批饼干。丽莎·汉莉在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

在本帖中，我们将说明 Kubernetes 在哲学上偏向于微服务而不是批处理作业。这导致阻抗不匹配，使得使用 Kubernetes 进行批处理作业变得比“应该”更困难。

## 批量世界 vs 微服务世界

我花了一段时间才弄明白的一件事是，为什么这么多广受欢迎的技术对我来说如此怪异。当我意识到我生活在“批量世界”时，它就点击了，这些技术是从“微服务世界”出来的。

我主要在对冲基金工作，我工作过的那些是 batch world 的一部分。这意味着代码通常在外部事件触发时运行，如供应商发送当天美国股票的收盘价，或清算所发送当天记录的交易列表。像 Airflow 这样的作业调度器根据发生的事件触发作业运行。该作业通常会触发其他作业，这些作业的最终输出类似于对执行交易的外部服务的 API 调用或通过电子邮件发送给人类的 PnL(损益)报告。

相比之下，在微服务领域(对此要有所保留，因为我只在那里旅游过)，不是那些运行到完成的作业，一切都是长期运行的服务。因此，与其接受美国股票收盘价格，然后开始计算 PnL 报告的工作，可能会有一个 PnL 报告服务，轮询价格服务，直到它需要的价格可用。

## 但是真的有什么区别吗？

批处理作业和服务之间的区别似乎很容易定义——批处理作业由一些“外部”事件触发，进行一些计算，然后自行退出。另一方面，服务永远在一个循环中运行，接受请求，进行少量的计算，然后响应这些请求。

但是我们可以用一种听起来像服务的方式来描述批处理作业，比如说批处理作业的每次调用都是一个“请求”。在这个视图中，运行批处理作业的整体模式看起来像一个服务，作业调度器扮演负载平衡器的角色，每个批处理作业调用扮演处理请求的角色。这种在自己的进程中处理每个请求的模式类似于相当常见的“[分叉服务器](https://unixism.net/2019/04/linux-applications-performance-part-ii-forking-servers/)”模式。

反之亦然，考虑一下这个思想实验，我们将代码分解成数千个不同的微服务，但我们没有足够的硬件来同时运行所有这些微服务。因此，我们配置了一个复杂的、响应迅速的自动缩放器，当有请求进来时，它只在单个请求/回复的持续时间内启动每个微服务。在这一点上，我们的 autoscaler 开始看起来更像一个作业调度器，我们的微服务看起来有点像批处理作业！

让我们澄清一下我们最初的直觉——我们对服务“永远运行”的描述是不精确的。的确，大多数服务不会自己“成功”退出，但是它们可能会由于灾难性的异常而退出，作为对自动伸缩决策的响应，或者作为部署过程的一部分。那么，当我们说服务“永远运行”时，我们实际上在说什么呢？最重要的一点是，我们认为服务是“无状态的”，也可以说是“服务很容易重启”。因为每个请求/回复周期通常是几毫秒到几秒的量级，并且在请求处理程序之外的服务器进程中通常只有很少的状态，所以重新启动服务应该不会让我们损失超过几秒的计算时间。相比之下，批处理作业通常“不可重启”，也就是说，如果我们有一个需要几个小时运行的批处理作业，而我们在一个小时后重启它，我们将需要重做第一个小时的工作，这通常是不可接受的。如果我们以某种方式编写了一个批处理作业，它每 5 秒创建一个状态检查点，从而使它或多或少地“无状态”，那么这个批处理作业就像服务一样“容易重启”。

因此，服务和批处理作业之间最合理的区别在于，服务一次只做少量(几毫秒到几秒)的工作，这使得它们很容易重启，而批处理作业一次做大量(几分钟到几小时)的工作，这使得它们很难重启。以这种方式陈述，服务和批处理作业之间的区别比它第一次出现时更模糊。

但是，仅仅因为我们可以想出难以归类的例子来挑战这种二元性，并不意味着这种差异是无意义的。实际上，服务通常作为响应请求的长期流程运行，为每个请求做一点工作，然后做出响应。与此同时，批处理作业通常由数据科学家或作业调度程序临时触发，并且一次执行大量工作，这使得它们更难重启。

查看服务与批处理作业之间差异的另一种方式是它们如何扩展。扩展服务通常意味着通过让自动缩放器运行服务的副本，负载平衡器将不同的请求发送到不同的副本，来处理大量的并发请求。扩展批处理作业通常意味着通过在不同的数据块上并行运行相同的代码来处理大量数据。我们将在下面更深入地探讨这些方面，因为我们将了解 Kubernetes 对这些场景的支持程度。

# Kubernetes 如何看世界

> 技术没有好坏之分；也不是中立的。梅尔文·克兰茨伯格

我们将跳过 Kubernetes 的全部历史和功能，因为现有的报道很多([这篇来自比特币基地](https://blog.coinbase.com/container-technologies-at-coinbase-d4ae118dcb6c)的帖子给出了一个很好的概述)。我们在这里的重点是理解 Kubernetes 的哲学——它如何看待这个世界，它为用户创造了什么样的模式。

![](img/460df44fa879640ba79535cf9d0c9823.png)

*都是哲学。通过维基共享资源，罗丹博物馆*地狱之门*中的思想者*

## Kubernetes 代表服务

我们将从 Kubernetes 文档中“概述”页面上的“[Kubernetes 能做什么](https://kubernetes.io/docs/concepts/overview/#why-you-need-kubernetes-and-what-can-it-do)”部分开始。此处列出的大多数关键功能都侧重于服务，而不是批处理作业:

*   “服务发现和负载平衡”将域名解析为服务的一个或多个副本。这与批处理作业无关，批处理作业通常没有请求/响应的概念，因此不需要将域名解析为容器，也不需要在服务的不同实例之间进行循环请求。
*   “自动推出和回滚”通过关闭一些实例，使用新的部署重新启动它们，然后重复直到所有实例都被更新，从而使服务的部署变得更加容易。这种想法不适用于批处理作业，因为批处理作业“很难重启”，并且会自动退出，所以正确的做法是等到批处理作业完成，然后使用新部署启动后续作业，而不是因为重启而丢失工作。我们当然不希望滚动部署导致分布式作业，不同的任务在不同版本的代码上运行！
*   “自我修复”:重启失败的作业是有用的，但是批处理作业没有“健康检查”的概念，并且在客户端准备好之前不向客户端通告服务与批处理作业无关。
*   “自动装箱”只与批处理作业部分相关—我们肯定希望智能地对运行作业的位置进行初始分配，但同样，批处理作业不能随意重启，因此它们不能“移动”到不同的节点。
*   “机密和配置管理”和“存储协调”对于服务和批处理作业同样重要。

贯穿这些特性的一个主题是，Kubernetes 假设它运行的代码相对容易重启。换句话说，它假设自己正在运行服务。

## Kubernetes 不相信配器

同一个[概览页面](https://kubernetes.io/docs/concepts/overview/#what-kubernetes-is-not)声明:

> Kubernetes 不仅仅是一个编排系统。事实上，它消除了对编排的需要。编排的技术定义是执行定义的工作流:首先执行 A，然后 B，然后 c。相比之下，Kubernetes 包括一组独立的、可组合的控制流程，这些流程不断地将当前状态推向所提供的期望状态。如何从 A 到 c 并不重要，也不需要集中控制。这使得系统更易于使用，并且更强大、健壮、有弹性和可扩展。

这一段大概是指在 Kubernetes 中，你以声明的方式定义你的配置(例如，确保这个服务一直有 3 个实例在运行)，而不是强制性的(例如，检查有多少个实例在运行，如果超过 3 个，就终止实例，直到剩下 3 个；如果少于 3 个，启动实例，直到我们有 3 个)。

然而，像 Airflow 这样的作业调度器是一种编排框架，正如本段所描述的那样。当然，我们可以在 Kubernetes 上运行 Airflow 来解决这个问题，但 Kubernetes 故意让这种编排难以在本地实现。

Kubernetes 有一个用于运行批处理作业的“[作业](https://kubernetes.io/docs/concepts/workloads/controllers/job/)”概念，但是对“先做 A，再做 B”的想法的厌恶意味着作业 API 可能永远无法表达这个核心概念。在 Kubernetes 中表达作业依赖关系的唯一希望是一个声明性模型——“为了运行 B，A 必须已经成功运行”。但是这个特性也不存在，尽管完全声明的模型有它的优点，但它目前还不是表达作业依赖关系的主流范例。

此外，在 Kubernetes，工作显然是次要的。作业根本不会出现在概览页面中，除了与作业明确相关的部分之外，在整个文档中，作业大多被忽略。一个明显的缺失是[这一页](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/)中写道“吊舱不会消失，除非有人(一个人或一个控制者)破坏了它们，或者出现了不可避免的硬件或系统软件错误。”这莫名其妙地忽略了 pods for jobs 自然完成的情况。

# 更多缺失的功能

不仅是 Kubernetes 的工作本身并不完整，它们还存在于一个更大的系统中，这个系统的设计理念是让工作变得不那么有用。在本文的其余部分，我们将研究 Kubernetes 的一些细节，以及为什么它们使批处理作业变得更加困难。

## 吊舱抢占

工作和服务都以“[豆荚](https://kubernetes.io/docs/concepts/workloads/pods/)的形式实现，理论上是中性的，但实际上偏向服务。一个例子是，pod 可以被抢占，以便为其他 pod 腾出空间，这假设 pod 很容易重启。文档承认这个[不适合批处理作业](https://kubernetes.io/docs/concepts/scheduling-eviction/pod-priority-preemption/#non-preempting-priority-class)，但是它提出的建议有点落后——它建议批处理作业应该设置`preemptionPolicy: Never`。这意味着这些 pod 永远不会抢占另一个只有在集群上的所有 pod 做同样的事情时才起作用的作业。理想情况下，有一种方法可以保证 pod 本身永远不会被抢占，即使在运行批处理作业和服务的集群中也是如此。有一些变通办法，比如为批处理作业保留更高的优先级，或者使用 [pod 中断预算](https://kubernetes.io/docs/tasks/run-application/configure-pdb/)，但是在那个页面上没有提到这些。这正是我们所说的阻抗不匹配的含义——我们最终可以完成我们需要做的事情，但需要付出比“应该”付出更多的努力。

## 可组合性

Kubernetes 只与容器一起工作，容器本身在如何组合方面也偏向于服务。如果我们有两个需要相互通信的容器，公开其中一个或两个容器中的服务是唯一的选择。

例如，假设我们想要运行一些 Python 代码，这些代码需要调用 [ImageMagick](https://imagemagick.org/index.php) 来进行一些图像处理。我们希望在基于 [Python Docker 图像](https://hub.docker.com/_/python)的容器中运行 Python 代码，并且我们希望在基于[该 ImageMagick Docker 图像](https://hub.docker.com/r/dpokidov/imagemagick)的单独容器中运行 ImageMagick。让我们考虑一下从 Python 容器调用 ImageMagick 容器的选项，即组合这两个容器。

*   我们可以使用 Python 图像作为基础图像，并将 ImageMagick Dockerfile 文件的一部分复制到一个新的 docker 文件中，以构建一个定制的组合图像。这可能是最实用的解决方案，但它具有复制/粘贴的所有常见缺点——如果不手动更新我们的 Dockerfile，对 ImageMagick Dockerfile 的任何改进都不会进入我们的 Dockerfile。
*   我们可以像调用命令行应用程序一样调用 ImageMagick 容器。Kubernetes 支持在同一个 pod 中的容器之间共享文件，所以至少我们可以来回发送我们的输入/输出，但是没有很好的方法来调用命令并在完成时得到通知。当然，任何事情都是可能的(例如，开始一个新任务并轮询 pods API 以查看它何时完成)，但是 Kubernetes 对编排的哲学厌恶在这里没有帮助。
*   我们可以修改 ImageMagick 图像来公开服务。这看起来很傻，但实际上大多数时候都是这样——人们没有构建像 ImageMagick 这样的命令行工具，而是构建服务来回避这个问题。

事实上，在 Docker 中，“[组合](https://docs.docker.com/compose/)”意味着组合服务:

> Compose 是一个定义和运行多容器 Docker 应用程序的工具。使用 Compose，您可以使用 YAML 文件来配置应用程序的服务。然后，只需一个命令，您就可以从您的配置中创建并启动所有服务。

即使我们默认了通过服务进行组合的想法，Kubernetes 也没有为我们在批处理作业是消费者的环境中运行服务提供很好的选择:

*   最简单的选择是不断地运行 ImageMagick 服务的一个或多个副本，但是当我们不运行任何需要该服务的东西时，这是对资源的浪费，并且当我们启动运行数千个并发任务的分布式作业时，它将不堪重负。
*   因此，我们可能会使用 [HorizontalPodAutoscaler](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) 来在需要时旋转实例，在不需要时关闭它们。这是可行的，但是为了获得在命令行上调用的响应，我们需要使自动缩放器的“同步周期”比默认的 15 秒短得多。
*   另一个选项是“ [sidecar containers](https://kubernetes.io/docs/concepts/workloads/pods/#workload-resources-for-managing-pods) ”，我们在同一个 pod 中运行 Python 映像和 ImageMagick 服务映像。这基本上是可行的，但是[没有办法在“主”批处理作业容器完成时自动终止](https://stackoverflow.com/questions/38600622/sidecar-containers-in-kubernetes-pods)服务的 sidecar 容器。允许这一点的提议特性最终被[拒绝了](https://github.com/kubernetes/enhancements/issues/753#issuecomment-713471597)，因为它“不是正确方向上的渐进步骤”。

探索批处理作业如何调用其他容器的这些选项表明，我们可以让它工作，但 Kubernetes 使它比它“应该”的更难。

## 临时工作

批处理作业的一个特点是，我们经常临时运行它们来进行研究、开发或故障排除。例如，我们编辑一些代码或调整一些数据，然后重新运行我们的线性回归，看看我们是否得到更好的结果。对于这些临时作业，理想情况下会有某种方法来获取我们正在本地处理的 CSV 文件，并将其“上传”到我们可以从 pod 中读取的卷。但是 Kubernetes 不支持这种场景，所以我们必须想出其他方法将数据放入我们的 pod。

一种选择是设置一个可从集群外部访问的 NFS(网络文件系统),并[将其](https://kubernetes.io/docs/concepts/storage/volumes/#nfs)暴露给集群中的 pod。另一个选择是，像往常一样，某种服务。我们可以使用一个类似于 [RabbitMQ](https://www.rabbitmq.com/) 的队列来临时存储我们的数据，并使其对我们的 pod 可用。

但是对于服务，我们现在有了从集群外部访问该服务的另一个问题。解决这个问题的一个方法是让我们的开发机器成为 Kubernetes 集群的一部分。然后，我们可以为 RabbitMQ 配置一个简单的[服务](https://kubernetes.io/docs/concepts/services-networking/service/)，可以从集群内部访问它。但是，如果这不是一个选项，我们将需要探索在集群中访问应用程序的[。选项归结为使用](https://kubernetes.io/docs/tasks/access-application-cluster/)[端口转发](https://kubernetes.io/docs/tasks/access-application-cluster/port-forward-access-application-cluster/)，这是一个不推荐用于“真实”用例的调试工具，或者设置一个[入口控制器](https://kubernetes.io/docs/concepts/services-networking/ingress-controllers/)，这是一种公开 Kubernetes 集群中运行的服务的正式方式，对于从内部网络访问服务的单个实例的单个客户端来说，这是一种过度的做法。这些选择都不理想。

Kubernetes 并没有让这变得容易，因为当我们运行服务时，我们要么不希望集群之外的任何东西与我们的服务交互，要么我们希望在我们的服务之前有一个相对重型的负载平衡器，以确保我们的服务不会因外界的负载而中断。这里缺少的功能是启动作业/pod 的客户端上传 pod 可以访问的文件的某种方式。这种情况在运行批处理作业时比较常见，在运行服务时不常见，所以在 Kubernetes 中不存在，我们必须使用这些变通方法。

## 分布式工作这种工作

批处理作业的另一个方面是，我们经常希望运行分布式计算，将数据分成块，并在每个块上运行一个函数。一个[流行的选择](https://news.ycombinator.com/item?id=27915761)是在 Kubernetes 之上运行 Spark，这正是为这个用例构建的。另外[还有](https://volcano.sh/en/) [其他](https://github.com/G-Research/armada/) [选项](https://github.com/kubernetes-sigs/kube-batch)用于添加软件，使在 Kubernetes 上运行分布式计算更容易。

然而，Kubernetes 文档并没有让位于第三方框架，而是给出了几个直接在 Kubernetes 上运行分布式计算的[选项](https://kubernetes.io/docs/concepts/workloads/controllers/job/#job-patterns)。但是，这些选项都没有吸引力，尤其是与 Kubernetes 为服务工作负载精心设计的方式相比。

最简单的选择是[为每个任务](https://kubernetes.io/docs/tasks/job/parallel-processing-expansion/)创建一个作业对象。正如文档所指出的，这对于大量的任务来说效果不好。[一个用户的体验](https://github.com/kubernetes/kubernetes/issues/95492)是很难超越几千个工作总量。似乎[绕过](https://kubernetes.io/docs/tasks/job/indexed-parallel-processing-static/)的最佳方式是使用索引作业，这是一个相对较新的功能，用于运行同一作业的多个副本，其中作业的不同副本具有不同的`JOB_COMPLETION_INDEX`环境变量的值。这为我们提供了运行分布式作业的最基本的层。只要每个任务只需要一个索引号，不需要“发回”任何输出，这就可以了。例如，如果所有的任务都在处理一个文件，并且任务“知道”它们需要处理跳过第一个`JOB_COMPLETION_INDEX * n`行之后出现的`n`行，然后将它们的输出写入数据库，这就很好了。

但是在某些情况下，我们会希望运行一些任务，例如需要一个文件名来知道它们的输入数据在哪里，并且可以方便地将结果直接发送回启动分布式作业的进程，以便进行后期处理。换句话说，我们可能需要在任务之间来回发送更多的数据，而不仅仅是一个数字。为此，文档提供了在 Kubernetes 集群中使用消息队列服务的两种 [变体](https://kubernetes.io/docs/tasks/job/fine-parallel-processing-work-queue/)。这种方法的主要困难是，我们遇到了和以前一样的问题，即从 Kubernetes 集群外部访问集群内部的服务，以便向消息队列服务添加消息。文档建议[创建一个临时的交互舱](https://kubernetes.io/docs/tasks/job/coarse-parallel-processing-work-queue/#testing-the-message-queue-service)，但那只对测试有意义。我们有与以前相同的选项—确保包括我们的开发机器在内的一切都在群集内运行，使用端口转发或创建入口。

## 分布式分组依据

分布式计算的另一个问题是分布式“分组”。当我们的数据集按一列(如日期)进行“分块”或“分组”时，分布式分组是必要的，但我们希望在应用计算之前按不同的列(如邮政编码)进行分组。这需要对我们的原始块进行重新分组，这被实现为“ [shuffle](https://spark.apache.org/docs/2.3.0/rdd-programming-guide.html#shuffle-operations) ”(也称为 map-reduce)。工人之间的交流是洗牌的核心。每个按日期工作线程获取特定日期的数据块，然后将每个邮政编码的行发送给一组按邮政编码的下游工作线程。按邮政编码工作人员从每个上游按日期工作人员接收“他们的”邮政编码行。

为了实现这种工人对工人的通信，Kubernetes 可以实现某种版本的[共享卷](https://kubernetes.io/docs/tasks/access-application-cluster/communicate-containers-same-pod-shared-volume/)，这将允许我们将结果从一个 pod 公开给需要这些输出的下游 pod。同样，这个功能在“批处理世界”中非常有用，但是用例在“服务世界”中不存在，所以这个功能不存在。相反，我们需要为工人与工人之间的通信编写自己的服务。

例如，Spark 就是这样做的，但它也会遇到阻抗不匹配的问题。Spark 的 shuffle 实现广泛使用本地磁盘来处理不适合内存的数据。但 Kubernetes 很难获得本地磁盘的全部性能，因为它只允许在容器中进行磁盘缓存，这意味着内核的磁盘缓存没有被使用，Spark 的 shuffle 性能在 Kubernetes 上受到影响。跨 pod 共享文件的更本地的方式将实现更快的实现。

## 缓存数据

我们将要讨论的分布式计算的另一个方面是缓存数据。大多数分布式计算都有一个分布式数据集的概念，这个数据集缓存在工作线程上，以后可以重用。比如 Spark 有 [RDDs](https://spark.apache.org/docs/2.3.0/rdd-programming-guide.html) (弹性分布式数据集)的概念。我们可以在 Spark 中缓存一个 RDD，这意味着每个 Spark worker 将存储一个或多个 RDD 块。对于该 RDD 上的任何后续计算，拥有特定块的工作线程将运行该块的计算。这种“向数据发送代码”的一般思想是实现高效分布式计算系统的一个重要方面。

Kubernetes 总体上对在节点上存储数据的想法有点不友好，[推荐](https://kubernetes.io/docs/concepts/storage/volumes/#hostpath)“尽可能避免使用主机路径是一种最佳实践”。尽管有通过[亲和、反亲和](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#affinity-and-anti-affinity)、[污染、容忍](https://kubernetes.io/docs/concepts/scheduling-eviction/taint-and-toleration/)和[拓扑分布约束](https://kubernetes.io/docs/concepts/scheduling-eviction/topology-spread-constraints/)将 pod 分配给节点的广泛功能，但这些功能都无法处理“特定节点上有哪些缓存数据可用”的概念。

总的来说，Kubernetes 对本地运行分布式计算有一些半心半意的支持。除了最简单的场景之外，任何事情都需要在 Spark 这样的另一个平台上实现解决方案或分层。

# Kubernetes 上批处理作业的未来

本文的观点并不是说 Kubernetes 考虑不周或实现不佳。可以说，[如果你有一个单一的服务](https://news.ycombinator.com/item?id=23359443)，Kubernetes 是多余的，至少有一个人预测它将在 5 年内[消失](https://news.ycombinator.com/item?id=22402315)，但它似乎是一个伟大的选择，例如[这个人运行 120 个微服务](https://news.ycombinator.com/item?id=26272144)。它使部署带有数据库[、DNS 记录和 SSL 证书](https://news.ycombinator.com/item?id=27910150)的 web 应用程序变得容易。这里我们要说的是，Kubernetes 和所有技术一样，有一个观点，这个观点对批处理作业不是特别友好。

与目前对批处理作业半心半意的支持不同，我们希望看到的一个选择是 Kubernetes 使其立场更加明确，并声明 Kubernetes 主要是为服务而设计的。这可以为更专门为批处理作业用例设计的其他平台打开空间。或者，由于 Kubernetes 已经是一个“[平台，可以用来构建平台](https://twitter.com/kelseyhightower/status/935252923721793536)”，在某些方面，我们可以看到类似 Spark-on-Kubernetes 这样的东西得到更好的支持。Kubernetes 似乎不太可能采用一种明显不同的整体哲学，在这种哲学中，批处理作业是一个一流的用例。

```
*Star us on* [*Github*](https://github.com/meadowdata/meadowrun) *or follow us on* [*Twitter*](https://twitter.com/kurt2001?s=21&t=66yV7Xy4agKRFj4dOaLLew)*! We’re working on* [*Meadowrun*](https://meadowrun.io/) *which is an open-source library that solves some of these problems and makes it easier to run your Python code on the cloud,* [*with*](https://docs.meadowrun.io/en/stable/how_to/kubernetes/) *or without Kubernetes.*
```