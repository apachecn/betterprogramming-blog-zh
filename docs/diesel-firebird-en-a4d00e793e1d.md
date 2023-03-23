# 用柴油和铁锈接近火鸟

> 原文：<https://betterprogramming.pub/diesel-firebird-en-a4d00e793e1d>

## 火鸟带锈怎么用

![](img/2c2845495e1c05260ccfa9e28d7895ad.png)

我们最近推出了`[rsfbclient-diesel](https://crates.io/crates/rsfbclient-diesel)`适配器，它使 [diesel](https://crates.io/crates/diesel) 能够与 [Firebird](https://firebirdsql.org) 数据库一起工作。

这篇文章是我上一篇文章[的延续，在那篇文章中我展示了如何用 Rust 来使用 Firebird。现在让我们学习如何用 ORM 层来改进这种用法。](https://itnext.io/firebird-rust-92e9043261cc)

为了便于理解，我们将使用示例数据库`employee.fdb`中的`job`表实现一个 CRUD CLI 工具。这个数据库是你的火鸟安装自带的，但是也可以在从[官方网站](https://firebirdsql.org/en/firebird-4-0/)提取的`examples/empbuild`文件夹中找到。

# 开始我们的项目

我们需要开始在我们的`Cargo.toml`文件中包含 [diesel](https://crates.io/crates/diesel) 和`[rsfbclient-diesel](https://crates.io/crates/rsfbclient-diesel)`依赖项:

现在在`main.rs`中，我们导入所有内容并连接到`employee.fdb`数据库:

# 列出行

为了用 diesel 操作一些表，我们需要声明这个表的字段和结构。在新模块`schema.rs`中，我们将声明我们的`job`表:

`Job`结构将是我们在`job`表中的记录表示。因为表格中的某些字段使用了`job_`前缀，所以我们需要使用`#[diesel(column_name = …)]`。下面我们声明我们的表模式，提供列的类型。

我们的结构声明了`#[derive(Queryable)]`。通过这种推导，我们能够执行读操作。

再次回到`main.rs`。我们导入新模块，并使用 diesel `load`函数获取我们的记录列表:

# 改进我们的记录列表

我们有一个列表，现在让我们改进视图和调用它的方法。为此，我们需要在`Cargo.toml`文件中添加[表格](https://crates.io/crates/tabled)和[隐语](https://crates.io/crates/argopt)。

我们正在构建一个 CRUD，让我们在`argopt`的帮助下将列表操作转移到一个名为`list`的命令中。

在`main.rs`中创建`list`函数，移动当前`main`函数的全部内容并用`#[subcmd]`标记:

为了使命令可用，我们需要在`main`函数上添加`#[cmd_group]`派生。

当我们测试时，我们会看到我们的项目列出了所有可用的命令:

```
‹dieselexample› cargo run
……
SUBCOMMANDS:
 help Print this message or the help of the given subcommand(s)
 list List all available jobs
```

现在我们只需要使用`tabled`格式化我们的列表。在这个项目中，我们将直接使用 schema struct 和`tabled`来简化我们的实现。

在我们的`Job`结构定义中，我们添加了`Tabled`派生:

在我们的`list`命令中，我们通过调用`tabled`改变了打印记录的方式:

当我们测试时，我们将看到我们的格式化列表:

```
‹dieselexample› cargo run list
+-------+--------------------------+-------------+
| code  | title                    | country     |
+-------+--------------------------+-------------+
| CEO   | Chief Executive Officer  | USA         |
+-------+--------------------------+-------------+
| CFO   | Chief Financial Officer  | USA         |
+-------+--------------------------+-------------+
| VP    | Vice President           | USA         |
+-------+--------------------------+-------------+
| Dir   | Director                 | USA         |
+-------+--------------------------+-------------+
| Mngr  | Manager                  | USA         |
```

# 插入新记录

在插入新记录之前，我们需要完成列映射。一些非空列仍然存在:

有了新的列，我们创建了`add`函数，并把它放在带有`#[cmd_groud]`的可用命令列表中:

当我们测试时，我们将看到我们的新命令:

```
‹dieselexample› cargo run add 
USAGE:
 employee add —code <CODE> —title <TITLE> —grade <GRADE> —country <COUNTRY> —min-salary <MIN_SALARY> —max-salary <MAX_SALARY>
```

现在我们去插页。要为插入操作启用一些结构，我们需要添加`Insertable` derive:

准备好结构后，我们只需要调用`insert_into` diesel 函数:

让我们测试一下:

```
‹dieselexample› cargo run add --code TE --title "Tech solver stuffs" --grade 1 --country USA --min-salary 2000 --max-salary 5000 
‹dieselexample› cargo run list
+-------+-------------+---------+-------+------------+------------+
| code  | title       | country | grade | min_salary | max_salary |
+-------+-------------+---------+-------+------------+------------+
....
| TE    | Tech solver | USA     | 1     | 2000       | 5000       |
+-------+-------------+---------+-------+------------+------------+
```

# 更新时间到了

我们已经有了列表和添加操作，仍然保持更新。我们从创建`update`命令开始:

我们将在可编辑字段上使用`Option`类型。使用这种方法，用户只需提供他真正想要更新的内容:

```
<dieselexample› cargo run update —help
USAGE:
    employee update [OPTIONS] <CODE>
ARGS:
    <CODE>    Job code
OPTIONS:
    --max-salary <MAX_SALARY>    New max salary
    --min-salary <MIN_SALARY>    New min salary
    --title <TITLE>              New title
```

因为我们的目的只是更新一些字段，所以让我们用代表这些字段的`AsChangeset`派生类创建一个新的结构:

在我们的命令中再次出现，现在我们只需要调用`update` diesel 函数:

让我们测试一下:

```
‹dieselexample› cargo run update TE —title=”New job title”
‹dieselexample› cargo run list
+-------+--------------+---------+-------+------------+------------+
| code | title         | country | grade | min_salary | max_salary |
+------+---------------+---------+-------+------------+------------+
....
| TE   | New job title | USA     | 1     | 2000       | 5000       |
+------+---------------+---------+-------+------------+------------+
```

## 删除记录

最后，现在让我们删除我们的记录。为此，我们创建了`remove` 命令:

正如所料，删除是通过调用`delete` diesel 函数来执行的:

让我们测试一下:

```
‹dieselexample› cargo run remove TE
‹dieselexample› cargo run list
+------+---------------+---------+-------+-----------+-------------+
| code | title         | country | grade | min_salary | max_salary |
+------+---------------+---------+-------+-----------+-------------+
....
| SRep | Sales Represe | France  | 4     | 20000     | 100000      |
+------+---------------+---------+-------+-----------+-------------+
```

# 改善连接

如您所见，字符串连接是硬编码的。我们可以用一个简单的方法来解决这个问题，使用环境变量。

为了管理 rust 中的环境变量，我们使用了`std:env`模块:

有了导入的模块，我们只需要从`DIESELFDB_CONN` env:

让我们测试一下:

```
‹dieselexample› export DIESELFDB_CONN=”firebird://SYA:ma@localhost/employee.fdb”
‹dieselexample› cargo run list
+------+---------------+---------+-------+-----------+-------------+
| code | title         | country | grade | min_salary | max_salary |
+------+---------------+---------+-------+-----------+-------------+
....
| SRep | Sales Represe | France  | 4     | 20000     | 100000      |
+------+---------------+---------+-------+-----------+-------------+
```

下面是示例项目的源代码:

[](https://github.com/fernandobatels/rsfbclient/tree/master/rsfbclient-diesel/examples/employee) [## rsfb client/rsfb client-diesel/示例/master fernandobatels/rsfb client 的员工

### 铁锈火鸟客户端。在 GitHub 上创建一个帐户，为 fernandobatels/rsfbclient 的开发做出贡献。

github.com](https://github.com/fernandobatels/rsfbclient/tree/master/rsfbclient-diesel/examples/employee) 

为了跟进`rsfbclient`和`rsfbclient_diesel`的开发，你可以访问我们的库:[https://github.com/fernandobatels/rsfbclient](https://github.com/fernandobatels/rsfbclient)

感谢您的关注，下次再见！