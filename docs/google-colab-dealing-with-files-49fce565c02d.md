# 如何在 Google Colab 中管理文件

> 原文：<https://betterprogramming.pub/google-colab-dealing-with-files-49fce565c02d>

## 使用 Google Colab 从任何地方访问文件的权威指南

![](img/f1d508b4fcd3998c067f6152ec03b2c1.png)

安德鲁·庞斯在 [Unsplash](https://unsplash.com/s/photos/files?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

*被困在付费墙后面？点击* [}的形式。](/google-colab-dealing-with-files-49fce565c02d########)

*   [然后在 Colab 中运行以下命令:](/google-colab-dealing-with-files-49fce565c02d########)

```
! mkdir ~/.kaggle #create the .kaggle folder in your root directory
! echo '<PASTE_CONTENTS_OF_KAGGLE_API_JSON>' > ~/.kaggle/kaggle.json #write kaggle API credentials to kaggle.json
! chmod 600 ~/.kaggle/kaggle.json  # set permissions
! pip install kaggle #install the kaggle library
```

[4.一旦在 Colab 中创建了`kaggle.json`文件并且安装了 Kaggle 库，您就可以使用以下命令搜索数据集:](/google-colab-dealing-with-files-49fce565c02d########)

```
! kaggle datasets list -s {KEYWORD}
```

[5.然后使用以下方式下载数据集:](/google-colab-dealing-with-files-49fce565c02d########)

```
! kaggle datasets download -d {DATASET NAME} -p /content/kaggle/
```

[数据集将被下载，并在指定的路径中可用(本例中为`/content/kaggle/`)。](/google-colab-dealing-with-files-49fce565c02d########)

[](/google-colab-dealing-with-files-49fce565c02d########)

# [从 Google Colab 访问 MySQL 数据库](/google-colab-dealing-with-files-49fce565c02d########)

[1.您需要导入预先安装的 sqlalchemy 库来使用关系数据库。](/google-colab-dealing-with-files-49fce565c02d########)

```
import sqlalchemy
```

[2.输入连接详细信息并创建引擎:](/google-colab-dealing-with-files-49fce565c02d########)

```
HOSTNAME = 'ENTER_HOSTNAME'
USER = 'ENTER_USERNAME'
PASSWORD = 'ENTER_PASSWORD'
DATABASE = 'ENTER_DATABASE_NAME'

connection_string = f'mysql+pymysql://{MYSQL_USER}:{MYSQL_PASSWORD}@{MYSQL_HOSTNAME}/{MYSQL_DATABASE}'

engine = sqlalchemy.create_engine(connection_string)
```

[3.最后，只需创建 SQL 查询，并使用`pd.read_sql_query()`将查询结果加载到数据框中:](/google-colab-dealing-with-files-49fce565c02d########)

```
query = f"SELECT * FROM {DATABASE}.{TABLE}"

import pandas as pd
df = pd.read_sql_query(query, engine)
```

[](/google-colab-dealing-with-files-49fce565c02d########)

# [Google Colab 在处理文件时的局限性](/google-colab-dealing-with-files-49fce565c02d########)

[使用 Colab 时要记住的一个重要警告是，你上传到它的文件不会永远可用。Colab 是一个临时环境，空闲超时为 90 分钟，绝对超时为 12 小时。这意味着，如果运行时已经空闲了 90 分钟或者已经使用了 12 个小时，它将断开连接。断开连接时，您会丢失所有的变量、状态、已安装的软件包和文件，并在重新连接时连接到一个全新的干净的环境。](/google-colab-dealing-with-files-49fce565c02d########)

[此外，Colab 的磁盘空间限制为 108 GB，其中只有 77 GB 可供用户使用。虽然这对于大多数任务来说应该足够了，但在处理更大的数据集(如图像或视频数据)时，请记住这一点。](/google-colab-dealing-with-files-49fce565c02d########)

[](/google-colab-dealing-with-files-49fce565c02d########)

# [结论](/google-colab-dealing-with-files-49fce565c02d########)

[对于那些希望利用高端计算资源(如 GPU)而不受价格限制的个人来说，Google Colab 是一个很好的工具。](/google-colab-dealing-with-files-49fce565c02d########)

[在本文中，我们已经介绍了通过在 Google Colab 中读取外部文件或数据以及从 Google Colab 向这些外部数据源写入数据来增强 Google Colab 体验的大多数方法。](/google-colab-dealing-with-files-49fce565c02d########)

[根据您的用例或者您的数据架构是如何设置的，您可以很容易地应用上述方法将您的数据源直接连接到 Colab 并开始编码。](/google-colab-dealing-with-files-49fce565c02d########)

[](/google-colab-dealing-with-files-49fce565c02d########)

# [其他资源](/google-colab-dealing-with-files-49fce565c02d########)

*   [Google Colab 入门|如何使用 Google Colab](https://www.youtube.com/watch?v=i-HnvsehuSw)
*   [外部数据:本地文件、驱动器、表单和云存储](https://colab.research.google.com/notebooks/io.ipynb)
*   [将数据导入 Google Colab——干净利落的方式](https://towardsdatascience.com/importing-data-to-google-colab-the-clean-way-5ceef9e9e3c8)
*   [入门:通过 A. Apte 将 CSV 文件加载到 Colab](https://towardsdatascience.com/3-ways-to-load-csv-files-into-colab-7c14fcbdcb92) 的 3 种方法
*   陆嘉荣[通过 Google Colab](https://towardsdatascience.com/downloading-datasets-into-google-drive-via-google-colab-bcb1b30b0166) 将数据集下载到 Google Drive

本博客最初发表于 [neptune.ai](https://medium.com/u/a7a4a9e70d82?source=post_page-----49fce565c02d--------------------------------) :

[](https://neptune.ai/blog/google-colab-dealing-with-files) [## 如何在 Google Colab 中处理文件:你需要知道的一切- neptune.ai

### 谷歌合作实验室是一个免费的 Jupyter 笔记本环境，运行在谷歌的云服务器上，让用户…

海王星. ai](https://neptune.ai/blog/google-colab-dealing-with-files) 

Medium 仍然不支持向印度以外的作者支付费用。如果你喜欢我的内容，你可以给我买杯咖啡:)

[](https://www.buymeacoffee.com/siddhantsadangi) [## Siddhant Sadangi 正在 Streamlit 上创建 python 网络应用程序

### 嘿👋我刚刚在这里创建了一个页面。你现在可以给我买杯咖啡了！

www.buymeacoffee.com](https://www.buymeacoffee.com/siddhantsadangi)