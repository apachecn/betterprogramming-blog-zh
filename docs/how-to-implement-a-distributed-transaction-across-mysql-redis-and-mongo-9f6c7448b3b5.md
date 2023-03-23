# 如何实现跨 Mysql、Redis 和 Mongo 的分布式事务

> 原文：<https://betterprogramming.pub/how-to-implement-a-distributed-transaction-across-mysql-redis-and-mongo-9f6c7448b3b5>

## 组合多个存储引擎以形成分布式事务

![](img/bb2be33724e49e6494dde8cff9eb2186.png)

简·安东宁·科拉尔在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

Mysql、Redis、Mongo 都是很受欢迎的商店，各有各的优势。在实际应用中，同时使用多个存储是很常见的，确保跨多个存储的数据一致性成为一个需求。

本文给出了一个跨多个存储引擎实现分布式事务的例子:Mysql、Redis 和 Mongo。这个例子是基于 https://github.com/dtm-labs/dtm[的分布式事务框架](https://github.com/dtm-labs/dtm)的，希望能帮助你解决跨微服务的数据一致性问题。

灵活组合多个存储引擎形成一个分布式事务的能力是由 DTM 首先提出的，没有其他分布式事务框架像这样陈述过这种能力。

# 我们试图解决什么问题？

假设一个用户现在正在参加一个促销活动:他们有余额，充值话费，促销会赠送商城积分。余额存储在 Mysql，账单存储在 Redis，商城积分存储在 Mongo。因为提升有时间限制，所以有参与可能失败的可能，所以需要回滚支持。

对于上述问题场景，您可以使用 DTM 的 Saga 事务，我们将在下面详细解释解决方案。

# 准备数据

