# 使用 Python 将文件从 FTP 服务器传输到 AWS S3 存储桶

> 原文：<https://betterprogramming.pub/transfer-file-from-ftp-server-to-a-s3-bucket-using-python-7f9e51f44e35>

## 在 paramiko 和 boto3 模块的帮助下实现文件传输功能

![](img/d2ead0c39f11d31afcb68773299ab14e.png)

图片来自 unspalsh。演职员表[@ iamrcup](https://unsplash.com/@iammrcup?utm_medium=referral&utm_campaign=photographer-credit&utm_content=creditBadge)

大家好。在本文中，我们将使用 **paramiko** 和 **boto3** 模块在 python 中实现文件传输(从 FTP 服务器到 amazon s3)功能。

# 先决条件

*   Python (3.6.x)
*   AWS S3 存储桶访问
*   FTP 服务器访问

# **Python 库**

*   帕拉米科
*   boto3

> 注意:你不需要熟悉上面的 python 库来理解这篇文章，但是要确保你有访问 AWS S3 桶和 FTP 服务器的凭证。我们将一步一步地学习 python 函数，我会在文章底部留下一个 github 链接。

# **步骤 1:初始设置**

使用 pip install 安装上述所有软件包:

```
pip install paramiko boto3
```

另外，在您的机器上安装 awscli 并配置访问 id、密钥和区域。下面是关于如何做的[链接](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html#cli-quick-configuration)。

# **第二步:打开 FTP 连接**

让我们来看一下与服务器建立 FTP 连接的函数。

我们将使用 paramiko 的 SSHClient 类创建一个新的 SSH 会话。我们需要为会话加载本地系统密钥。对于 ssh 上的 FTP 传输，我们需要指定服务器主机名`ftp_host`和端口`ftp_port`。一旦建立了连接，我们就使用`transport.connect()`认证 FTP 服务器来打开新的 FTP 连接。如果认证成功，我们使用 paramiko 的 SFTPClient 启动一个 FTP 连接。我们将获得`ftp_connection`对象，用它我们可以在 FTP 服务器上执行远程文件操作。

# **步骤 3:将文件从 FTP 传输到 S3**

这将是一个很大的功能，将为你做实际的转移。我们将分解代码片段来理解这里实际发生了什么。

## 首先要做的事情——连接到 FTP 和 S3

初始 ftp 和 s3 连接设置

该函数接受一组参数，其中大部分是不言自明的。`ftp_file_path`是从 FTP 服务器的根目录到文件的路径，带有文件名。例如，`folder1/folder2/file.txt`。类似地，`s3_file_path`是从 S3 存储桶的根开始的路径，包括文件名。该程序从 FTP 路径读取文件，并将同一文件复制到给定 s3 路径的 S3 存储桶。

我们还将从 FTP 中读取文件大小。根据文件的大小，我们将决定方法——通过提供`chunk_size`(也称为多部分上传)是传输完整的文件还是分块传输。

## 避免重复拷贝

这个小的 try-catch 块将提供的 s3 文件名与相同的路径进行比较。它还会检查文件的大小。如果匹配，我们将中止传输，从而关闭 FTP 连接并从函数返回。

## 一次性传输小文件

立即传输文件

> 注意:由于无法将 FTP 文件数据转换为字节，导致 python 抛出 IOError，以上脚本中断。GitHub repo 中已经修复了这个问题，您可以在这里参考[。](https://github.com/kirankumbhar/File-Transfer-FTP-to-S3-Python/blob/master/ftp_to_s3.py)

如果文件小于我们提供的块大小，那么我们使用`read()`方法读取完整的文件。这将以字节为单位返回文件数据。然后，我们使用`upload_fileobj()`函数，使用给定的路径和文件名，将这个字节数据直接上传到 s3 bucket。

## 成批传输大文件，也称为多部分上传

分块传输文件

我们将分块传输文件！这是真正的乐趣开始的地方…

首先，我们根据文件大小计算需要传输的块的数量。记住，AWS 不允许任何块大小小于 5MB，除了最后一部分。最后一部分可以小于 5MB。

我们对所有数据块进行 for 循环迭代，从 FTP 读取数据块，并上传到 S3。我们使用 boto3 库提供的多部分上传工具。`create_multipart_upload()`将启动流程。块传输将由` transfer_chunk_from_ftp_to_s3()`函数执行，该函数将返回包含有关名为`parts`的上传部分的信息的 python 字典。

python 字典`parts_info`有键`‘Parts’`，值是 python 字典`parts`的列表。这个`parts_info`字典将被`complete_multipart_upload()`用来完成转账。它还从启动多部分上传后返回的多部分字典中获取上传 id。完成多部分上传后，我们关闭 FTP 连接。

## 如何转移组块？

该函数将通过将块大小传递给`ftp_file.read()`函数来读取以字节为单位的块大小的 FTP 文件数据。该字节数据将作为`Body`参数传递给`s3_connection.upload_part()`函数。`upload_part()`将带其他参数如 bucket 的名称，s3 文件路径。零件号参数只是表示零件数量的整数，如 1、2、3 等。

一旦上传了部分，我们返回带有`Etag`和`PartNumber`的部分输出 dict，然后将它作为值传递给名为`part_info`的 dict 以完成多部分上传。

## 我们做到了！

就是这样！您已经成功地将文件从 FTP 传输到 s3，现在您应该会在控制台上看到该消息。

访问 [Github 链接](https://github.com/kirankumbhar/File-Transfer-FTP-to-S3-Python/blob/master/ftp_to_s3.py)获取完整的 python 脚本。谢谢你读到这里。我希望这篇文章对你有所帮助。干杯！