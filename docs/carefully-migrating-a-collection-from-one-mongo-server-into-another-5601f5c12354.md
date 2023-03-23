# 如何小心地将集合从一台 Mongo 服务器迁移到另一台服务器上

> 原文：<https://betterprogramming.pub/carefully-migrating-a-collection-from-one-mongo-server-into-another-5601f5c12354>

## 使用`mongodump`和`mongorestore`

![](img/0a8e1bb20ad27b28983fdcff5f5bceff.png)

照片由 [Dino Reichmuth](https://unsplash.com/@dinoreichmuth?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

这里的练习是将 Mongo 集合的记录从一个 Mongo 服务器移动到另一个服务器。这方面的一个用例是将集合或集合的一部分从生产环境复制到开发环境中。

# 我们的设置

在我们的示例中，我们将有两台远程托管的数据库服务器。我们将使用连接字符串来访问它们。每个数据库服务器都有相同的数据库和集合。

## Mongo 服务器 A

```
Connection string: *mongodb+srv://user_a:password_a@database_a.mongodb.net/db_1?ssl=true&authSource=admin*Databases: *db_1*, *db_2*
Collections: *collection_a*, *collection_b*, *collection_c*
```

我们会说所有的收藏都在`db1`，除了`collection_c`在`db2`。

## Mongo 服务器 B

服务器 B 与服务器 A 相同，但具有不同的连接字符串，即与 A 相同，除了:

```
Connection string: *mongodb+srv://user_a:password_a@database_b.mongodb.net/db_1?ssl=true&authSource=admin*
```

现在我们知道了数据库的设置，我们可以开始迁移集合了。让我们将数据从服务器 A 的`mongodb+srv://user_a:password_a@database_a.mongodb.net/db_1?ssl=true&authSource=admin` 迁移到服务器 B 的*。*

# 将源数据获取到您的本地机器

我们的第一步将是从服务器 A 下载`collection_b`到我们的本地机器。您需要在本地机器上安装 Mongo，包括我们将使用的 CLI 工具:`mongodump`和`mongorestore`。

要从远程服务器 A 下载，我们将使用的终端命令是:

```
mongodump --uri="*mongodb+srv://user_a:password_a@database_a.mongodb.net/db_1?ssl=true&authSource=admin*" --collection="*collection_b*"
```

关于该命令的详细信息:

*   这是一个允许我们从 Mongo 服务器获取数据的应用程序。
*   `--uri` —这是远程 Mongo 服务器的 Mongo 连接字符串。
*   `--collection` —这是我们希望从 Mongo 服务器本地获取的集合。如果关闭，将下载整个服务器，以及所有数据库中的所有集合。

当执行时，您应该得到类似这样的结果:

```
2020-03-18T21:57:28.591-0700 writing db_1.collection_b to 
2020-03-18T21:57:29.460-0700 [........................]  db_1.collection_b  101/21924  (0.5%)
2020-03-18T21:57:38.463-0700 [#######.................]  db_1.collection_b  7065/21924  (32.2%)
2020-03-18T21:57:47.464-0700 [#########...............]  db_1.collection_b  10932/21924  (49.9%)
2020-03-18T21:58:05.463-0700 [###########.............]  db_1.collection_b  12264/21924  (55.9%)
2020-03-18T21:58:17.461-0700 [##############..........]  db_1.collection_b  15534/21924  (70.9%)
2020-03-18T21:58:38.458-0700 [#################.......]  db_1.collection_b  20074/21924  (91.6%)
2020-03-18T21:59:04.758-0700 [########################]  db_1.collection_b  21924/21924  (100.0%)
2020-03-18T21:59:04.761-0700 done dumping db_1.collection_b (21924 documents)
```

从输出中，我们可以看到集合中记录的总数。在这种情况下，有 21，924 个项目。

如果我们查看当前目录，我们可以看到备份是在`./dump`目录中创建的:

```
$ ls
dump
```

在`./dump`的探索中，我们可以看到如下的文件结构:

```
$ tree
.
└── dump
    └── db_1
        ├── collection_b.bson
        └── collection_b.metadata.json
```

我们可以看到，转储目录包含一个用于数据库`db_1`的目录，其中有两个用于我们的`collection_b` *的文件。*第一个文件*、*、`collection_b.bson`、*、*包含集合的记录，而第二个文件`collection_b.metadata.json`包含关于集合本身的数据。

成功到此为止！我们能够从一个源 Mongo 服务器 Server A 下载一个集合。下一步将是把数据上传到我们的目标 Mongo 服务器 Server B。

# 将本地数据推送到目标 Mongo 服务器

为了将本地备份上传到我们的目标远程 Mongo 服务器，即服务器 B，我们将使用以下命令:

```
mongorestore --uri="mongodb+srv://user_a:password_a@database_b.mongodb.net/db_1?ssl=true&authSource=admin" --nsInclude="db_1.collection_b" ./dump
```

关于该命令的详细信息:

*   `mongorestore` —这个应用程序允许我们将数据从本地备份推送到 Mongo 服务器。
*   `--uri` —这是远程 Mongo 服务器的 Mongo 连接字符串。
*   `--nsInclude` —此标志帮助我们限制将备份中的哪些集合推送到远程服务器。例如，如果我们的备份包含许多集合，我们可以将单个集合作为目标。我们使用“点”符号来指定集合所在的数据库，后面是集合本身。在示例中，它是这样的:`db_1.collection_b`。

该命令将从本地备份中获取记录，并将它们推送到远程 Mongo 服务器。如果远程服务器中存在具有相同 id 的记录，它将不会被覆盖。如果备份中有您想要替换的记录，您必须首先从目标远程服务器中删除它们。

在执行时，输出如下所示:

```
...
2020-03-18T23:47:12.032-0700 [#######################.]  db_1.collection_b  982MB/990MB  (99.2%)
2020-03-18T23:47:12.471-0700 continuing through error: E11000 duplicate key error collection: db_1.collection_b index: _id_ dup key: { : ObjectId('5e46cdc62aa12a0026a678d1') }
2020-03-18T23:47:15.031-0700 [########################]  db_1.collection_b  990MB/990MB  (100.0%)
2020-03-18T23:47:42.594-0700 restoring indexes for collection db_1.collection_b from metadata
2020-03-18T23:47:42.673-0700 finished restoring db_1.collection_b (219233 documents, 1 failures)
2020-03-18T23:47:42.674-0700 219233 document(s) restored successfully. 1 document(s) failed to restore.
```

我们可以看到，恢复了 219，233 个文档，有一个文档失败。在这种情况下，失败是意料之中的，因为在目标 Mongo 服务器的集合中存在一个具有相同 id 的记录。我们可以看到误差表示为:

```
2020-03-18T23:47:12.471-0700 continuing through error: E11000 duplicate key error collection: db_1.collection_b index: _id_ dup key: { : ObjectId('5e46cdc62aa12a0026a678d1') }
```

“重复键错误”向我们表明该记录已经存在，不会被覆盖。

在检查目标 Mongo 服务器 *Server B* 时，我们看到记录已经在`collection_b`集合中创建，该集合驻留在数据库`db_1` *中。*成功了！我们已经成功地将一个集合的数据从源 Mongo 服务器迁移到目标服务器。为了启动，我们非常小心，因为没有覆盖目标服务器集合中的现有记录。

我希望这有所帮助！帕特里克

# 资源

*   [本地安装 Mongo 的文档](https://docs.mongodb.com/manual/installation)
*   【mongodump 的文档
*   【mongorestore 的文档