第一步是准备数据。为了方便用户快速上手示例，我们在 [en.dtm.pub](https://en.dtm.pub/) 准备了所有数据，包括 Mysql、Redis、Mongo，具体连接用户名和密码可以在 [dtm-labs/dtm-examples](https://github.com/dtm-labs/dtm-examples) 找到。

> 如果想自己在本地准备数据环境，可以使用[DTM-labs/DTM/blob/main/helper/compose . store . yml](https://github.com/dtm-labs/dtm/blob/main/helper/compose.store.yml)启动 Mysql、Redis、Mongo 然后执行[dtm-labs/DTM/tree/main/sqls](https://github.com/dtm-labs/dtm/tree/main/sqls)中的脚本，为这个例子准备数据，其中`busi.*`是业务数据，`barrier.*`是 DTM 使用的辅助表

# 编写业务代码

让我们从最熟悉的商店引擎 Mysql 的业务代码开始

> 以下代码是 Golang 语言。其他语言如 C#、PHP、Java 都可以在这里找到:【https://en.dtm.pub/ref/sdk.html SDKs】()

```
func SagaAdjustBalance(db dtmcli.DB, uid int, amount int) error {
  _, err := dtmimp.DBExec(db, "update dtm_busi.user_account set balance = balance + ? where user_id = ?" , amount, uid)
  return err
}
```

这段代码主要执行数据库中用户余额的调整。在我们的示例中，这部分代码不仅用于 Saga 的正向操作，还用于补偿操作，在补偿操作中，只需要为补偿传递一个负数。

对于 Redis 和 Mongo，业务代码的处理类似，只是增加或减少相应的余额。

# 如何保证幂等性

对于 Saga 事务模式，当我们在子事务服务中出现临时故障时，将重试失败的操作。这种失败可能发生在子事务提交之前或之后，因此子事务操作需要是幂等的。

DTM 提供了帮助表和帮助函数，帮助用户快速实现幂等。对于 Mysql，它会在业务数据库中创建一个辅助表`barrier`，当用户启动一个交易来调整余额时，它会先在`barrier`表中插入`Gid`。如果有重复行，那么插入会失败，然后跳过平衡调整，保证等幂。使用 helper 函数的代码如下:

```
app.POST(BusiAPI+"/SagaBTransIn", dtmutil.WrapHandler2(func(c *gin.Context) interface{} {
  return MustBarrierFromGin(c).Call(txGet(), func(tx *sql.Tx) error {
    return SagaAdjustBalance(tx, TransInUID, reqFrom(c).Amount, reqFrom(c).TransInResult)
  })
}))
```

Mongo 处理幂等性的方式和 Mysql 类似，我就不再赘述了。

Redis 对幂等性的处理与 Mysql 不同，主要是因为事务的原理不同。Redis 事务主要通过 Lua 的原子执行来保证。DTM 助手函数将通过 Lua 脚本调整平衡。在调整余额之前，它会在 Redis 中查询`Gid`。如果`Gid`存在，将跳过平衡调整；如果不是，它将记录`Gid`并执行平衡调整。用于 helper 函数的代码如下:

```
app.POST(BusiAPI+"/SagaRedisTransOut", dtmutil.WrapHandler2(func(c *gin.Context) interface{} {
  return MustBarrierFromGin(c).RedisCheckAdjustAmount(RedisGet(), GetRedisAccountKey(TransOutUID), -reqFrom(c).Amount, 7*86400)
}))
```

# 怎么做补偿

对于佐贺来说，也需要处理补偿操作，但补偿不是简单的反向调整，有很多陷阱需要注意。

一方面，补偿需要考虑幂等性，因为上一小节描述的失败和重试在补偿中也存在。

另一方面，补偿也需要考虑“零补偿”，因为 Saga 的正向操作可能返回失败，这可能发生在数据调整之前或之后。对于已经提交调整的故障，我们需要执行反向调整，但是对于尚未提交调整的故障，我们需要跳过反向调整。

在 DTM 提供的 helper 函数中，一方面会根据 forward 操作插入的 Gid 判断补偿是否为空补偿，另一方面会再次插入 Gid+'compensate '判断补偿是否为重复操作。如果有正常的补偿操作，则执行业务上的数据调整；如果存在空补偿或重复补偿，将跳过对该业务的调整。

Mysql 代码如下。

```
app.POST(BusiAPI+"/SagaBTransInCom", dtmutil.WrapHandler2(func(c *gin.Context) interface{} {
  return MustBarrierFromGin(c).Call(txGet(), func(tx *sql.Tx) error {
    return SagaAdjustBalance(tx, TransInUID, -reqFrom(c).Amount, "")
  })
}))
```

Redis 的代码如下。

```
app.POST(BusiAPI+"/SagaRedisTransOutCom", dtmutil.WrapHandler2(func(c *gin.Context) interface{} {
  return MustBarrierFromGin(c).RedisCheckAdjustAmount(RedisGet(), GetRedisAccountKey(TransOutUID), reqFrom(c).Amount, 7*86400)
}))
```

补偿服务代码与前一步操作的代码几乎相同，只是金额乘以-1。DTM 助手函数自动正确处理幂等和空补偿。

# 其他例外

在写向前操作和补偿操作的时候，其实还有一个例外叫做“暂停”。当超时或重试次数达到配置的限制时，全局事务将回滚。正常情况是在补偿之前执行正向操作，但是在“过程暂停”的情况下，补偿可以在正向操作之前执行。因此，forward 操作还需要确定补偿是否已经执行，如果已经执行，也需要跳过数据调整。

对于 DTM 用户来说，这些异常已经被优雅而恰当地处理了，作为一个用户，你只需要遵循上面描述的`MustBarrierFromGin(c).Call`调用，根本不需要关心它们。DTM 处理这些异常的原理在此详细描述:[异常和子交易障碍](https://dtm.pub/practice/barrier.html)

# 启动分布式事务

在编写了各个子交易服务之后，下面的代码启动了一个 Saga 全局交易。

```
saga := dtmcli.NewSaga(dtmutil.DefaultHTTPServer, dtmcli.MustGenGid(dtmutil.DefaultHTTPServer)).
  Add(busi.Busi+"/SagaBTransOut", busi.Busi+"/SagaBTransOutCom", &busi.TransReq{Amount: 50}).
  Add(busi.Busi+"/SagaMongoTransIn", busi.Busi+"/SagaMongoTransInCom", &busi.TransReq{Amount: 30}).
  Add(busi.Busi+"/SagaRedisTransIn", busi.Busi+"/SagaRedisTransOutIn", &busi.TransReq{Amount: 20})
err := saga.Submit()
```

在这部分代码中，创建了一个由 3 个子事务组成的 Saga 全局事务。

*   从 Mysql 转出 50
*   30 分钟后转移到 Mongo
*   20 分钟后转到 Redis

在整个事务中，如果所有子事务都成功完成，则全局事务成功；如果其中一个子事务返回业务失败，则全局事务回滚。

# 奔跑

如果你想运行一个以上的完整例子，步骤如下。

1.  运行 DTM

```
git clone https://github.com/dtm-labs/dtm && cd dtm
go run main.go
```

1.  运行一个成功的例子

```
git clone https://github.com/dtm-labs/dtm-examples && cd dtm-examples
go run main.go http_saga_multidb
```

1.  运行一个失败的例子

```
git clone https://github.com/dtm-labs/dtm-examples && cd dtm-examples
go run main.go http_saga_multidb_rollback
```

您可以修改该示例，以模拟各种临时故障、空补偿情况和各种其他异常，在这些情况下，当整个全局事务完成时，数据是一致的。

# 摘要

本文给出了一个跨 Mysql、Redis 和 Mongo 的分布式事务的例子。它详细描述了需要处理的问题以及解决方案。

本文中的原则适用于所有支持 ACID 事务的存储引擎，您可以很快将其扩展到其他引擎，比如 TiKV。

欢迎参观 github.com/dtm-labs/dtm。这是一个致力于使微服务中的分布式事务更容易的项目。它支持多种语言和多种模式，如两阶段消息、Saga、Tcc 和 Xa。