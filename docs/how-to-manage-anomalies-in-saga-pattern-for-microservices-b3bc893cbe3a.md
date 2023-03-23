# 如何管理微服务 Saga 模式中的异常

> 原文：<https://betterprogramming.pub/how-to-manage-anomalies-in-saga-pattern-for-microservices-b3bc893cbe3a>

## 跨不同平台提供一致性

![](img/0482d9858d06c8355c6b339cb7a5cbaf.png)

由[法尔扎德·纳兹菲](https://unsplash.com/@euwars?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

Saga 是确保跨服务数据一致性的最常见模式。它将一个全局事务分成多个子事务。如果所有子事务操作都成功，则全局事务成功，如果重试后仍有一个子事务失败，则所有执行的子事务都会得到补偿。

上面的过程简单明了，但是在分布式环境中，可能会出现一些异常。确保 Saga 交易正常运行并保持数据不一致并不是一件容易的事情。我们将讨论这里可能出现的各种异常以及如何管理它们。

出于讨论的目的，我们将 Saga 中的分支交易的远期交易称为`Action`，补偿交易称为`Compensation`。

# 三种异常情况

通常情况下，如果发生回滚，Saga 事务中的`Action`会在`Compensation`之前执行。但是由于网络延迟或进程暂停，可能会先执行`Compensation`，然后再执行`Action`，或者根本不执行。

这个场景在分布式事务中引入了两个异常:

1.  空补偿:当`Compensation`正在执行时，对应的`Action`还没有执行，所以`Compensation`需要确定`Action`还没有执行，忽略业务数据更新，直接返回。
2.  挂起动作:当`Action`正在执行时，`Compensation`已经执行，所以`Action`需要确定`Compensation`已经执行，忽略业务数据更新，直接返回

分布式事务中需要处理的另一个常见异常是`Duplicated Requests`。当进程崩溃并且 Saga 重试服务时，服务可能被多次调用，因此需要幂等处理。

这三种类型的异常需要小心管理；否则，可能会出现数据不一致并中断业务。

# 解决办法

https://github.com/dtm-labs/dtm 开创了一种同时管理这三种异常的技术，名为 T0。对于 Saga 模式，原则如下:

1.  在本地数据库中创建一个表`dtm_barrier.barrier`，其惟一索引为`gid-branchid-branchop`
2.  开始本地事务
3.  在`Action`和`Compensation`中，插入忽略一行`gid-branchid-action|compensation`，如果受影响的行数为 0(如果是重复请求，挂起动作)，直接提交并返回
4.  在`Compensation`中，插入忽略一个额外的行`gid-branchid-action`，如果受影响的行数为 1(在空补偿的情况下)，直接提交并返回
5.  执行业务逻辑并提交，或者在出现错误时回滚

# 重复的请求

由于上面的惟一索引，重复请求中的插入肯定会被忽略，业务处理会被跳过。

# 零补偿

当出现空补偿时，步骤 3 的成功插入和步骤 4 的忽略插入将直接提交并返回，跳过业务逻辑。

# 悬挂动作

当`Hanging Action`发生时，已经执行了补偿，已经插入了一行`gid-branchid-action`，所以在`Action`中，它会发现第 3 步的插入被忽略，然后直接返回，跳过了业务逻辑。

# 行动和补偿重叠

如果`Action`和`Compensation`执行时间重叠，`Compensation`和`Action`都会插入同一行`gid-branchid-action`。由于唯一索引冲突，两个操作中只有一个会成功，而另一个将等待持有锁的事务完成，然后返回。

*   场景一:`Action`失败，`Compensation`成功插入`gid-branchid-action`。这是典型的`Null Compensation`和`Hanging Action`场景，`Action`和`Compensation`都将忽略业务逻辑，直接根据上述算法返回
*   场景二:`Action`成功，`Compensation`插入`gid-branchid-action`失败。交易执行顺序为`Action`在`Compensation`之前，无异常发生。根据上述算法，`Action`和`Compensation`中的业务将依次执行
*   场景 3，如果数据库在重叠期间关闭，将重试操作，并最终转到场景 1 或 2。

综上所述，`Sub-transaction Barrier`能够管理异常并确保数据一致性。

上述算法也适用于 TCC 分布式事务。即使您使用工作流引擎(如 Candence 或 Camunda)来处理 Saga 事务，该算法也同样适用。

# 密码

上述`Sub-transaction Barrier`技术，当与分布式事务框架[https://github.com/dtm-labs/dtm](https://github.com/dtm-labs/dtm)结合使用时，已经可以在几种语言[SDK](https://en.dtm.pub/ref/sdk)中使用，下面是 Go 中的示例代码。

在`TransOut`服务中，第一行是从 HTTP 请求创建一个屏障，后面几行调用屏障内部的业务。

当我们把业务处理放在一个 subtransaction barrier 里面，前面描述的各种异常都被过滤掉了，开发者只需要考虑正常流程下的业务处理。

完整的例子可以在这里找到:[https://github.com/dtm-labs/dtm-examples](https://github.com/dtm-labs/dtm-examples)。在`dtm`设置之后，您可以通过以下命令运行一个完整的示例:
`go run main.go http_saga_barrier`

# 结论

本文提出了一种算法来处理微服务的 Saga 模式中的异常。它优雅而高效，可以应用于 TCC 模式和许多其他工作流。

欢迎你来参观 https://github.com/dtm-labs/dtm。这是一个开源项目，致力于简化分布式事务的开发。