# 如何用 Python 下载流响应文件

> 原文：<https://betterprogramming.pub/how-to-download-streaming-responses-as-a-file-in-python-51b52943f4e7>

## 将文件直接传输到磁盘

![](img/ce0b809dd5d12740763dd662aae33114.png)

亚历山大·沙托夫在 [Unsplash](https://unsplash.com/s/photos/desktop-icon?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片。

大多数情况下，从服务器返回音频或视频文件时，流响应是首选。这主要是因为流响应非常适合大文件，尤其是文件大小超过 1GB 的文件。

在本教程中，您将学习:

*   创建一个简单的 FastAPI 服务器，通过`StreamingResponse`返回一个音频文件。
*   创建一个简单的 Python 脚本来调用 FastAPI 服务器，并将响应作为文件直接保存到磁盘。

让我们继续下一部分，开始安装必要的模块。

# 设置

强烈建议您在继续安装之前创建一个虚拟环境。

## FastAPI

激活虚拟环境并运行以下命令来安装 FastAPI:

```
pip install fastapi
```

## 紫玉米

您将需要一个 ASGI 服务器来服务 FastAPI。推荐的选择是 Uvicorn。您可以选择通过以下方式安装标准版:

```
pip install uvicorn[standard]
```

或最小版本，如下所示:

```
pip install uvicorn
```

## 要求

为了保持简洁，将使用`requests`包来调用 FastAPI 服务器。请注意，`requests`是基于阻塞 I/O 的。因此，它将阻塞内容属性，直到下载完整个响应。如果您正在寻找非阻塞 I/O 包，可以考虑使用`asyncio`来代替。运行以下命令进行安装:

```
pip install requests
```

# 履行

在本节中，您将实现:

*   FastAPI 服务器。
*   一个 Python 脚本。

## FastAPI 服务器

创建一个名为`server.py`的新 Python 文件，并在其中添加以下代码:

将任何音频/视频文件放在与`server.py`相同的目录中。如果您使用不同的媒体文件，请记住相应地更改文件名和`media_type`。对于 MP4 等视频文件，将`media_type`设置为`video/mp4`。

按如下方式运行服务器:

```
uvicorn server:app
```

默认情况下，它将使用端口 8000。您可以通过`port`参数设置您自己的自定义端口，如下所示:

```
uvicorn server:app --port 8005
```

当从同一局域网内的另一台机器调用时，您还应该设置主机:

```
uvicorn server:app --host 0.0.0.0 --port 8005
```

## Python 脚本

接下来，创建另一个名为`download.py`的 Python 文件，并在文件顶部添加以下导入声明:

```
import requests
import shutil
import uuid
```

当`requests`模块用于调用 FastAPI 服务器时，`shutil`模块主要用于本地保存文件。它是 Python 标准包的一部分，提供了许多对文件的高级操作。

另一方面，`uuid`用于生成唯一的文件名。在 Python 文件中追加以下函数:

```
def generate_audio_filename():
    return f"{uuid.uuid4()}.wav"
```

让我们创建另一个函数，该函数调用 FastAPI 服务器并将流数据作为文件下载到本地磁盘中:

调用`requests`模块时，需要将`stream`参数设置为`True`:

```
stream=True
```

除此之外，您应该使用模式`wb`打开文件作为二进制文件。`shutil.copyfileobj`将复制相应的数据并将其作为文件保存在您的机器中。

在函数的正下方添加以下代码，为您的脚本添加最后的润色:

```
url = "http://localhost:8000/wav"
download_file(url)
```

如果您使用不同的配置，请相应地修改 URL。您可以在以下[要点](https://gist.github.com/wfng92/a9e616ae6adecab4327eeae2a1f57f65)中找到完整的脚本:

通过以下命令运行脚本:

```
python download.py
```

您应该会在项目所在的目录中看到一个新的 wav 文件。在这种情况下，我得到了以下内容:

```
0009956a-79dc-46c2-9716-3d06f755897b.wav
```

# 结论

让我们回顾一下你今天所学的内容。

本教程从简单的`requests`、`fastapi`和`uvicorn`包安装步骤开始。

接下来，它关注于实现一个通过`StreamingResponse`返回音频文件的 FastAPI。

然后介绍了如何使用`uuid`模块生成唯一的文件名，并通过`shutil`模块将流数据保存到本地磁盘。

感谢阅读！请随意查看我的其他文章。祝您愉快！

# 参考

1.  [请求—高级用法](https://requests.readthedocs.io/en/latest/user/advanced/)
2.  [FastAPI —自定义响应](https://fastapi.tiangolo.com/advanced/custom-response/)
3.  [stack overflow——用 Python 下载大文件并请求](https://stackoverflow.com/questions/16694907/download-large-file-in-python-with-requests)