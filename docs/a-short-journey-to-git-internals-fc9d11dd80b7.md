# Git 是如何工作的

> 原文：<https://betterprogramming.pub/a-short-journey-to-git-internals-fc9d11dd80b7>

## 看看 Git 的内部结构，理解添加、提交、推送等操作时会发生什么

![](img/777800305fa77553984603e7e5723631.png)

照片由[注意到](https://unsplash.com/@notethanun)上的 [Unsplash](https://unsplash.com/) 。

在本文中，我们将通过一个真实的例子深入 Git 内部。如果你还没有打开你的候机厅，那就打开吧，系好安全带，我们走吧！

# 初始化 Git 存储库

您可能已经使用`git init`初始化了一个空的 Git 项目，但是您有没有想过这个命令是做什么的？

让我们创建一个空文件夹并初始化一个空的 Git 项目。这是官方 [Git 文档](https://git-scm.com/docs/git-init)对`git init`的描述:

> 这个命令创建一个空的 Git 存储库——基本上是一个包含子目录`objects`、`refs/heads`、`refs/tags`和模板文件的`.git`目录。还会创建一个引用主分支的头的初始`HEAD`文件。

如果我们检查文件夹的内容，我们会看到以下结构:

```
$ tree -L 1 .git/
.git/
├── HEAD
├── config
├── description
├── hooks
├── info
├── objects
└── refs
```

我们稍后将讨论一些对象。

# Git 是一个键值存储

在其核心，Git 是一个*内容寻址的*文件系统。啊？好吧，Git 只是一个键值数据库。您可以向 Git 存储库中插入任何类型的内容，Git 会返回一个惟一的标识符(一个键),您可以用它来检索内容。

Git 使用`[hash-object](https://git-scm.com/docs/git-hash-object)`命令将值存储到数据库中:

> 用命名文件(可以在工作树之外)的内容计算指定类型的对象的对象 ID 值，并可选择将结果对象写入对象数据库。向其标准输出报告其对象 ID。未指定<type>时，默认为‘blob’"</type>

“blob”只不过是一个字节序列。Git blob 包含作为文件的确切数据，但是它存储在 Git 键值数据存储中，而“实际的”文件存储在文件系统中。

让我们创建一个 blob:

```
$ echo hello | git hash-object --stdin -w
ce013625030ba8dba906f756967f9e9ca394464a
```

我们使用`-w`标志实际上*将*对象写入对象数据库，而不仅仅是显示它(通过`--stdin`标志实现)。

值“hello”是 Git 数据存储中的“值”,从`hash-object`函数返回的散列是我们的键。我们现在可以做相反的操作，使用`[git-cat-file](https://git-scm.com/docs/git-cat-file)`命令通过键值读取值:

```
$ git cat-file -p ce013625030ba8dba906f756967f9e9ca394464a
hello
```

我们可以使用`-t`标志来检查它的类型:

```
$ git cat-file -t ce013625030ba8dba906f756967f9e9ca394464a
blob
```

`git hash-object`将数据存储在`.git/objects/`文件夹中(又名对象数据库)。让我们验证一下:

```
$ tree .git/objects/
.git/objects/
├── ce
│   └── 013625030ba8dba906f756967f9e9ca394464a
├── info
└── pack
```

散列后缀(在“ce”目录下)与我们从`hash-object`函数中得到的相同，但是它有一个不同的前缀。为什么？这是因为父文件夹名包含我们的密钥的前两个字符。为什么？因为一些文件系统对可能的子目录数量有限制。所以引入这一层可以缓解这个问题。

让我们保存另一个对象:

```
$ echo world | git hash-object --stdin -w
cc628ccd10742baea8241c5924df992b5c019f71
```

正如所料，我们现在在`.git/objects/`下有两个目录:

```
$ tree .git/objects/
.git/objects/
├── cc
│   └── 628ccd10742baea8241c5924df992b5c019f71
├── ce
│   └── 013625030ba8dba906f756967f9e9ca394464a
├── info
└── pack
```

同样，包含密钥前缀的`cc`文件夹在包含的文件名中包含密钥的其余部分。

# 树对象

我们要研究的下一个 Git 对象是树。这种类型解决了文件名的存储问题，允许将一组文件存储在一起。

树对象包含条目。每个条目都是 blob 或子树的 SHA-1 及其相关的模式、类型和文件名。让我们检查一下文档中对`[git-mktree](https://git-scm.com/docs/git-mktree)`的定义:

> "读取非递归`ls-tree`输出格式的标准输入，并创建一个树对象。mktree 规范了树条目的顺序，因此不需要对输入进行预先排序。构建的树对象的对象名被写入标准输出。

如果你想知道`[ls-tree](https://git-scm.com/docs/git-ls-tree)`的输出格式，它看起来像这样:

```
<mode> SP <type> SP <object> TAB <file>
```

现在让我们把上面的两个斑点联系起来:

```
$ printf '%s %s %s\t%s\n' \
    100644 blob ce013625030ba8dba906f756967f9e9ca394464a hello.txt \
    100644 blob cc628ccd10742baea8241c5924df992b5c019f71 world.txt |
  git mktree
88e38705fdbd3608cddbe904b67c731f3234c45b
```

`mktree`返回新创建的树对象的键。

此时，我们可以将我们的树想象如下:

```
 88e38705fdbd3608cddbe904b67c731f3234c45b  
                                |                   
                  +-------------|------------+             
                  |                          |        
                  |                          |        
                  |                          |          
                  |                          |       
                  |                          |
                hello                       world        
            ce013625030b                 cc628ccd1074
```

让我们来看看树的内容:

```
$ git cat-file -p 88e38705fdbd3608cddbe904b67c731f3234c45b
100644 blob ce013625030ba8dba906f756967f9e9ca394464a hello.txt
100644 blob cc628ccd10742baea8241c5924df992b5c019f71 world.txt
```

当然，相应地更新了`.git/objects`:

```
$ tree .git/objects/
.git/objects/
├── 88
│   └── e38705fdbd3608cddbe904b67c731f3234c45b
├── cc
│   └── 628ccd10742baea8241c5924df992b5c019f71
├── ce
│   └── 013625030ba8dba906f756967f9e9ca394464a
├── info
└── pack
```

到目前为止，我们还没有更新我们的索引。为此，我们使用`[git-read-tree](https://git-scm.com/docs/git-read-tree)`命令:

> 将由<tree-ish>给出的树信息读入索引，但实际上**不会更新**它缓存的任何文件。(参见: [git-checkout-index[1]](https://git-scm.com/docs/git-checkout-index) )”</tree-ish>

```
$ git read-tree 88e38705fdbd3608cddbe904b67c731f3234c45b
$ git ls-files -s
100644 ce013625030ba8dba906f756967f9e9ca394464a 0 hello.txt
100644 cc628ccd10742baea8241c5924df992b5c019f71 0 world.txt
```

但是，请注意，我们的*文件系统*中仍然没有这些文件，因为我们正在将值直接写入 Git 数据存储。为了“检出”文件，我们将使用`git-checkout-index`命令将文件从索引复制到*工作树*:

```
git checkout-index 0 -a
```

`-a`代表“所有”现在我们应该可以看到我们的文件了:

```
$ ls
hello.txt world.txt
$ cat hello.txt
hello
$ cat world.txt
world
```

# 奖金

`git-hash-object`输出与`openssl` SHA-1 不同的 SHA。为什么？这是因为 Git 使用下面的公式进行 [SHA-1 计算](https://stackoverflow.com/questions/552659/how-to-assign-a-git-sha1s-to-a-file-without-git):

```
sha1("blob " + filesize + "\0" + data)
```

所以为了得到同样的 SHA-1，我们需要做下面的[小把戏](https://stackoverflow.com/questions/5290444/why-does-git-hash-object-return-a-different-hash-than-openssl-sha1):

```
# without outputing a newline
$ echo -n hello | git hash-object --stdin -w
b6fc4c620b67d95f953a5c1c1230aaab5db5a1b0
$ printf 'blob 5\0hello' | openssl sha1
b6fc4c620b67d95f953a5c1c1230aaab5db5a1b0
```

# 摘要

在本文中，我们将两个文件直接存储到 Git 数据存储中。这些文件对我们的本地文件系统还不可见。我们已经创建了一棵树，并将两个“blobs”关联起来，然后我们使用`git-checkout-index`命令将文件放到我们的工作目录中。