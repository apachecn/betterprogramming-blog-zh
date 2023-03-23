# 如何在 MySql 中创建超级用户

> 原文：<https://betterprogramming.pub/how-to-create-a-superuser-in-mysql-1a300ef6ffba>

## 成为超级用户的通行证

![](img/7734246d759afffda819bfd7cd704f05.png)

照片由[因戈·斯蒂勒](https://unsplash.com/@ingo231177?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

MySQL 中的超级用户是一个拥有完全访问权限的用户，授予对 MySQL 提供的所有管理权限的超级用户权限。

# MySQL 权限

MySQL 为帐户提供特权，以确定帐户可以执行的操作。它们是:

*   **管理权限**使用户能够管理 MySQL 服务器的操作。这些权限是全局的，并不特定于数据库。
*   **数据库特权**适用于数据库和其中的所有对象。这些权限可以授予特定数据库，也可以应用于所有数据库。
*   **数据库对象**如表、索引、视图和存储例程的权限可以授予数据库中的特定对象、数据库中给定类型的所有对象或所有数据库中给定类型的所有对象。

# 创建具有所有权限的超级用户

所以，让我们继续创建我们的超级用户。

## 注册

首先，你必须以根用户的身份登录。如果您使用的是 MySQL Workbench，登录过程非常简单。否则，您可以在命令行中使用以下代码。

```
mysql -u root -p <your_password>
```

## 创建新用户

在这里，我们创建了一个用户，我们希望使其成为超级用户。

```
CREATE USER ‘<user_name>’@’localhost' IDENTIFIED BY ‘<password>'
```

## 授予特权

目前，用户对任何数据库都没有权限。在那里你需要授予他们特权。MySQL 提供了以下基本的数据库特权:

*   **所有权限:**对数据库的完全根访问权限。如果没有指定数据库，它可以在整个系统中进行全局访问。
*   **创建**:创建新的表格或数据库
*   **删除:**删除表格或数据库
*   **删除:**从表格中删除行
*   **插入:**将行插入表格
*   **选择:**使用选择命令读取数据库
*   **更新:**更新表格行
*   **授予选项** —授予或取消其他用户的权限

有关所有 MySQL 特权的详细列表，请访问 MySQL 官方文档。

## 成为超级用户

由于我们正在创建一个超级用户，我们将授予我们的用户所有特权，这是`**GRANT ALL PRIVILEGES**` 选项。

```
GRANT ALL PRIVILEGES ON *.* TO ‘<user_name>’@’localhost' WITH GRANT OPTION;
```

我们的用户现在拥有类似 root 的权限，拥有所有特权。

## 为相同的用户名创建另一个帐户

我们现在将为相同的用户名创建另一个帐户。

但是这一次，我们为主机提供了一个通配符`‘%’`，允许任何主机连接到它。

这是因为我们用`‘localhost’`创建的用户只能通过本地主机访问。

```
CREATE USER '<user_name>'@'%' IDENTIFIED BY '<password>';
```

现在，让我们授予通配符用户超级用户权限。

```
GRANT ALL PRIVILEGES ON *.* TO '<user_name>'@'%' WITH GRANT OPTION;
```

## 验证用户权限

现在我们已经创建了我们的超级用户，让我们验证我们的用户是否有他的特权。为此，我们使用`SHOW GRANTS`命令。

```
SHOW GRANTS FOR <user_name>;
```

一旦我们确认我们的用户拥有所有权限，让我们重新加载它们。

## 重新加载所有权限

为了重新加载所有特权，我们在查询中使用了`**FLUSH PRIVILEGES**`命令。

```
FLUSH PRIVILEGES;
```

# 结论

本教程主要关注于向 MySQL 用户授予超级用户权限。关于 MySQL 用户账户管理的更多信息，请参考 MySQL [官方文档](https://dev.mysql.com/doc/refman/8.0/en/creating-accounts.html)。

分享知识是获得知识的唯一途径。对于那些可能从这个故事中受益的人，请随意添加并通过评论做出贡献。

玩得开心！享受编码！